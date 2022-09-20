## WordPress Playbooks

In this folder you will find the playbooks for the installation and management of WordPress. This document will describe the use cases for each playbook and the particulars for running them.

---

## Major Playbooks

The playbooks listed below are the primary playbooks you will likely need to run. They should cover the core functions for creating a new WordPress server, backing up those servers, and then restoring or copying those servers. 

### wp_new_server.yml

This playbook is for creating new WordPress websites from a blank DigitalOcean droplet. The playbook does reference the 'wordpress.yml' variables file located in /playbooks/secrets/wordpress.yml, but you should not have to update the variables for each installation unless you want to create new MySQL users or passwords. This playbook is **OK** to run on multiple targets at a time.

### wp_clone.yml

This playbook is for copying one WordPress website to another server. The playbook will reference the wp_clone.yml variable file located in the 'secrets' folder. You will need to update all variables in that file prior to running this playbook. This playbook is **NOT OK** to run on multiple targets at a time and must be run with the '--limit' tag.

### wp_backup.yml

This playbook is for creating backups of our WordPress websites. The playbook will copy a website's MySQL database and wp-content folder and then put them in a compressed folder stored by date in the /var/wp_backups/.. folder. With the database file and wp-content folder you should be able to restore any WordPress website onto a 'blank' WordPress installation, this process can be completed by running the wp_clone.yml playbook. This playbook is **OK** to run on multiple targets at a time, and is also ok to run on non-wordpress websites (it just won't copy anything from non-wordpress websites).

---

## Minor Playbooks

These are the playbooks that make up the 'Major' playbooks. These playbooks can be run by themselves to complete their specific task. 

### wp_url.yml

This playbook is for updating the URL for a WordPress website in the MySQL database. You would use this playbook when you are updating a website from http:// to https:// or moving a website from dev.casat.org to casat.org. Before running this playbook you will need to update the relevant variables within the wp_clone.yml file, inside the secrets folder.

### wp_install.yml

This playbook is for installing WordPress on a 'blank' Ubuntu linux droplet. The playbook will install Apache, PHP and any necessary extensions. It then installs MySQL using the 'geerlingguy.mysql' ansible role. Once that is completed it creates a MySQL database and the necessary users WordPress needs to interact with it. Finally it downloads WordPress copies it to the correct directory, configures the wp-config.php file, and updates permissions.

- This playbook runs as part of the wp_new_server.yml playbook

- This playbook does require the geerlingguy.mysql ansible role, its should be already installed. But you can find more information here if you have trouble:

    - [Link to GeerlingGuy MySQL Ansible Galaxy page](https://galaxy.ansible.com/geerlingguy/mysql)

    - [Link to GeerlingGuy MySQL role Github page and documentation](https://github.com/geerlingguy/ansible-role-mysql)

### wp_ssl_get.yml
### wp_ssl_on.yml
### wp_ssl_off.yml
### wp_restore.yml
### wp_password.yml
