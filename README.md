[![Build Status](https://travis-ci.org/scathatheworm/ansible-netsnmp.svg)](https://travis-ci.org/scathatheworm/ansible-netsnmp) [![Ansible Galaxy](http://img.shields.io/badge/galaxy-scathatheworm.netsnmp.svg)](https://galaxy.ansible.com/scathatheworm/netsnmp)

# ansible-netsnmp
Ansible Role for deploying and configuring a simple v1/v2c snmp daemon

## Description of variables as found in defaults/main.yml

# Default loglevel
This can be 0,1,2,3,4,5,6,7, matching log levels of:
- 0: emergency
- 1: alert
- 2: critical
- 3: error
- 4: warning
- 5: notice
- 6: info
- 7: debug

The default is 6 which is pretty verbose and floods logs

```
snmpd_loglevel: 4
```

# Define listening port

```
snmpd_port: 161
```
 
# Disable ipv6 by default

```
snmpd_ipv6_enabled: false
```

# sysLocation, sysContact, and sysName definitions, empty by default

```
# snmpd_sysLocation: 
# snmpd_sysContact:
# snmpd_sysName: 
```

# fallback to assign sysName as the ansible inventory hostname

```
snmpd_sysName_ansible_inventory_hostname: true
```

# Building views
The `systemview` name is assigned the most common OIDs for viewing host resources, `all` defines a view for ALL resources. This is optional.

```
snmpd_views:
- name: systemview
  tree_object: .1.3.6.1.2.1.1
- name: systemview
  tree_object: .1.3.6.1.2.1.25
- name: all
  tree_object: .1
```

# Access control
This will define communities named `public` and `private` which only localhost can access. `view` and `network` are optional. For a `trapcommunity`, view and network should not be defined. This block is optional.

```
snmpd_community_acl:
- type: rocommunity
  community_name: public
  view: systemview
  network: 127.0.0.1
- type: rwcommunity
  community_name: private
  view: systemview
  network: 127.0.0.1
- type: trapcommunity
  community_name: trapcommunity
```

# Define trap destinations
`trapsink` is to send SNMPv1 traps, `trap2sink` is to send SNMPv2 traps. Ordinarily you should define a single version or the destination will get duplicate notifications. The definition of a port is optional, the default is 162. Community is also optional, but if used, it must be defined above in Access Control using `snmpd_community_acl` as a `trapcommunity`

```
snmpd_trap_destionation:
- type: trapsink
  host: 127.0.0.1
  community: trapcommunity
  port: 162
- type: trap2sink
  host: 127.0.0.1
  community: trapcommunity
  port: 162
```

# Send a trap notification upon failed SNMP authenthication

```
snmpd_auth_traps: true
```

# Enable agentx master behaviour for extensibility

```
snmpd_enable_agentx: true
```

# Additional snmp configuration
This allows to define extra custom configuration parameters

```
snmpd_additional_config: |
  sysServices    72
  proc  mountd
  proc  ntalkd    4
  proc  sendmail 10 1
  disk       /     10000
  disk       /var  5%
  includeAllDisks  10%
  load   12 10 5
  trapsink     localhost public
  iquerySecName   internalUser
  rouser          internalUser
  defaultMonitors          yes
  linkUpDownNotifications  yes
  extend    test1   /bin/echo  Hello, world!
  extend-sh test2 echo Hello, world! ; echo Hi there ; exit 35
```
