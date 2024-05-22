---
title: Playing arround with text diff in PHP
published: true
---


# Playing arround with text diff in PHP

Sometimes, while working on PHP projects, you might need to get the difference between two strings. Instead of using complex algorithms or libraries, you can leverage the `diff` Linux binary to achieve this. In this post, I'll show you how to use `diff` in PHP by writing a wrapper function.


## The Solution

Here’s the complete PHP code to get the diff of two strings using the `diff` command:

```php
<?php

declare(strict_types=1);

/**
 * generate a random string
 */
function random_string_generator(int $length, string $keyspace = 'abcdefghijklmnopqrstuvwxyzqwertyuioplkjhgfdsazxcvbnm0123456789_'): string
{
    $str = '';
    $max = strlen($keyspace) - 1;

    for ($i = 0; $i < $length; ++$i) {
        $str .= $keyspace[rand(0, $max)];
    }

    return $str;
}

/**
 * get the diff of two strings
 */
function diff(string $a, string $b): string
{
    do {
        $a_path = "/tmp/" . random_string_generator(24);
    } while (file_exists($a_path));

    do {
        $b_path = "/tmp/" . random_string_generator(24);
    } while (file_exists($b_path));

    file_put_contents($a_path, $a);
    file_put_contents($b_path, $b);

    $ret = shell_exec("diff $a_path $b_path");

    if (is_null($ret)) {
        echo "cannot get the diff of the string.";
        return "";
    }

    unlink($a_path);
    unlink($b_path);

    return $ret;
}

/**
 * ensure that the diff command exists
 */
function ensure_diff_exists(): void
{
    $ret = shell_exec("command -v diff 2> /dev/null");

    if (is_null($ret)) {
        echo "diff is not installed. please install it.";
        exit(1);
    }
}
```


## Explanation

Random String Generator: The random_str function generates a random string of a given length from a specified keyspace. This is used to create unique temporary file names.

Diff Function: The diff function takes two strings, saves them to temporary files, runs the diff command on these files, and returns the output. Temporary files are deleted after the diff is generated.

Ensure Diff Exists: The ensure_diff_exists function checks if the diff command is available on the system. If not, it prints an error message and exits.


## Example Usage

Here’s an example of how you can use the diff function:

```php
<?php

$a = <<<EOL
hello world\n
just a test\n
another line.\n
EOL;

$b = <<<EOL
hello world!\n
just a test with string\n
another line.\n
new line.\n
EOL;

ensure_diff_exists();
echo diff($a,$b);

/**
 * Output:
 *
 * 1c1
 * < Hello world
 * ---
 * > Hello world!
 * 3c3
 * < Just a Test
 * ---
 * > Just a Test with string
 * 5a6,7
 * >
 * > New line.
 */
```


## Conclusion

Using the diff Linux binary in PHP is a straightforward and efficient way to find differences between two strings. This method leverages the power of existing command-line tools, making it both simple and powerful. Feel free to use and modify this wrapper function to fit your needs!
