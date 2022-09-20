# Ansible Playbooks Repository

Ansible automates processes by operating through a series of tasks defined in Playbooks. This repository is a collection of playbooks for Ansible used to manage Digital Ocean droplets.

If you need more information about Ansible you can check the [official documentation](https://docs.ansible.com/ansible-core/devel/index.html) or the [unofficial reddit](https://old.reddit.com/r/ansible/).

## Important information

All of our Ansible playbooks are saved in this directory and any referenced variables that are not defined within the playbooks themselves will be saved in /etc/ansible/playbooks/secrets on our Ansible server. If you wish to test edits/changes or create new playbooks you can start them out in the /etc/ansible/testbooks folder on the Ansible server. When you are ready to make changes to the live playbooks, you should make the changes to these github files and then download your changes to the Ansible server using git.

To download changes to these playbooks from Github, and disregard any edits made on the Ansible server itself, you should use the following commands:

```
sudo git fetch --all
sudo git reset --hard origin/main
```

## Running Ansible commands

Ansible commands have a few parts to them; It starts with defining whether or not it's a single command or a playbook, followed by defining which 'host' file to use, then specifying which playbook (if it is a playbook), and then specifying which particular server (if you only want to run the command on a single host). In practice this looks something like this:  

```
ansible-playbook -i production.yml playbooks/users/users.yml --limit resettech.net
```
*This will run the playbook 'users.yml' on the list of production servers, but only on resettech.net.*

**ansible-playbook** - Tells the system we are going to run an Ansible playbook  
**-i production** - Tells Ansible to reference the 'production' host file/list  
 **playbooks/users/users.yml** - This is the location of the playbook we want run  
 **--limit resettech.net** - Tells Ansible to only run the playbook on casat.org and no others
 
 ---

It is also possible to use Ansible to run ad-hoc commands (a single command, not a whole playbook) on many (or just one) hosts. Those commands look something like this:  

```
ansible -i dev -a "/sbin/reboot" --limit dev.resettech.net
```
*This command is to restart the dev.resettech.net server only*

**ansible** - Tells the system we are going to run an Ansible ad-hoc command (not a playbook)  
**-i dev** - Tells Ansible to reference the 'dev' host file/list  
**-a "/sbin/reboot"** - -a tells Ansible that the command is a shell (Ubuntu) command, "/sbin/reboot" is a linux command to restart the server.  
**--limit dev.resettech.net** - Tells Ansible to run the command on this server only  

-OR-

```
ansible -i production -m ping
```
*This command will ping all the servers in the production list*

**ansible** - Tells the system we are going to run an Ansible ad-hoc command (not a playbook)  
**-i production** - Tells Ansible to reference which host file to reference  
**-m ping** - -m tells Ansible that the command is an Ansible function, ping is a command to check connectivity

### How to run a playbook on localhost

## Managing Ansible

### Creating new playbooks

#### General guidelines

#### Use Github

### Editing the hosts files

For our purposes we have split up the Ansible hosts file into 3 separate files; *dev, staging, and production*. This is somewhat non-standard but is done to prevent playbooks from accidently being run in production. Because of this when running any Ansible command, the host file must be specified by using the ***-i*** flag, ie:
```
ansible-playbook -i staging ...
```

The 3 host files are named based on their roles, and droplets should be added to the appropriate group depending on its status.

The 'dev' hosts file is where newly created droplets should first be added after initial creation and prior to any content being added. The dev group is where Droplets should live through setup and then be moved into either the 'staging' or 'production' hosts.

The 'staging' host file is for Droplets that are out of the development stage and getting content added to them. These servers should be getting about as much attention as our production servers at this point, just updates and backups. Droplets should remain here until they are ready to go live and then they should be added to the 'production' list.

The 'production' host file is for our Droplets that are live websites or applications. The host file is made up of several different groups and when adding a new droplet to the list, it should be added under it's respective group and not the 'production' list directly. The different groups in the production file are representative of the organization in Digital Ocean, so any droplets added to the production list should match their Digital Ocean location.