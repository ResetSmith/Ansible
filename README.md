# Ansible Playbooks Repository

Ansible automates processes by operating through a series of tasks defined in Playbooks. This repository is a collection of playbooks for Ansible that CASAT is using to manage it's Digital Ocean droplets.

If you need more information about Ansible you can check the [official documentation](https://docs.ansible.com/ansible-core/devel/index.html) or the [unofficial reddit](https://old.reddit.com/r/ansible/).

## How to start

For our purposes there is a Digital Ocean droplet that is setup to run Ansible tasks across all of our other droplets. The server is bastion.casat.org and it can be found in our Digital Ocean account within the AIC project. This server was created after testing from local machines on the campus would get connectivity issues when running longer playbooks. A typical workflow for Ansible would look like this; Create or update Ansible playbooks on your local device, then upload the playbook into Github. Then connect to the bastion server, navigate to the Ansible playbook folder and pull your updates from Github. After that you are ready to run commands.

### Connecting to bastion.casat.org

The bastion.casat.org server is only open to connections via SSH. To add an entirely new user to the server, a user needs to be created and their public key uploaded into their respective /.ssh folder. Once that is complete the user can SSH into the server and then navigate to the Ansible folder, /etc/ansible and then from there can run commands.

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
ansible-playbook -i production.yml playbooks/users/users.yml --limit casat.org
```
*This will run the playbook 'users.yml' on the list of production servers, but only on casat.org.*

**ansible-playbook** - Tells the system we are going to run an Ansible playbook  
**-i production** - Tells Ansible to reference the 'production' host file/list  
 **playbooks/users/users.yml** - This is the location of the playbook we want run  
 **--limit casat.org** - Tells Ansible to only run the playbook on casat.org and no others
 
 ---

It is also possible to use Ansible to run ad-hoc commands (a single command, not a whole playbook) on many (or just one) hosts. Those commands look something like this:  

```
ansible -i dev -a "/sbin/reboot" --limit behavioralhealthnv.org
```
*This command is to restart the behavioralhealthnv server only*

**ansible** - Tells the system we are going to run an Ansible ad-hoc command (not a playbook)  
**-i dev** - Tells Ansible to reference the 'dev' host file/list  
**-a "/sbin/reboot"** - -a tells Ansible that the command is a shell (Ubuntu) command, "/sbin/reboot" is a linux command to restart the server.  
**--limit behavioralhealthnv.org** - Tells Ansible to run the command on this server only  

-OR-

```
ansible -i production -m ping
```
*This command will ping all the servers in the production list*

**ansible** - Tells the system we are going to run an Ansible ad-hoc command (not a playbook)  
**-i production** - Tells Ansible to reference which host file to reference  
**-m ping** - -m tells Ansible that the command is an Ansible function, ping is a command to check connectivity

### How to run a playbook on localhost (bastion.casat.org)

## Managing Ansible

### Creating new playbooks

#### General guidelines

#### Use Github

### Editing the hosts files

For our purposes we have split up the Ansible hosts file into 3 separate files; *dev, staging, and production*. This is somewhat non-standard but is done to prevent playbooks from accidently being run in production. Because of this when running any Ansible command, the host file must be specified by using the ***-i*** flag, ie:
```
ansible-playbook -i staging ...
```

The 3 host files are named based on their roles, and droplets should be added to the appropriate group depending on its status. The 'dev' hosts file is made up of 2 groups, 'new' and 'dev'. The new group is for freshly acquired Digital Ocean Droplets and utilizes the root account for running Ansible actions. Droplets in the new group should have their initial setup done, and then be moved into the 'dev' group for further work. When a server is ready to have another team start creating content on it (Media or whomever) it should be removed from the 'dev' group and host file and then be added to the 'staging' host file.

The 'staging' host file is for Droplets that are out of the development stage and getting content added to them. These servers should be getting about as much attention as our production servers at this point, just updates and backups. Droplets should remain here until they are ready to go live and then they should be added to the 'production' list.

The 'production' host file is for our Droplets that are live websites or applications. The host file is made up of several different groups and when adding a new droplet to the list, it should be added under it's respective group and not the 'production' list directly. The different groups in the production file are representative of the organization in Digital Ocean, so any droplets added to the production list should match their Digital Ocean location.

####
