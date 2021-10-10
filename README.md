# Ansible Umbrel

A little Ansible playbook to update custom configs after an Umbrel update.

## Prerequisites

There are a few prerequisite before we can run the playbook:

1. Install Ansible
2. Clone the it repository to your local machine (in Ansible land this is called the control node)
3. Add your umbrel host to the host.ini file. A default configuration is provide, so update as required
4. Create an Ansible user on the umbrel machine
5. Create an ansible public/private key if you don't have one and copy to the umbrel machine
6. Run the ansible playbook to setup the Ansible user on Umbrel

### Install Ansible

[Ansible](https://www.ansible.com/) is an agentless automation tool that you install on a control node. From the control node, Ansible manages machines and other devices remotely (by default, over the SSH protocol).

Install Ansible using the following guide: [Installation Guide » Installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

### Clone Git Repository

The repository can be either downloaded by a [zip file]() or the git clone command.

```bash
git@github.com:BarneyBuffet/ansible-umbrel.git
```

### Ansible Host

Add your umbrel machine to `./inventory/hosts.ini`. Ansible has trouble finding python, so we tell it in the host.ini file.

```ini
[umbrel]
umbrel.local ansible_ssh_private_key_file=~/.ssh/ansible ansible_user=ansible ansible_python_interpreter=/usr/bin/python3.7
```

### Ansible User

Next we need to create a Ansible user on Umbrel to do all the work.

``` bash
ssh umbrel@umbrel.local
sudo adduser --quiet --shell /bin/bash -ingroup sudo ansible
exit
```

### Ansible SSH Private/Public Key

Generate ansible ssh private/public key pair and add public (pub) key on your control node (local machine) copy to Umbrel.

```bash
ssh-keygen -f ~/.ssh/ansible  
ssh-copy-id -f -i ~/.ssh/ansible.pub ansible@umbrel.local
```

### Configure Ansible User

Configure the ansible user on the Umbrel machine with the `00-ansible.yaml` playbook.

```bash
ansible-playbook 00-ansible.yaml -i inventory/hosts.ini --ask-become-pass
```

## Configure Playbook

Options for the playbook can be configured in `./inventory/umbrel.yaml`. With available options being:

| Variable          | Description |
|:------------------|:-----|
| ANSIBLE_USER      | Name of the ansible user created in the prerequisites above |
| ANSIBLE_USER_PUB  | SSH public key of the Ansible controller machine for the ansible user. Can be found with `cat ~/.ssh/ansible.pub` |
| DOMAIN            | Domain to use with umbrel. Typically this is `local`, but not always |
| LND_ALIAS         | Your sexy Lightning node alias name |
| LND_COLOR         | Your sexy Lightning color |

## Configure your Umbrel Machine

Now we are set up to configure Umbrel each time we update Umbrel and the update overwrites the configuration files.

```bash
ansible-playbook 01-configure.yaml -i ./inventory/hosts.ini
```
