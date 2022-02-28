# Assignment .. Create a set of ansible playbooks.
The following assignment will get you started in writing ansible playbooks. Use the basic lab we have created and build a set of playbooks that configures the following settings.

* I've substituted this assignment instead of having a quiz next week*

## Due Date: <b> Feb 11th</b>
## Playbooks to create

- Hostname
- Login Banner
- SNMP
- Name Servers
- Logging
- NTP
- Configure IP address on interfaces
- Configure a routing protocol (router BGP/OSPF)


<i>Note: For NTP or logging or name servers you can use any ip address example 8.8.8.8</i>

## How to submit
- Using slate: [ excerise-week-04-ansible ]
- You can upload the playbooks in worddoc or send me a link to your repo for this assignment

## Resources
* Github page for cisco IOS ansible https://github.com/ansible-collections/cisco.ios 

## Example of a Paybook that deploy the cisco banner when you SSH into the switch
* https://github.com/ansible-collections/cisco.ios/blob/main/docs/cisco.ios.ios_banner_module.rst#examples

```
    - name: Configure the login banner
      cisco.ios.ios_banner:
        banner: login
        text: |
          This is my login banner
          that contains a multiline
          string
        state: present
```

