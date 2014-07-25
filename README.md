# WordPress Skeleton

This is simply a skeleton repo for a WordPress site. Use it to jump-start your WordPress site repos, or fork it and customize it to your own liking!

## This Fork

The goal of this fork is provide an easy way to set up WordPress Multisite using the WordPress Skeleton directory structure. The following are some potential gotchas:

* Only tested on OS X
* Requires Ansible, Vagrant for local provisioning
* Only tested with the subdomain multisite option
* Certainly not yet secure for live deployment
* Uses Apache
* Vagrant uses NFS for synced folders

The main configuration is located in `provision/group_vars/all.yml`. There you can state your main site URL and any subdomains you have. The provisioner will set up the Apache virtual hosts for you. Additionally, set the WP info so the provisioner can install and configure multisite using WP-CLI.

To get started simply run `vagrant up`. You'll have to edit your local `/etc/hosts` file to point to the VM's private IP, for example:

```shell
192.168.88.88 example.dev sub0.example.dev sub1.example.dev
```

Use the URLs you set in the `all.yml` file. Now you should be able to access the development site at [http://example.dev](http://example.dev).

### WP Config

The configuration files have been set as templates in the WordPress Ansible role `provision/roles/wordpress/templates`. The `local-config.php` template is where you will want to set your development MySQL credentials. This might be something to move to the Ansible group vars as well.

`wp-config` will be edited by WP-CLI &mdash; it will add the generated multisite configuration above the `/* That's all, stop editing! Happy blogging. */` line.

The `.htaccess` file should not require any editing.

## Assumptions

* WordPress as a Git submodule in `/wp/`
* Custom content directory in `/content/` (cleaner, and also because it can't be in `/wp/`)
* `wp-config.php` in the root (because it can't be in `/wp/`)
* All writable directories are symlinked to similarly named locations under `/shared/`.

## Questions & Answers

**Q:** Will you accept pull requests?  
**A:** Maybe — if I think the change is useful. I primarily made this for my own use, and thought people might find it useful. If you want to take it in a different direction and make your own customized skeleton, then just maintain your own fork.

**Q:** Why the `/shared/` symlink stuff for uploads?  
**A:** For local development, create `/shared/` (it is ignored by Git), and have the files live there. For production, have your deploy script (Capistrano is my choice) look for symlinks pointing to `/shared/` and repoint them to some outside-the-repo location (like an NFS shared directory or something). This gives you separation between Git-managed code and uploaded files.

**Q:** What version of WordPress does this track?  
**A:** The latest stable release. Send a pull request if I fall behind.

**Q:** What's the deal with `local-config.php`?  
**A:** It is for local development, which might have different MySQL credentials or do things like enable query saving or debug mode. This file is ignored by Git, so it doesn't accidentally get checked in. If the file does not exist (which it shouldn't, in production), then WordPress will used the DB credentials defined in `wp-config.php`.

**Q:** What is `memcached.php`?  
**A:** This is for people using memcached as an object cache backend. It should be something like: `<?php return array( "server01:11211", "server02:11211" ); ?>`. Programattic generation of this file is recommended.

**Q:** Does this support WordPress in multisite mode?  
**A:** Yes, as of WordPress v3.5 which was released in December, 2012.
