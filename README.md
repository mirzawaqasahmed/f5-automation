# f5-automation

This repository contains different scripts and Ansible playbooks used for testing F5 Automation.

## Roles

* onboarding
* deploy
* getters

### Onboarding - role

This role will perform initial configuration of the F5 appliance. It will set the following configuration parameters.

* license
* ntp
* dns
* motd banner
* (un)tagged vlan
* self-ip
* static routes
* management routes.

In order to use this role you would need to update the variable file under roles/onboarding/vars/main.yml.

### Deploy - role

This role can be used to deploy configuration on F5 appliance. This variable file under /roles/deploy/vars/main.yml is modeled in such a way that so that a full schema of the service can be provided and hence deployed accordingly.
Currently this role supports the following configuration deployment.

1. Creating/Deleting (based on tag) a UCS/backup file
2. Creation of irule based on a template.
3. Uploading irule to F5.
4. Creating nodes.
5. Creating Pools.
6. Updating pool with its members
7. Creating virtual servers
8. Creating simple http redirect virtual servers.
9. Saving configuration in the end.


_Note: Currently there is a bug in the bigip_virtual_server that errors out if a new VIP is pushed along with irule definition. Hopefully in future it will be fixed and then irules can be activated along with the creation of the VIP. In the meantime you may have to run this role twice, once without irule defnition and second time with the irule definition._

### Getters - role

This is rather a simple role. In which case it fetches facts from BigIP. This is currently using bigip_facts and bigip_gtm_facts, which are announced eol. In future this will be using the bigip_device_facts module.
All facts are stored in facts/inventory_hostname directory structure.

## Playbooks

1. `pb_switch_pool_availability.yml` playbook will perform the following

    1. Ask for the bigip device to run this playbook against.
    2. Add it dynamically to inventory.
    3. Fetch the current pool facts from bigip.
    4. Store them in stats/pool_status.yml file.
    5. Ask for the pool member, node, node-port, IP to enable/disable/create/delete/force-offline a node in an existing pool.
    6. Confirm if the current state is as per the desired state of the request.

_Note: You can provide all variables at runtime of the playbook too, similar to:_

```bash
ansible-playbook -i inventory pb_switch_pool_availability.yml \
-e pool_name=test_pool-2 -e member_ip=10.10.10.11 \
-e member_port=80 -e member_name=node-11 \
-e target_host=adc01.lab.local \
-e member_state=present
```

2. `pb_test_ansible_docker.yml` a dummy playbook to test vscode/ansible/docker integration.