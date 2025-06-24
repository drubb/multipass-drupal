# Drupal Test Instances using Canonical Multipass

## Introduction

[Canonical Multipass](https://canonical.com/multipass) is a lightweight alternative to local
Docker environments like DDEV or Lando. It supports spinning up local Ubuntu VMs that are similar
to cloud environments (e.g. VPS) and are orchestrated using [cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html).
This project shows how to setup a local LAMP VM suitable for Drupal test environments or development.

## Requirements

You'll need Canonical Multipass preinstalled. In Ubuntu, this can be done using Snap:

`sudo snap install multipass`

For other environments, see the [documentation](https://canonical.com/multipass/install).

## Launch a VM prepared for Drupal

* Clone this repository: `git clone https://github.com/drubb/multipass-drupal.git`
* Launch a Drupal VM: `multipass launch --name drupal -m 2G -c 2 -d 5G --cloud-init multipass-drupal/cloud-init.yml noble`
* Enter the shell: `multipass shell drupal`

In this example, you'll get a VM with 2 cores, 2 GB RAM, 5 GB disk and Ubuntu 24.04 aka noble.

See the [documentation](https://documentation.ubuntu.com/multipass/en/latest/) for Multipass commands and options,
for example if you want to mount a folder inside the VM.

The project root is /var/www/html, you can install Drupal as usual using e.g. composer-create-project:
```shell
cd /var/www/html
composer create-project drupal/recommended-project .
composer require drush/drush
drush si
​sudo chown -R www-data:www-data web/sites/default/files/﻿
```
You can also fully automate the installation by adding those lines at the bottom of cloud-init.yml
and providing the required arguments to drush site-install, like this:
`drush si --db-url=mysql://drupal:drupal@localhost:3306/dbname?module=mysql`

Drush is provided using an alias in ~/.bash_aliases

You can also use the web installer instead of Drush. To find the ip address of your instance,
exit the VM (`exit`) and use the command `multipass list` to find the IP. Enter this in your
browser. Example: `http://10.29.51.235`. You should see the Drupal installer.

The default Linux user is "ubuntu", and there's passwordless sudo.

The default credentials for the database are

* Database: drupal
* User: drupal
* Password: drupal

You can change this settings in cloud-init.yml

## Stop or destroy the VM

* Stop (shutdown) the VM: `multipass stop drupal`
* Destroy (delete) the VM: `multipass delete drupal --purge`

## Licence: MIT
