# Demo Playbook for sros_config module

This playbook tests if the [sros_config module](https://docs.ansible.com/ansible/2.9/modules/sros_config_module.html) is working as expected.
All possible modules for SROS: https://docs.ansible.com/ansible/2.9/modules/list_of_network_modules.html#sros

Use cases tested in this demo playbook:
* Create config backup
* Set system name
* Get number of linecards
* Configure customer and VLAN
* Load config from file



## Usage

First adjust the `vars.yml` and add the credentials for the device.

Run the playbooK:
```
ansible-playbook main.yml --diff --check
ansible-playbook main.yml --tags demo::command --diff --check
ansible-playbook main.yml --tags demo::rollback --diff --check
ansible-playbook main.yml --tags demo::config:details --diff --check
ansible-playbook main.yml --tags demo::config:single --diff --check
ansible-playbook main.yml --tags demo::config:loop --diff --check
ansible-playbook main.yml --tags demo::config:file --diff --check
```

## Checks

Following things are required to test to met the expectation of this modules:

### Generic

What is the output if:

* [ ] `--check` is used?
* [ ] `--check --diff` is used?
* [ ] Runtime measurements

### sros_command module

* Not used for configuration! Use the next module!
* https://docs.ansible.com/ansible/2.9/modules/sros_command_module.html#sros-command-module

Checks:
* [ ] Show version of device
* [ ] Show version and port details of device
* [ ] Run a single command to gather information/facts for further processing (number of linecards)


### sros_rollback

* https://docs.ansible.com/ansible/2.9/modules/sros_rollback_module.html#sros-rollback-module

Checks:
* [ ] Create rollback location
* [ ] Remove rollback location

### sros_config module

* Used to configure the device
* https://docs.ansible.com/ansible/2.9/modules/sros_config_module.html

Checks:
* [ ] Use backup feature to create dump of running-config
* [ ] Print system details
* [ ] Adjust system name
* [ ] Create customer and VLAN (single + loop)
* [ ] Add second customer and VLAN with a prepared config file

Expected result: Two customers (ID 42 & 43) with a single MGMT VLAN

Raw Commands for config module test:
```
configure service customer 3420 description "Customer_42"
configure service vpls 3420 customer 3420 create
configure service vpls 3420 description 42_MGMT
configure service vpls 3420 sap lag-1:3420 create
configure service vpls 3420 sap lt:1/1/1:3420  create
configure service vpls 3420 no shutdown
configure vlan id 3420 mode residential-bridge name 42_MGMT new-broadcast enable new-secure-fwd enable ipv6-mcast-ctrl
configure vlan id 3420 dhcp-opt82-ext enable circuit-id-dhcp physical-id remote-id-dhcp customer-id
```
