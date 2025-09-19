---
title: Create a deb repository with aptly
published: true
---

# Create a deb repository with aptlya

Let's assume that there is a `debs` folder with all your `.deb` files.

```
apt install aptly nginx
aptly repo create -distribution=trixie -component=main esh-release
aptly repo add esh-release debs
aptly snapshot create esh-{last_version_here} from repo esh-release
aptly publish snapshot esh-{last_version_here}
```

Thats it with aptly, now, let's setup nginx as webserver to provide static content:

```
cat <<EOF > /etc/nginx/sites-available/repo
server {
    listen 80;
    server_name repo.{domain_here};

    root /home/{username_here}/.aptly/public;
    index index.html;

    location / {
        autoindex on;
    }
}
EOF

ls -sf /etc/nginx/sites-available/repo /etc/nginx/sites-enabled/repo

systemctl restart nginx
```

And that's it, if you have gpg to sign your packages you will be prompet the pass for your key, otherwise, will se automatic.

Thanks for reading.
