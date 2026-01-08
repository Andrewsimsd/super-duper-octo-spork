//install ansible on controller
sudo apt update
udo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
ansible-galaxy collection install ansible.posix
//Create your Ansible workspace
mkdir -p ~/dev-machines/{inventory,playbooks,roles}
cd ~/dev-machines

// verify install with:
ansible --version
 


// on target manually:
sudo apt-get update
apt install -y openssh-server
sudo systemctl enable --now ssh
sudo apt install net-tools

//Make sure you can SSH without typing passwords:
//create ssh key on controller
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -C "dev-machines"
ssh-copy-id administrator@192.168.1.90
as a backup :ssh-copy-id -i  ~/.ssh/id_ed25519.pub administrator@192.168.1.90



// test connectivity
ansible -i inventory/hosts.ini dev -m ping

run a playbook
ansible-playbook -i inventory/hosts.ini playbooks/cryptsetup.yml --ask-become-pass
