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

- cp6
provisioning, some possible usage:
```
config.vm.provision "PROVISIONER"
config.vm.provision "shell", path: "script.sh"
config.vm.provision "ansible", playbook: "playbook.yml"
config.vm.provision "shell", inline: "echo abc"
```
By default, provisioning is executed only during the first run of $ vagrant up  
If you halt the system using $ vagrant halt or $ vagrant suspend, the next run of $ vagrant up will skip the provisioners.  
```
vagrant up --provision
vagrant up --no-provision (turn off provisioners even already executed)
```

create a sh file(in host machine)  
```
#!/usr/bin/env bash
echo "Installing: nodejs, lynx, ruby and jekyll"
apt-get update -y >>/tmp/provision-script.log 2>&1
apt-get install nodejs -y >>/tmp/provision-script.log 2>&1
apt-get install lynx-cur -y >>/tmp/provision-script.log 2>&1
apt-get install ruby1.9.1-dev -y >>/tmp/provision-script.log 2>&1
gem install jekyll >>/tmp/provision-script.log 2>&1
```
assign provisioner  
```
vagrant up --provision-with x, y, z
vagrant up --provision-with shell
```

You can boot up guest os first by no provisioning  
```
vagrant up --no-provision
```
then
```
vagrant provision
```
same on reload:
```
vagrant reload --provision
vagrant reload --no-provision
vagrant reload --provision-with shell
```

inline script
```
Vagrant.configure(2) do |config|
  config.vm.box = "vagrant"
  config.vm.network :forwarded_port, guest: ......

$script = <<SCRIPT

cd /vagrant
jekyll serve -H 0.0.0.0 --detach

SCRIPT

  config.vm.provision "shell", inline: $script, run: "always"

end
```

solve “not a tty” Problem  
delete 'mesg n' in `/root/.profile`  
```
vagrant up
vagrant ssh
sudo sed -i "/mesg n/d" /root/.profile
```


Create ansible provisioner
```
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```
- cp8
vm config
```
config.vm.provider "virtualbox" do |v|
    v.name = "my-project"
    v.gui = true
    v.memory = 4096
    v.cpus = 2
    v.customize ["modifyvm", :id, "--cpus", "2"]  #same as above
    v.customize ["modifyvm", :id, "--cpuexecutioncap", "50"]
end
```
package a iso file to box(?)
```
vagrant package --base my--project
```
run with debug mode:
```
vagrant up --debug
```
check guest ram
```
free m
cat /proc/meminfo
sudo lshw
```
memory test:
```
/bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
/sbin/mkswap /var/swap.1
/sbin/swapon /var/swap.1
```

config guest machine's hostname
```ruby
Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty32"
  config.vm.hostname = "www.example.com"
  config.vm.post_up_message = "Finished"
  config.vm.boot_timeout = 1  #boot time. if exceeds,give prompt
  config.vm. graceful_halt_timeout = 5 #time before halt
  
  config.ssh.username
  config.ssh.password
  config.ssh.host
  config.ssh.port
  config.ssh.private_key_path
  config.ssh.insert_key = false #for public
  
  config.vm.box_download_checksum_type = "sha256"
  config.vm.box_download_checksum = "01234567890"
end
```
check using ``` hostname -f```  
And change host os hostfile
```
127.0.0.1 www.example.com
```
Get checksum  
```
# OS X
shasum -a 256 filename.box
# On Windows
openssl dgst -sha256 filename.box
```
Add box with checksum
```
vagrant box add boxname --checksum-type sha256 --checksum 123456 <link>
```

port collision.  
Fix 
```
config.vm.network :forwarded_port, guest: 80, host: 8800, auto_correct: true #if 8800 already used, may forward to 2201
config.vm.usable_port_range = (2200..2250)  #defaults to 2200..2250.
```

- cp10
multi machine management  
Ex:  
```ruby
Vagrant.configure(2) do |config|
  config.vm.define "web" do |web|
    web.vm.box = "chef/centos-6.5-i386"
    web.ssh.insert_key = false
    web.vm.provider "virtualbox" do |v|
      v.memory = 1024
    end
  end

  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/trusty32"
  end
end
```

To start individual:
```
vagrant up web
```
define primary machine:
```
config.vm.define "web", primary: true do |web|
```
