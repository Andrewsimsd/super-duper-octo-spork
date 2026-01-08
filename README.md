# Ansible Dev Machines Setup

This repository documents how to set up Ansible on a **controller** machine and prepare **target** development machines for management. Follow the steps in order.

## 1) Install Ansible on the controller

Update package metadata (keeps the package list current):

```bash
sudo apt update
```

Install helpers so you can add PPAs:

```bash
sudo apt install -y software-properties-common
```

Add the official Ansible PPA and refresh package metadata:

```bash
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

Install Ansible itself:

```bash
sudo apt install -y ansible
```

Install the `ansible.posix` collection (includes common POSIX modules used in playbooks):

```bash
ansible-galaxy collection install ansible.posix
```

## 2) Create your Ansible workspace

Create a standard directory layout for inventory, playbooks, and roles:

```bash
mkdir -p ~/dev-machines/{inventory,playbooks,roles}
```

Move into the workspace so relative paths in commands below are consistent:

```bash
cd ~/dev-machines
```
An existing workspace is included in this repository.

## 2a) Directory purpose and usage

Ansible projects commonly organize content into the following directories:

- `inventory/`: defines target hosts and groups (for example, `hosts.ini`) that playbooks run against. See the Ansible inventory documentation for supported formats and conventions: https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html
- `playbooks/`: contains YAML playbooks that describe the desired state of the target machines. See the playbook documentation for structure and execution details: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_intro.html
- `roles/`: a reusable packaging format for tasks, handlers, templates, and defaults. This repository currently does **not** use roles, but the directory exists so you can adopt roles later as the playbooks grow. See the roles documentation for the standard layout and how to apply them in playbooks: https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html

## 3) Verify the controller installation

Check that Ansible is installed and available in your PATH:

```bash
ansible --version
```

## 4) Prepare the target machine(s)

On each target, ensure SSH is installed and running so Ansible can connect:

Update package metadata:

```bash
sudo apt-get update
```

Install the SSH server:

```bash
sudo apt install -y openssh-server
```

Enable and start the SSH service immediately:

```bash
sudo systemctl enable --now ssh
```

Install networking tools (useful for obtaining the IP address via ifconfig is leasing through DHCP):

```bash
sudo apt install -y net-tools
```

## 5) Configure SSH key-based access

Ansible works best with passwordless SSH. Generate a key on the **controller**:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "dev-machines"
```

Copy the public key to the target (prompts once for the remote password):

```bash
ssh-copy-id administrator@192.168.1.90
```

If the default command fails, explicitly provide the public key path:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub administrator@192.168.1.90
```

## 6) Test connectivity

Use Ansible’s built-in `ping` module to verify inventory connectivity:

```bash
ansible -i inventory/hosts.ini dev -m ping
```

## 7) Run a playbook

Execute a playbook against your inventory. `--ask-become-pass` prompts for sudo access on the targets:

```bash
ansible-playbook -i inventory/hosts.ini playbooks/cryptsetup.yml --ask-become-pass
```
