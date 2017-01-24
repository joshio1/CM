[Virtual Machine](VM.md) | [Ansible](Ansible.md) | [Playbooks](Playbooks.md)

Ansible is a tool for configuring and coordinating software on multiple machines.
In general, Ansible (like Salt, Chef, and Puppet), use a central server that controls other nodes.  Unlike the other tools, Ansible does not require a client service to run on the nodes.

![image](https://cloud.githubusercontent.com/assets/742934/22233647/b26951a4-e1bf-11e6-9bff-0a168a8dc66b.png)

## Setting up Ansible

Follow these steps to install Ansible on your configuration server.

```bash
sudo apt-get update
# Basic dev deps
sudo apt-get -y install git make vim python-dev python-pip libffi-dev libssl-dev libxml2-dev libxslt1-dev libjpeg8-dev zlib1g-dev
# Ansible, setuptools, dopy
pip install ansible==2.2.0
pip install -U pip setuptools>=11.3
pip install dopy==0.3.5
```

## Setting up inventory file and ssh keys

An inventory file is a useful way to manage.

#### Creating an inventory file

An inventory file allows ansible to define, group, and coordinate configuration management of multiple machines. At the most basic level, it basically lists the names of an asset and details about how to connect to it.

Create a `inventory` file that contains something like the following.  **Note use your ip address and private_key**:
    
    node0 ansible_ssh_host=192.168.1.103 ansible_ssh_user=vagrant ansible_ssh_private_key_file=./keys/node0.key


#### Setting up ssh keys

**You will need the following information to help connect to node0.**

* On the host machine, run `vagrant ssh-config` to get path of the private_key (IdentityFile), open it up and copy contents into textfile. In mac os, you can run `pbcopy < path/private_key` to copy contents into clipboard.

#### Creating a private key to slave nodes.

You need a way to automatically connect to your server without having to manually authenicate each connection. Using a public/private key for ssh, you can ssh into your node VM from the Ansible Server automatically.

Create a `keys/node0.key` file that contains the private_key you previously copied.  You may need to `chmod 500 keys/node0.key`.

#### Testing connection

Now, run the ping test again to make sure you can actually talk to the node!

    ansible all -m ping -i inventory -vvvv

#### Performing configuration management
    
Let's install a web server, called nginx (say like engine-X), on the node.

    ansible all -s -m apt -i inventory -a 'pkg=nginx state=installed update_cache=true'
    
Start the web server.
    
    ansible all -s -m shell -i inventory  -a 'nginx'

Open a browser and enter in your node's ip address, e.g. http://192.168.1.103/

Removing nginx.

    ansible all -s -m apt -i inventory -a 'pkg=nginx state=absent update_cache=true'

Actually, nginx is a metapackage, show you also need to run this:

    ansible all -s -m shell -i inventory -a 'sudo apt-get -y autoremove'
    
Webserver should be dead.