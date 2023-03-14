# aptos-ansible
Ansible playbook for Aptos public nodes. This playbook is intended for node runners who utilize
docker, however it does also install aptos cli for those who may want it installed. Note also it makes no
assumptions about how many resources should be allocated to docker - it'll eat as much as it wants. This
is not intended to be used to fully setup a validator or full node, but to help initiate the process.

**Assumes you are NOT using root**

## Installation

### Pre-reqs

#### server setup
This is **NOT** intended to securely set up your server, and assumes you already have. If you would like an
initial pass at server setup (including firewalls), try out our [server-setup ansible playbook](https://github.com/LavenderFive/secure-server-setup-ansible). 

This playbook will open the required ports for peering, and open ports for API if you're using a public node or
validator-full-node. It keeps the port closed for validators.

#### ansible
[Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) must be installed locally.
If you have python installed, it should be as easy as:
```sh
python3 -m pip install --user ansible
```

#### ansible.community.docker
[Requires installation](https://docs.ansible.com/ansible/latest/collections/community/docker/docker_container_module.html#ansible-collections-community-docker-docker-container-module) of `ansible.community.docker`. 

```bash
ansible-galaxy collection install community.docker
```

To confirm it's installed, run:
```
ansible-galaxy collection list
```

**Note:** some ansible installation methods come with `ansible.community.docker` pre installed.

## Usage

### main.yml playbook
This is intended for **public full nodes**. Validators and Validator-full-nodes may come later.

On the target machine, this will:
1. install all dependencies including python, docker, and Aptos cli
2. download and setup the public full node configs
3. launch/begin syncing the public full node

Note that it will *NOT* download the required files from the aptos core repository, but instead utilizes local files. 

#### 1. copy inventory.yml.example
The inventory file is the `rosetta stone` of the playbook. Copy over the example and fill in your variables.

```sh
mv inventory.yml.example inventory.yml
```
**Warning:** Due to the sensitive nature of this file, it should **NEVER** be exposed publicly, or
checked into version control. For that reason, it is in .gitignore.

#### 2. run playbook
```sh
ansible-playbook main.yml -e target=aptos_mainnet
```

After this, the public node should be syncing.

### network_upgrade.yml playbook
This will:

1. check if the node is already at the appropriate version
2. if it's not on the correct version, upgrade to latest
2. download the latest docker image
3. restart the process with the newest image

Note how steps 2 and 3 are separate, as this is *KEY* to upgrading without missing proposals. Doing
them in a single step will result in ~5 minutes of down time.

#### 1. update node_version
For the desired network type (mainnet, testnet, previewnet), write over the `node_version` variable in `inventory.yml`.

#### 2. run network_upgrade.yml
```sh
ansible-playbook network_upgrade.yml -e target=aptos_mainnet
```
