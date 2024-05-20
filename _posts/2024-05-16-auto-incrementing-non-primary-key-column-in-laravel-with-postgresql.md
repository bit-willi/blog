---
title: Auto-Incrementing Non-Primary Key Column in Laravel with PostgreSQL
published: true
---


# Solving the Auto-Incrementing Non-Primary Key Column Problem in Laravel with PostgreSQL


Recently, while working on a Laravel project using PostgreSQL, I faced a challenge. I needed a column that auto-increments but isn't the primary key. The column should start at 1 by default and increment sequentially (2, 3, 4, etc.) unless the user specifies a number. If the user specifies a number like 1000, the next value should be 1001.

Using Laravel's autoIncrement() method alone didn't work because it forces the column to be a primary key. Although there is a [pull request](https://github.com/laravel/framework/pull/50155) that aims to address this issue, it hasn't been merged yet, and will not be.

After some research, I found a solution that worked perfectly. Below is the step-by-step implementation:

```php
<?php

return new class extends Migration
{
    public function up(): void
    {
        //Drop stuffs before migration. Without this i was getting an error when recreating database for tests porposes.
        DB::statement('DROP TRIGGER IF EXISTS random_data_trigger ON random_data;');
        DB::statement('DROP FUNCTION IF EXISTS random_data_setval();');
        DB::statement('DROP SEQUENCE IF EXISTS random_data_number_seq;');
        //End drop

        DB::statement('CREATE SEQUENCE random_data_number_seq;');

        Schema::create('random_data', function (Blueprint $table) {
            $table->id();
        $table->integer('number')->default(DB::raw("nextval('random_data_number_seq')"))->nullable()->unique();
            $table->timestamps();
        });

        DB::statement("
            CREATE OR REPLACE FUNCTION random_data_setval() RETURNS TRIGGER AS $$
            BEGIN
            IF NEW.number IS NOT NULL THEN
            PERFORM setval('random_data_number_seq', GREATEST(NEW.number, nextval('random_data_number_seq') - 1));
            END IF;
            RETURN NEW;
            END;
            $$ LANGUAGE plpgsql;
            ");

        DB::statement("
            CREATE TRIGGER random_data_trigger
            BEFORE INSERT ON random_data
            FOR EACH ROW
            EXECUTE FUNCTION random_data_setval();
            ");
    }

    public function down(): void
    {
        DB::statement('DROP TRIGGER IF EXISTS random_data_trigger ON random_data;');
        DB::statement('DROP FUNCTION IF EXISTS random_data_setval();');
        DB::statement('DROP SEQUENCE IF EXISTS random_data_number_seq;');

        Schema::dropIfExists('random_data');
    }
};
```


## Explanation

1. Dropping Existing Database Objects: Before creating the sequence, trigger, and function, we drop any existing ones to avoid conflicts during database recreation for testing purposes.
2. Creating a Sequence: We create a PostgreSQL sequence named random_data_number_seq that will be used to generate the auto-incrementing values.
3. Creating the Table: The random_data table includes an id column (primary key) and a number column that uses the sequence for its default value. The number column is also unique and nullable.
4. Creating the Function: The random_data_setval function adjusts the sequence value. If a new row has a specified number, it sets the sequence to the maximum of this number and the current sequence value minus one.
5. Creating the Trigger: The random_data_trigger ensures that the function is called before inserting a new row, applying the logic defined in the function.

This solution ensures that the number column auto-increments as expected and allows for user-specified values that influence the sequence.


## Conclusion


Using PostgreSQL sequences and triggers in Laravel, you can achieve an auto-incrementing column that isn't a primary key. This workaround is necessary until some new pull request in Laravel is merged to fix the issue.

I hope this post helps anyone facing similar challenges with auto-incrementing non-primary key columns in Laravel with PostgreSQL. Feel free to reach out with any questions or further improvements!
