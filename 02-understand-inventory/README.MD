In the above output every [ ] defines a group. For example [dc1] is a group that contains the hosts rtr1 and rtr3. Groups can also be nested. The group [routers] is a parent group to the group [cisco]

Parent groups are declared using the children directive. Having nested groups allows the flexibility of assigining more specific values to variables.

We can associate variables to groups and hosts.
````
Note:
A group called all always exists and contains all groups and hosts defined within an inventory.
```

Host variables can be defined on the same line as the host themselves. For example for the host rtr1:
``` rtr1 private_ip=172.16.129.86 ```

* rtr1 - The name that Ansible will use. This can but does not have to rely on DNS
* private_ip - This value is not reserved by ansible so it will default to a host variable. This variable can be used by playbooks or ignored completely. (Mgmt IP address of the device)

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

