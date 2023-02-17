<h1 align="center">
  <img alt="IDENA Sibling Ansible Playbook - for fast idena-go node client deployment on multiple servers" src="https://github.com/ltraveler/ltraveler/raw/main/images/lt_idena_sibling_logo.png" width="224px"/><br/>
  IDENA Sibling
</h1>
<p align="center"><b>Ansible Playbook</b> for fast <b>Idena Shared Node Deployment</b> and management.</p>

<p align="center"><a href="https://github.com/ltraveler/idena-sibling/releases/latest" target="_blank"><img src="https://img.shields.io/github/v/release/ltraveler/idena-sibling?style=for-the-badge&logo=none" alt="idena sibling latest version" /></a>&nbsp;<a href="https://wiki.ubuntu.com/FocalFossa/ReleaseNotes" target="_blank"><img src="https://img.shields.io/badge/Ansible-2.13+-00ADD8?style=for-the-badge&logo=none" alt="Ubuntu minimum version" /></a>&nbsp;<a href="https://github.com/ltraveler/idena-sibling/blob/main/CHANGELOG.md" target="_blank"><img src="https://img.shields.io/badge/Build-Stable-success?style=for-the-badge&logo=none" alt="idena-go latest release" /></a>&nbsp;<a href="https://www.gnu.org/licenses/quick-guide-gplv3.html" target="_blank"><img src="https://img.shields.io/badge/license-GPL3.0-red?style=for-the-badge&logo=none" alt="license" /></a>&nbsp;<a href="https://github.com/ltraveler/idena-sibling/blob/main/README.md" target="_blank"><img src="https://img.shields.io/badge/readme-ENGLISH-orange?style=for-the-badge&logo=none" alt="Скрипт Idena Sibling" /></a></p>

## 💬&nbsp; Overview

The main goal of this playbook is to help shared node operators deploy Idena Shared Node quickly and securely on multiple servers. You can configure all parameters of your shared node and easily import your API keys. The shared node will be deployed using an HTTPS connection, and you will need an SSL certificate that you can purchase or obtain for free using the Let's Encrypt service.

## 🫴&nbsp; Requirements:
### Destination droplets:

* Python version 3.9 or higher.
* Your public SSH key must be added to the authorized_keys file on the server. Alternatively, you may use password authentication as a less secure option.
* The script has been tested on Ubuntu 20.04 LTS and may work on other Debian-based distributions, but those have not been explicitly tested.

### Master node:

* Python version 3.9 or higher and pip3 must be installed.
* The latest version of Ansible must be installed on the machine.
* If you are using a Windows machine, you can run this playbook by using WSL2 with an Ubuntu distribution.

## ⚙️&nbsp; Configuration
### 🔐&nbsp; Secret and Public vars:

- Secret and public variables are stored in separate files in the `./idena-sibling/group_vars/main/` folder.
- Public variables are stored in a plain text file with the name `vars`.
- Sensitive variables are stored in a special secret vault file, which is saved as an encrypted file (`vault`).<br>To edit this file, you must remove the existing one and create a new file with your own password using the command `ansible-vault create vault`. To edit this file in the future, use the command `ansible-vault edit vault`. The file must have a similar structure to the example provided below:

```
---
vault_api_key: "your shared node api key"
vault_node_key: "your shared node pure private key"
userpass: "the password of the user under which name your shared node gonna be run"
droplet_ip: "your droplet destination ip address"
droplet_domain: "your.droplet_domain.com"
api_keys: [ "api_key_1", "api_key_2", "api_key_3" ]
#ansible_ssh_pass: "your_droplet_root_pass"
```

- To avoid having to re-enter your vault storage password every time you want to edit it, please save it in the `.vault_pass` plain text file in the root folder of the repository.
- Public variables are stored in the `./idena-sibling/group_vars/main/vars` file and can be edited directly using a text editor of your choice. The default parameters should be sufficient for most shared node deployment processes.

### 🎯&nbsp; Hosts configuration

All variables related to the destination connection must be set in the main hosts file, which is available in the root folder of the repository and is named `hosts`.

```
[main]
XXX.YYY.ZZZ.UUU

[all:vars]
ansible_ssh_private_key_file = ~/.ssh/id_rsa
ansible_user = root
idena_group = Olga
username = Olga
public_key_file = ~/.ssh/id_rsa.pub
#ansible_connection=ssh
```
If you prefer to use root password authentication instead of SSH key authentication, you will need to set your root password in the vault data storage under `ansible_ssh_pass` variable as described earlier.

## 🚀&nbsp; Basic configuration (requires root privileges)

Please make sure that you have a pure Ubuntu 20.04 droplet on the host side.
The structure of configuration files:
* `ansible.cfg` all sensitive information is saved in encrypted vault storage.   
* `hosts` change `host1_ip` to your droplet IP address; please uncomment corresponding line, add your droplet root password, change paths to your droplet private and public ssh_key.
* `vars` - change vars based on your requirements.
* `vault` - encrypted data storage.
Vault file structure:
```
---
vault_api_key: "f350Aea6f1d62b079E478d3b372966E3"
vault_node_key: "idena node key"
userpass: "idena user password"
```
* `ssh-copy-id root@host1_ip` add your pub key as an authorized key in your droplet
* `ansible-playbook -i hosts idena_install.yaml` run Ansible playbook

PLEASE ATTEND!!! This is pre-alpha version.
There's no warranty, one should use it at one's own risk.
