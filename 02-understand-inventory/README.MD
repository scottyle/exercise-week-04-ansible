# Understand the Inventory File
* How to build Ansible Inventory https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html

* Ansible works against multiple managed nodes or “hosts” in your infrastructure at the same time, using a list or group of lists known as inventory. Once your inventory is defined, you use patterns to select the hosts or groups you want Ansible to run against.

* The default location for inventory is a file called /etc/ansible/hosts. You can specify a different inventory file at the command line using the -i <path> option. You can also use multiple inventory files at the same time as described in Using multiple inventory sources, and/or pull inventory from dynamic or cloud sources or different formats (YAML, ini, and so on), as described in Working with dynamic inventory. 

```
[all:vars]
ansible_user=admin
ansible_password=admin

[routers:children]
cisco

[cisco]
rtr1 ansible_host=192.168.86.170
rtr2 ansible_host=192.168.86.171

[cisco:vars]
ansible_user=admin
ansible_network_os=ios
ansible_connection=network_cli

[dc1]
rtr1
rtr2
```


* In the above output every [ ] defines a group. For example [dc1] is a group that contains the hosts rtr1 and rtr2. Groups can also be nested. The group [routers] is a parent group to the group [cisco]

* Parent groups are declared using the children directive. Having nested groups allows the flexibility of assigining more specific values to variables.

* We can associate variables to groups and hosts.

```
Note:
A group called all always exists and contains all groups and hosts defined within an inventory.
```


* Host variables can be defined on the same line as the host themselves. For example for the host rtr1:

``` rtr1 ansible_host=172.16.129.86 ```

* rtr1 - The name that Ansible will use. This can but does not have to rely on DNS
* ansible_host - The IP address that ansible will use, if not configured it will default to DNS
* private_ip - This value is not reserved by ansible so it will default to a host variable. This variable can be used by playbooks or ignored completely.


Group variables groups are declared using the vars directive. Having groups allows the flexibility of assigning common variables to multiple hosts. Multiple group variables can be defined under the [group_name:vars] section. For example look at the group cisco:

```
[cisco:vars]
ansible_user=ec2-user
ansible_network_os=ios
ansible_connection=network_cli
```

* ansible_user - The user ansible will be used to login to this host, if not configured it will default to the user the playbook is run from
* ansible_host - The IP address that ansible will use, if not configured it will default to DNS
* ansible_network_os - This variable is necessary while using the network_cli connection type within a play definition, as we will see shortly.
* ansible_connection - This variable sets the connection plugin for this group. This can be set to values such as netconf, httpapi and network_cli depending on what this particular network platform supports.

