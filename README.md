<h1 align="center">
  <img alt="IDENA Sibling Ansible Playbook - for fast idena-go node client deployment on multiple servers" src="https://github.com/ltraveler/ltraveler/raw/main/images/lt_idena_sibling_logo.png" width="224px"/><br/>
  IDENA Sibling
</h1>
<p align="center"><b>Ansible Playbook</b> for fast <b>Idena Shared Node Deployment</b> and management.</p>

<p align="center"><a href="https://github.com/ltraveler/idena-sibling/releases/latest" target="_blank"><img src="https://img.shields.io/github/v/release/ltraveler/idena-sibling?style=for-the-badge&logo=none" alt="idena sibling latest version" /></a>&nbsp;<a href="https://wiki.ubuntu.com/FocalFossa/ReleaseNotes" target="_blank"><img src="https://img.shields.io/badge/Ansible-2.13+-00ADD8?style=for-the-badge&logo=none" alt="Ubuntu minimum version" /></a>&nbsp;<a href="https://github.com/ltraveler/idena-sibling/blob/main/CHANGELOG.md" target="_blank"><img src="https://img.shields.io/badge/Build-Stable-success?style=for-the-badge&logo=none" alt="idena-go latest release" /></a>&nbsp;<a href="https://www.gnu.org/licenses/quick-guide-gplv3.html" target="_blank"><img src="https://img.shields.io/badge/license-GPL3.0-red?style=for-the-badge&logo=none" alt="license" /></a>&nbsp;<a href="https://github.com/ltraveler/idena-sibling/blob/main/README.md" target="_blank"><img src="https://img.shields.io/badge/readme-ENGLISH-orange?style=for-the-badge&logo=none" alt="Скрипт Idena Sibling" /></a></p>

## 💬&nbsp; Overview

The main goal of this playbook is to facilitate a quick and secure deployment of Idena Node / Shared Node. You can configure all parameters of your node / shared node and easily update your shared node API keys. If you choose to deploy a shared node, it will be deployed using an HTTPS connection. You can opt to use your own SSL certificate or a certificate from Let's Encrypt. If you choose to use Let's Encrypt, the certificate will be automatically updated via a special crontab task.

### 📚&nbsp; The complete guide of how to use Idena Sibling is available here:
https://medium.com/@idna.project/6ff31873101b

## 🫴&nbsp; Requirements:

### Destination droplets:

- Python version 3.9 or higher is required.
- Your public SSH key must be added to the authorized_keys file on the server. You can use the following command: `ssh-copy-id -i ~/.ssh/mykey user@host
`. Alternatively, you may use password authentication as a less secure option. To change the authentication method, please modify the value of the `sshd_PasswordAuthentication` variable inside the `./group_vars/main/vars` file and uncomment the `ansible_connection` variable inside the `host` file located in the root of the repository folder.
- The script has been tested on Ubuntu 20.04 LTS and may work on other Debian-based distributions, but they have not been explicitly tested.

### Master node:

- Python version 3.9 or higher and pip3 must be installed.
- The latest version of Ansible must be installed on the machine.
- If you are using a Windows machine, you can run this playbook by using WSL2 with an Ubuntu distribution.

## ⚙️&nbsp; Configuration

### 🔐&nbsp; Secret and Public vars:

- Secret (`vault` file) and public (`vars` file) variables are stored in the `./idena-sibling/group_vars/main/` folder.
- `api_keys.yaml` and `api_mgmt.yaml` consist of variables that are responsible for managing your API keys. `api_keys.yaml` consists of the complete list of your API keys that are supposed to be added to the remote node. `api_mgmt.yaml` consists of two variables that have keys which are supposed to be added and removed after shared node installation. The logic is pretty simple: first, import keys from `api_keys.yaml`, and then add keys from the `api_keys_add` variable and remove keys from the `api_keys_remove` variable in the `api_mgmt.yaml` file.

**Please note**: that the Shared Node Installation playbook (`idena_shared.yaml`) will import API keys from the `api_keys.yaml` file and ignore the values from the   `api_mgmt.yaml` file, such as `api_keys_add` and `api_keys_remove`. On the other hand, the Idena Node Management playbook (`idena_node_mgmt.yaml`) will use `api_keys_add` and `api_keys_remove` to add and remove keys from your remote droplet idena-node-proxy configuration. It will add new keys, remove old ones, and delete duplicates. After all of these manipulations, the `apiKeys` values will be synced with your local `api_keys.yaml` file.

- Public variables are stored in a plain text file with the name `vars`.
- Sensitive variables are stored in a special secret vault file, which is saved as an encrypted file (`vault`).<br>To edit this file, you must remove the existing one and create a new file with your own password using the command `ansible-vault create vault`. To edit this file in the future, use the command `ansible-vault edit vault`. The file must have a similar structure to the example provided below:

```
---
vault_api_key: "your shared node api key"
vault_node_key: "your shared node pure private key"
userpass: "the password of the user under which name your shared node gonna be run"
letsencrypt_email: "email@for_letsencrypt_certificate.com"
droplet_domain: "your.droplet_domain.com"
#ansible_ssh_pass: "your_droplet_ssh_sudo_pass"
#ansible_sudo_pass: "your_droplet_sudo_pass"
```

- To avoid having to re-enter your vault storage password every time you want to edit it, please save it in the `.vault_pass` plain text file in the root folder of the repository.
- Public variables are stored in the `./idena-sibling/group_vars/main/vars` file and can be edited directly using a text editor of your choice. The default parameters should be sufficient for most shared node deployment processes.

### 📜&nbsp; Generating a PEM file for your SSL certificate:

If you have your own SSL certificate for your shared node domain please check that the value of `letsencrypt` variable is set to `no`.
Generally, after purchasing an SSL certificate, you will receive a file archive containing the following files.

```
.crt
server.csr
server.key
```

To generate a Privacy Enhanced Mail (PEM) file, you typically need to concatenate the two files using the following command inside the folder that contains the crt and key files:<br>`cat server.crt server.key > domain_pem`

After creating your final PEM file called `domain_pem`, you would need to copy it (overwrite the existed one) to the `./idena-sibling/node/` folder of the repository and encrypt it using the command `ansible-vault encrypt domain_pem`.<br>If you want to change the contents of your certificate vault storage in the future, you can use the command `ansible-vault edit domain_pem`.

### 📜&nbsp; Generate and Install a Let's Encrypt SSL Certificate:

Apart from using your own SSL certificate, the certificate can be issued by the Let's Encrypt provider by setting the value of the `letsencrypt` variable to `yes`. In that case, please ensure that the A record of your shared node domain has been correctly set to the IP address of the droplet that you are using to install your shared node. If everything has been set correctly, you will end up with a fully configured shared node, including a special crontask to automatically update your Let's Encrypt SSL certificate before it expires.

### 📢&nbsp; Public Variables Configuration

The playbook has been configured to change all parameters from configuration files of idena-go and idena-node-proxy applications. You could have a look at `idena-sibling/group_vars/main/vars` file. Hovewer I will put your attention on the most important variables that with great probability you would like to change:

`idena_go_ver: "1.0.3"` You need to set the latest version of the [Idena-go](https://github.com/idena-network/idena-go/releases) repository.
`sshd_PasswordAuthentication: "no"` By setting this variable, you can choose to use or avoid using an SSH password to authenticate to your server. Please be careful when setting the value to `no`, as if you lose your private key, you won't be able to authenticate yourself in the droplet.
`letsencrypt: "yes"` Choosing between Let's Encrypt SSL Certificate and your own SSL certificate that would be imported from `./node/domain_pem` file.

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
#ansible_ssh_port=10222
```

If you prefer to use root password authentication instead of SSH key authentication, you will need to set your root password in the vault data storage under `ansible_ssh_pass` variable as described earlier and uncomment `ansible_connection=ssh` variable in the `hosts` file.

`#ansible_ssh_port=10222` - if you are using a non-standard SSH port, you would need to uncomment and set it here.

## ⏳&nbsp; Install required collections and python libraries on your local machine:
Before starting to use these playbooks, please install the required Python packages and Ansible-galaxy modules.

- **Installing dnspython library** `pip3 install dnspython`
- **Installing required ansible community modules** `ansible-galaxy install -r requirements.yml`

## 🚀&nbsp; Run the playbook:

If the `host_key_checking` variable from `ansible.cfg` file is set to `false`, use `ssh-copy-id root@XXX.YYY.ZZZ.UUU` to automatically copy your **public SSH key** to the `authorized_keys` file on your destination droplet server.

### ☑&nbsp; After setting all parameters, run the playbook depends on what kind of installation you would like to make:
  1. **Regular node installation**: `ansible-playbook -i hosts idena_node.yaml`
  2. **Shared node installation**: `ansible-playbook -i hosts idena_shared.yaml`
  3. **Management of Idena Node after installation**: `ansible-playbook -i hosts idena_node_mgmt.yaml --tags operation_tag_name`

#### Possible values of _operation_tag_name_:
- `IdenaNodekeyUpdate`: Takes a new nodekey (private key) value from the `vault_node_key` variable.
- `IdenaNodeStart`: Starts the Idena Node.
- `IdenaNodeStop`: Stops the Idena Node.
- `IdenaMiningOn`: Changes mining status to ON.
- `IdenaMiningOff`: Changes mining status to OFF.
- `IdenaApiKeysUpdate`: Updates Shared Node API keys after chaning values inside `./group_vars/main/api_keys.yaml` and `./group_vars/main/api_mgmt.yaml`.

## 🗒️&nbsp; Ater using the playbook there are a few things left to do:

    ✦ If you have used a custom SSL certificate option (letsencrypt: "no") and haven't done so already, change the DNS A record related to your droplet domain.
    ✧ Try accessing your shared node's domain via a web browser using the "https://" prefix.
    ✧ Try to connect to your shared node using any of your API keys via app.idena.io.
    ✦ Please remember that after entering the API key and Shared Node URL in the app.idena.io, your status should become 'ONLINE'.

──────────────────────────────────────────────────<br>
💻 LTraveler:<br>
💬 Telegram: https://t.me/ltrvlr<br>
🌐 WWW: https://ltraveler.github.io<br>
👛 `0xf041640788910fc89a211cd5bcbf518f4f14d831`<br>

<ins>Please note</ins>: All playbooks inside this repository have been tested on the author's own droplets.
There's no warranty, one should use it at one's own risk.
