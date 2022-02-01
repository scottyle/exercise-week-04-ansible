# Ansible Facts

## Objective
Demonstration use of Ansible facts on network infrastructure.

Ansible facts are information derived from speaking to the remote network elements. Ansible facts are returned in structured data (JSON) that makes it easy manipulate or modify. For example a network engineer could create an audit report very quickly using Ansible facts and templating them into a markdown or HTML file.

This exercise will cover:

Building an Ansible Playbook from scratch.
Using ansible-navigator :doc for documentation
Using the cisco.ios.facts module.
Using the debug module.

## Examine the documents
YAML representation of the same exact documentation you would see on docs.ansible.com. Examples can be cut and paste directly from the module documentation into your Ansible Playbook.

When referring to a non-built in module, there is three important fields:
namespace.collection.module

For example:
```
cisco.ios.facts
```

Explanation of terms:

* namespace - example cisco - A namespace is grouping of multiple collections. The cisco namespace contains multiple collections including ios, nxos, and iosxr.
* collection - example ios - A collection is a distribution format for Ansible content that can include playbooks, roles, modules, and plugins. The ios collection contains all the modules for Cisco IOS/IOS-XE
* module - example facts - Modules are discrete units of code that can be used in a playbook task. For example the facts modules will return structured data about that specified system.


## Step 2 - Creating the play

Enter the following play definition into facts.yml:

```
---
- name: gather information from routers
  hosts: cisco
  gather_facts: no
```

Here is an explanation of each line:

* The first line, --- indicates that this is a YAML file.
* The - name: keyword is an optional description for this particular Ansible Playbook.
* The hosts: keyword means this playbook against the group cisco defined in the inventory file.
* The gather_facts: no is required since as of Ansible 2.8 and earlier, this only works on Linux hosts, and not network infrastructure. We will use a specific module to gather facts for network equipment.

## Step 3 - Create the facts task

Next, add the first task. This task will use the cisco.ios.facts module to gather facts about each device in the group cisco.

```
---
- name: gather information from routers
  hosts: cisco
  gather_facts: no

  tasks:
    - name: gather router facts
      cisco.ios.facts:
```

Note: A play is a list of tasks. Modules are pre-written code that perform the task.

Save the playbook.

## Step 4 - Executing the playbook
You can scroll down to view any facts that were collected from the Cisco network device.

## Step 5 - Using debug module
Write two additional tasks that display the routers' OS version and serial number. -vvvv

```
---
- name: gather information from routers
  hosts: cisco
  gather_facts: no

  tasks:
    - name: gather router facts
      cisco.ios.facts:

    - name: display version
      debug:
        msg: "The IOS version is: {{ ansible_net_version }}"

    - name: display serial number
      debug:
        msg: "The serial number is:{{ ansible_net_serialnum }}"
```

## Step 6 - Using stdout



## Takeaways
* The ansible command will allow you access to documentation without an internet connection. This documentation also matches the version of Ansible on the control node.
* The cisco.ios.facts module gathers structured data specific for Cisco IOS. There are relevant modules for each network platform. For example there is a junos_facts for Juniper Junos, and a eos_facts for Arista EOS.
The debug module allows an Ansible Playbook to print values to the terminal window.