# Step 1 - Examine Ansible Playbook

```
---
- name: snmp ro/rw string configuration
  hosts: cisco
  gather_facts: no

  tasks:

    - name: ensure that the desired snmp strings are present
      cisco.ios.config:
        commands:
          - snmp-server community ansible-public RO
          - snmp-server community ansible-private RW
```

We will explore in detail the components of an Ansible Playbook in the next exercise. It is suffice for now to see that this playbook will run two Cisco IOS-XE commands

```
snmp-server community ansible-public RO
snmp-server community ansible-private RW
```

##  Step 2 - Execute Ansible Playbook

Run the playbook using ansible-playbook playbook.yaml

## Step 3 - Verify configuration on router

Verify that the Ansible Playbook worked. Login to rtr1 and check the running configuration on the Cisco IOS-XE device.

```
rtr1#show run | i snmp
snmp-server community ansible-public RO
snmp-server community ansible-private RW
```

## Step 4 - Validate idempotency
The cisco.ios.config module is idempotent. This means, a configuration change is pushed to the device if and only if that configuration does not exist on the end hosts.

To validate the concept of idempotency, re-run the playbook:

Re-running the Ansible Playbook multiple times will result in the same exact output, with ok=1 and change=0. Unless another operator or process removes or modifies the existing configuration on rtr1, this Ansible Playbook will just keep reporting ok=1 indicating that the configuration already exists and is configured correctly on the network device.

## Step 5 - Modify Ansible Playbook
Now update the task to add one more SNMP RO community string named ansible-test.

```
snmp-server community ansible-test RO
```

Use Visual Studio Code to open the playbook.yml file to add the command:

The Ansible Playbook will now look like this:

```
---
- name: snmp ro/rw string configuration
  hosts: cisco
  gather_facts: no

  tasks:

    - name: ensure that the desired snmp strings are present
      cisco.ios.config:
        commands:
          - snmp-server community ansible-public RO
          - snmp-server community ansible-private RW
          - snmp-server community ansible-test RO
```

Make sure to save the playbook.yml with the change.


## Step 6 Re-run the Ansible Playbook

## Step 7 Verify configuration is applied

```
[student1@ansible network-workshop]$ ssh rtr1

rtr1#sh run | i snmp
snmp-server community ansible-public RO
snmp-server community ansible-private RW
snmp-server community ansible-test RO
```

# Takeaway
* the config (e.g. cisco.ios.config) modules are idempotent, meaning they are stateful
* This Ansible Playbook could be scheduled in Automation controller to enforce the configuration. For example this could mean the Ansible Playbook could be run once a day for a particular network. In combination with check mode this could just be a read only Ansible Playbook that sees and reports if configuration is missing or modified on the network.

