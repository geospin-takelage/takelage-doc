# takelage-doc: gopass

[Table of contents](../../README.md)

## Gopass Password Manager

[gopass](https://www.gopass.pw/) is a rewrite of 
[pass](https://www.passwordstore.org/) in 
[go](https://golang.org/) with additional features. 
gopass uses the same 
[gpg](https://www.gnupg.org/) encrypted text files as pass 
so third-party apps like the ansible 
[passwordstore](https://docs.ansible.com/ansible/latest/plugins/lookup/passwordstore.html)
lookup plugin works. Unlike pass, gopass uses sub-stores
which can be mounted in the password tree with different
[git](https://git-scm.com) origins.

## Versions

gopass is a moving target.
The API changed a lot prior to version 1.10.
This documentation uses gopass 1.10.1.

## Definitions

Let `git@example.com:my_project/pass.git`
be the git origin of our password store.
Let `development/my_project` be the mount path of
the password store in the gopass password hierarchy.
Let `/home/myuser/.local/share/gopass/stores/projects-my_project`
be the file system path of your password store.
You can examine the configuration by running `gopass config`.

## Clone a Store

gopass password stores should only be cloned 
on your host system and not in a takelage container.
You have to restart takelage container after you
have cloned a new store so that it becomes available
in the container.
You will be asked for your name and mail address.
These values should correspond to the ones in your git config.

```bash
gopass clone git@example.com:my_project/pass.git projects/my_project
```

## Create a Store

Be sure to add the `--store` argument or you will
reinitialize your root password store! 

```bash
gopass init --store projects/my_project
```

After cloning, gopass tells you the file system path 
of your new store. Change into that directory and 
add a newly created git repository as origin:

```bash
cd /home/myuser/.local/share/gopass/stores/projects-my_project
git remote add origin git@example.com:my_project/pass.git
```

## Delete a store

Run `gopass config` to get the file system path 
of the store you want to delete.

```bash
gopass mounts umount projects/my_project
rm -fr /home/myuser/.local/share/gopass/stores/projects-my_project
```