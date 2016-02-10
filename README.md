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

- cp3
init a guest os
```
vagrant init -m ubuntu/trusty32
```
-m :minimal Vagrantfile will be generated
-f : will rewrite Vagrantfile if already exists


- cp4
download file, two syntax
```
$ curl [URL] -o [FILENAME]
$ curl http://xxx.box > xxx.box
$ wget -O [FILENAME] [URL]
```

- cp5
make first box:  
How to package:  
```
vagrant package --output name.box
```
(Can be used for running or halted. If running, it will be shut down.)  


list all boxes:  
```
ls -la $VAGRANT_HOME/.vagrant.d/boxes/
```
Download box
```
vagrant box add [NAME] [ADDRESS]       //address may be http://, file:///
```
Ex:
```
vagrant box add first-box first-box.box
```

jekyll: build first page
```
jekyll new -f .
jekyll build
jekyll serve -H 0.0.0.0 --detach
```
Repackaging (first,delete old version box)

syntax: vagrant box repackage NAME PROVIDER VERSION  
```
vagrant box repackage boxname
```





