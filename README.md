#### approvagrant

- cp1

host os->physical machine.
guest os->vm

- cp2
```
vagrant global-status  //show all guest vm runnning on host machine.
```
to destroy vm, to ways
```
vagrant destroy  //must in folder
vagrant destroy 12345  //can be in any folder
```

if you shutdown a vm in virtubox, need to run
```
vagrant global-status --prune
```

port forwarding (note the order)
```
config.vm.network :forwarded_port, guest: 4567, host: 45670, host_ip: "127.0.0.1"
```
in boot up,
```
default: Forwarding ports...
default: 4567 => 45670 (adapter 1)
```
Then run 
```
vagrant status
```
to check guest os status.
