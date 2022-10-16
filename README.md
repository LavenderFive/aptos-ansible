# aptos-ansible
Ansible playbook for Aptos nodes and validators

[Requires installation](https://docs.ansible.com/ansible/latest/collections/community/docker/docker_container_module.html#ansible-collections-community-docker-docker-container-module) of `aptos-docker`. 

```bash
ansible-galaxy collection install community.docker
```

To confirm it's installed, run:
```
ansible-galaxy collection list
```