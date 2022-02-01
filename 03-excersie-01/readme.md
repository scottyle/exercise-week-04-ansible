# Step 1: Setup/Connect to your ansible server
1. Log into your ansible server or deploy the docker from the folder above. 
* <b>Optional</b> if you are using the docker and VS Code.. 
  0. Edit the docker to include you own github account
  1. Deploy the docker ``` docker build . -t ansible ```
  2. Run the docker ``` docker run -it ansible``` 
  3. Install Docker extention in VS Code ![image](vscode1.png)
  4. When you click on Docker icon you will see the container is deployed, you can right click on the container and ![image](vscode2.png) see the file system, attach the shell..etc
  5. This helps you edit and create files easier using VSCODE

2. Fork this repo to your github account.. https://github.com/TELE36058-Software-Defined-Networks/exercise-week-04-ansible.git 
3. Clone the repo from your account to your ansible server


# Step 3: Connect to your cisco switch and configure the management IP
1. Using VMWare consol log into your cisco switch and go configure gigabitethernet 1 with a management IP address
2. <br> optional </br> you can select ip dhcp (this will grab an ip address from your home network
3. <br> optional </br> you can specificy a static ip

# Step 3: Create your inventory file 
1. You can create an inventory file in linux ``` touch hosts ```
2. Edit your hosts file to include 
3. Start with the following inventory file
4. <br>note</br> change the IPs to the IPs you configured

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

# Step 4: Create your first Cisco playbook
* Lets create a simple playbook that shows us the version
* Cisco Ansible modules https://docs.ansible.com/ansible/latest/collections/cisco/ios/ios_facts_module.html 
* Cisco ansible galaxy https://galaxy.ansible.com/cisco/ios 
* The docker installs the cisco modules automatically, but if you are not using it you can do it manually on your ansible server ``` ansible-galaxy collection install cisco.ios ```

1. Built our first get facts playbook
2. You can use VI or VSCdoe.. built a get facts playbook calls get_facts.yaml


```
---
- name: Get facts 
  hosts: cisco
  gather_facts: no

  tasks:
    - name: Gather all legacy facts
      cisco.ios.ios_facts:
        gather_subset: all
```

3. Execute your playbook  ``` ansible-playbook get_facts.yaml -i hosts ```
4. If the playbook was successful you will see ok=1 ![playbook](playbook1.png)
5. Unfortunaly we didn't get any information printed out.. so lets get more output in our terminal
6. To troubleshoot ansible-playbooks you can use ``` -v ``` to get more detailed information out.. also if you add more ``` -vv ``` you get more info.. if you add 3x or 4x -vvvvv you get more details.. . 
7. Execute  ``` ansible-playbook get_facts.yaml -i hosts -v```
8. View the output
9. Execute  ``` ansible-playbook get_facts.yaml -i hosts -vv```
10. View the output
11. Execute  ``` ansible-playbook get_facts.yaml -i hosts -vvv```
12. View the output

Note: -vvv is the sweat spot for me to troubleshoot my plabooks.. the more you "v" you add the more noice you get.


# Step 5: Create a new get facts playbook with output
Create a playbook that will provide us detailed output for a specific configuration

1. Create a new playbook called get_facts_display.yaml 

```
---
- name:  Display Cisco info
  hosts: cisco
  gather_facts: no

  tasks:
    - name: Display Cisco Info
      cisco.ios.ios_facts:
        gather_subset: all

    - name: Display Model number 
      debug:
        msg: "The IOS model is {{ansible_net_model}}"

```

2. Execute the playbook  ``` ansible-playbook get_facts_display.yaml -i hosts  ```
3. Next edit your playbook and get some more info from the device
4. Get the Version of IOS

```
   - name: Display Version number 
      debug:
        msg: "The IOS Version is {{ansible_net_version}}"
```
5. Execute the playbook to make sure you it works..
6. Execute the same playbook with -vvv and add more functionity into your playbook.. get the hostname { { ansible_net_hostname }}
7. Add {{ ansible_net_hostname }}

```
   - name: Display Switch hostname
      debug:
        msg: "The Hostname of the switch it {{ansible_net_hostname}}"
```

8. Execute the playbook to make it works.
9. Add more data.
10. Now lets print out subdirectories
11. When we execute -vvv after the playbook we saw the following... 

```
"ansible_net_interfaces": {
            "GigabitEthernet1": {
                "bandwidth": 1000000,
                "description": null,
                "duplex": "Full",
                "ipv4": [
                    {
                        "address": "192.168.86.170",
                        "subnet": "24"
                    }
                ],
                "lineprotocol": "up",
```

12. Next lets edit your exiting playbook and get the information of the interface.

```

    - name: Display Interface info
      debug:
        msg: "The GigabitEthernet1 interface is up {{ansible_net_interfaces.GigabitEthernet1.lineprotocol}}"
```


13. Execute the playbook to ensure that you get the

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

