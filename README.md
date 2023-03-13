# vagrant-infra

Vagrant file that generates 4 nodes to use with Ansible in order to install the requierements for a dev environment.

## Preparation

### Mount the vagrant VM's

`vagrant up`

Then connect to MasterNode via SSH

`vagrant ssh MasterNode`

### Prepare the VM for Ansible

Install Ansible

`sudo apt update`

`sudo apt install ansible`

Verify that Ansible is installed

`ansible --version`

Edit hosts in Ansible in order to add other VM's into the MasterNode

`sudo vim /etc/ansible/hosts`

File should look like this : 


> [all]
> 
> vagrant@172.16.1.51
> 
> vagrant@172.16.1.52
> 
> vagrant@172.16.1.53
> 
> vagrant@172.16.1.54
> 
> 
> [db]
> 
> vagrant@172.16.1.51
> 
> 
> [nginx]
> 
> vagrant@172.16.1.52
> 
> 
> [apache]
> 
> vagrant@172.16.1.53
> 
> 
> [docker]
> 
> vagrant@172.16.1.54
> 
>

### Mount a shared folder with VirtualBox

`sudo mount -t vboxsf shared /home/vagrant/shared_folder`

This is useful for working in VSCode on Windows and easily share the files with the MasterNode VM.

### Create SSH key on MasterNode

We need a SSH key pair in order to make connection with the other VM's

To do so, we will first go to the SSH folder in the MasterNode :

`cd ~/.ssh`

Then we generate a key pair

`ssh-keygen`

Copy the code in the **.pub** file corresponding to the newly generated key.

### Establish SSH permission with the other VM's

Connect to the other machines with `vagrant ssh *name of the node*` then edit *authorized_keys* file in *~/.ssh* folder.

To do so you can use this command : 

`sudo vim authorized_keys`

Then paste the **.pub** you copied earlier.

Repeat for all the VM's except the MasterNode.

### Connect the MasterNode with the other nodes via SSH

Test the connection between the MasterNode and the other nodes by using a command like this in the MasterNode : 

`ssh -i id_rsa vagrant@172.16.1.51`

"id_rsa" is the name of your SSH key.

"vagrant@172.16.1.51" is the username@ip_address of the VM you're connecting to.

### Test ping with Ansible

In order to test the connection between the MasterNode and the machines with Ansible, we run this command :

  `ansible -m ping all`
  
  If the ping is successful, we can move on to the next part.

# Ansible

### Create base Playbook

Create a .yaml file in the shared directory of the MasterNode.

It will be the base of what's comming next.

You can find the file in the `shared` folder. (`base_playbook.yaml`)

Now we can already test the playbook by going in the base_playbook.yaml folder and run this command : 

`ansible-playbook base_playbook.yaml`

The next step will be to use Ansible's roles in order to organize the playbook better. It will make it so there is less redondance.

## Roles

### Purpose

In this part, we will separate the playbook into different roles.

Here we'll create the "role" folder in the shared directory to make it easier for us.

### Our first role

Let's create our first role, that will be used by all the VM's

`ansible-galaxy init apt-update`

It will do the apt update command on every machine before installing anything else.

This command creates an "apt-udpate" folder in which we will make the role.

The only change that we'll make here is in the main.yml file located at apt-update/tasks/main.yml.

We'll add these lines taken from our base_playbook.yaml

> - name: apt update
>
>   shell: apt update
>

note: if you're doing it on VSCode, there may be some indentation issues when you copy/paste, so make sure everything is correct for this.

### MYSQL role

Let's create the mysql installer role.

`ansible-galaxy init mysql`

We'll copy the "tasks" section from our base_playbook to mysql/tasks/main.yml.

As we have a "vars" section for the MYSQL installation part, we'll copy this section into mysql/vars/main.yml aswell.

So let's add these lines :

> new_mysql_root_password: redacted
> 
> mysqlsoftware:
> 
>   - mysql-server
> 
>   - mysql-client

We're done with the MYSQL installer role.

### Nginx role

Let's create the Nginx installer role. It's the same process as previously.

`ansible-galaxy init nginx`

Copy the tasks into nginx/tasks/main.yml.

note: don't copy the "apt update" part as this will be done by the apt-udpate role.

This time, we have handlers, so we'll copy them into nginx/handlers/main.yml.

The file will look like this : 

> - name: "restart nginx"
> 
>   service:
> 
>     name: nginx
> 
>     state: restarted

And we're done with this role.

### Apache role

We'll do the process once more.

`ansible-galaxy init apache`

Copy the "tasks" section into apache/tasks/main.yml and the "handlers" section into apache/handlers/main.yml.


### Docker role

Once again.

`ansible-galaxy init docker`

We only have tasks here, so we'll just copy the "tasks" section into docker/tasks/main.yml.

### Create the Playbook that will play the roles

To make it work, we'll just copy the base of every play from the base_playbook, but instead of copying all the tasks, vars, handlers, etc... We'll just type which role we want it to play.

Example : 

> # Install MYSQL Database on Database node
> 
> - name: Install MYSQL
> 
>   hosts: db
> 
>   become: yes
> 
>   roles:
> 
>     - role: /home/vagrant/shared_folder/roles/apt-update
> 
>     - role: /home/vagrant/shared_folder/roles/mysql
> 

You can see the whole file at shared/roles/roles_playbook.yaml.

### Test

Let's test the playbook and see if it works.

`ansible-playbook roles_playbook.yaml`