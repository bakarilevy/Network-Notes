JunOS uses FreeBSD under the hood so some Unix commands are available like the pipe operator and grep etc.

To start the unix shell:
```
start shell
```

Context sensitive help:
```
?
```

Space does the same as tab, autocompletes a command

You begin JunOS in operational mode, here you can run basic commands.

Basic show commands:
```
show configuration
show system uptime
show route
show route brief
show route detail
```

To get cpu usage information:
```
show chassis routing-engine
```

To get basic information on interfaces:
```
show interfaces terse
```

To configure enter the configuration command:
```
configure
```

You can then edit different aspects of the configuration:
```
edit protocols
```
```
edit protocols bgp
```

To return to the top of the hierarchy:
```
top
```

To change a particular value, use the set command:
```
configure
edit system
set hostname example-router
```
When commands are entered in JunOS the configurations are entered into the candidate configuration, they are not immedeately changed.
To see the commands in the candidate configuration:
```
show | compare
```

To apply the entered settings you need to run the commit command:
```
commit
```
If there are no errors in the configurations you can commit and quit:
```
commit and-quit
```

When in configuration mode to use regular commands, prefix with run:
```
run show route detail
```

To leave configuration mode:
```
exit
```

To delete a configuration statement simply preface the statement with delete (functions the same as no command):
```
delete ...
```

To check if your configuration candidate has any problems run:
```
commit check
```

To remove any faulty configurations at the top of the edit hierarchy run the command (erases everything in candidate config):
```
rollback
```

Configurations that have been committed will be written to disk and persist on system restart.

Long way to edit an interface:
```
edit interfaces
edit lo0
edit unit 12
edit family inet
set address 1.1.1.1/32
```

Fast way to edit an interface:
```
set intefaces lo0 unit 12 family inet address 2.2.2.2/32
```

To disable an interface:
```
set interfaces lo0 disable
commit
run show interfaces terse
```

To modify an interface without deleting:
```
rename interfaces lo0 unit 12 to unit 0
```

Interfaces can have multiple IPs a primary address is used for traffic sent to a remote network, the preferred is used for traffic sent to the local network.

```
set interfaces lo0 unit 0 family inet address 10.10.10.10/24 primary
set interfaces lo0 unit 0 family inet address 172.16.10.10/32
commit
```

To enable an interface, you must remove the disable statement:
```
delete interfaces lo0 disable
commit
```

To create vlans set the name and the vlan id:
```
set vlans Admin vlan-id 100 description "Admin VLAN"
set vlans Test vlan-id 200 description "Testing VLAN"
set vlans Wireless vlan-id 300 description "Wireless VLAN"
commit

show vlans
```

Set interface as trunk port as follows:
```
set interfaces ge-0/0/1 unit 0 family ethernet-switching port-mode trunk
set interfaces ge-0/0/1 unit 0 family ethernet-switching vlan members Test

set interfaces ge-0/0/1 unit 0 family ethernet-switching vlan members [Admin Wireless]
commit check
```

Set interface as access port as follows:
```
set interfaces ge-0/0/2 unit 0 family ethernet-switching port-mode access
set interfaces ge-0/0/2 unit 0 family ethernet-switching vlan members Test
set interfaces ge-0/0/2 description "Workstation-1"
commit check
```

Set subinterface for port as follows:
```
set interfaces ge-0/1/1 vlan-tagging
set interfaces ge-0/1/1 unit 10 vlan-id 10
set interfaces ge-0/1/1 unit 10 family inet address 10.10.10.10/24
commit check
```

A VLAN IRB interface is the same as an SVI, associates a VLAN with an IP address.

In newer JunOS:
```
set interfaces irb unit 10 family inet address 10.10.10.10/24
set vlans Test 13-interface irb.10
commit check
```

In older JunOS:
```
set interfaces vlan unit 10 family inet address 10.10.10.10/24
set vlans Test 13-interface irb.10
commit check
```

Copy configurations from one interface to another:
```
copy interfaces ge-0/0/2 to ge-0/0/3
```

To replace a pattern:
```
replace pattern Workstation-1 with PC
```
You can use this configuration change with regex, useful for bulk changing IP patterns to avoid conflicts.

[Quick-Reference](https://networkdirection.net/study-notes/introduction-to-juniper/cli-introduction/)