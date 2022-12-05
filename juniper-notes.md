JunOS uses FreeBSD under the hood so some Unix commands are available like the pipe operator and grep etc.

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

To enable an interface, you must remove the disable statement:
```
delete interfaces lo0 disable
commit
```

[Quick-Reference](https://networkdirection.net/study-notes/introduction-to-juniper/cli-introduction/)