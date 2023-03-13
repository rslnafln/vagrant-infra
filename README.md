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

### Connect the MasterNode with the other nodes via SSH

### Test ping with Ansible

## Ansible

### Create base Playbook

### Roles

### Play the Playbook

### Test
