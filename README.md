# vagrant-infra

Vagrant file that generates 4 nodes to use with Ansible in order to install the requierements for a dev environment.

## Mount a shared folder with VirtualBox

sudo mount -t vboxsf shared /home/vagrant/shared_folder

This is useful for working in VSCode on Windows and easily share the files with the MasterNode VM.
