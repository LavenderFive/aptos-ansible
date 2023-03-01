# aptos-ansible
Ansible playbook for Aptos nodes and validators. This playbook is intended for node runners who utilize
docker, however it does also install aptos cli for those who may want it installed. Note also it makes no
assumptions about how many resources should be allocated to docker - it'll eat as much as it wants.

## Installation

### Pre reqs

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
This will do the initial setup and installation of a node. Note that it will *NOT* download the required 
files from the aptos core repo (yet!). Once this has been run, you will need to manually download and set
up the various files needed.

#### 1. copy inventory.yml.example
The inventory is the `rosetta stone` of the playbook. Copy over the example and fill in your variables.

```sh
mv inventory.yml.example inventory.yml
```
**Warning:** Due to the sensitive nature of this file, it should **NEVER** be exposed publicly, or
checked into version control. For that reason, it is in .gitignore.

#### 2. install dependencies
```sh
ansible-playbook main.yml -e target=aptos_mainnet
```

This will install dependencies (aptos cli, docker, python, etc.) on all machines outlined in `inventory.yml`.

### network_upgrade.yml playbook
This will:

1. check if the node is already at the appropriate version
2. download the latest docker image
3. restart the process with the newest image

Note how steps 2 and 3 are separate, as this is *KEY* to upgrading without missing proposals. Doing
them in a single step will result in ~5 minutes of down time.

#### 1. update node_version
For the desired network type (mainnet, testnet, previewnet), write over the `node_version` variable.

#### 2. run network_upgrade.yml
```sh
ansible-playbook network_upgrade.yml -e target=aptos_mainnet
```
