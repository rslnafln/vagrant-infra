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

Repeat for all the VM's except de MasterNode.

### Connect the MasterNode with the other nodes via SSH

Test the connection between the MasterNode and the other nodes by using a command like this in the MasterNode : 

`ssh -i id_rsa vagrant@172.16.1.51`

"id_rsa" is the name of your SSH key.

"vagrant@172.16.1.51" is the username@ip_address of the VM you're connecting to.

### Test ping with Ansible

In order to test the connection between the MasterNode and the machines with Ansible, we run this command :

  `ansible -m ping all`
  
  If the ping is successful, we can move on to the next part.

## Ansible

### Create base Playbook

Create a .yaml file in the shared directory of the MasterNode.

It will be the base of what's comming next.

You can find the file in the `shared` folder.

### Roles

### Play the Playbook

### Test
