juju-dev-box
============


# Overview
A Vagrant self contained juju development environment for developing your charms.

It also contains:

* A bootstrapped local environment
* Juju GUI accessible locally through your browser
    - https://juju.ubuntu.com/docs/howto-gui-management.html
* Charms tools
    - https://juju.ubuntu.com/docs/tools-charm-tools.html
* Charms helpers
* Amulet, a testing harness (soon..)
    - https://juju.ubuntu.com/docs/tools-amulet.html


## Requirements

* A VM provider: VirtualBox (only one supported at the moment)
    - https://www.virtualbox.org/wiki/Downloads
* Vagrant
    - http://www.vagrantup.com/downloads.html

## Installation

### Clone this repo

```
git clone https://github.com/Altoros/juju-dev-box.git
cd cf-vagrant-installer
```

### Provision The VM

```
vagrant up
```

## Test your VM

### juju environment
SSH into the VM
> juju-gui takes some time to deploy so your output may differ

```
vagrant ssh
...
...
vagrant@cf-juju:~$ juju status
environment: local
machines:
  "0":
    agent-state: started
    agent-version: 1.17.4.1
    dns-name: localhost
    instance-id: localhost
    series: trusty
  "1":
    agent-state: started
    agent-version: 1.17.4.1
    dns-name: 10.0.3.170
    instance-id: vagrant-local-machine-1
    series: precise
    hardware: arch=amd64
services:
  juju-gui:
    charm: cs:precise/juju-gui-85
    exposed: true
    units:
      juju-gui/0:
        agent-state: started
        agent-version: 1.17.4.1
        machine: "1"
        open-ports:
        - 80/tcp
        - 443/tcp
        public-address: 10.0.3.170
```

### Juju GUI
As you can see above, the GUI has been deployed and is listening on http://10.0.3.170
This is the LXC container assigned IP and won't be accessible from outside the VM.
In order to enable that run the following script from the Host machine. It may require your root password

```
cd juju-dev-box
./scripts/add-route
```

You should be able to load the Juju GUI from your favourite browser

> What is the password???
```
vagrant ssh
vagrant@cf-juju:~$ cat .juju/environments/local.jenv | grep admin-secret
  admin-secret: 702488bcacfb062b3119c6a415f41e0a
```

## Troubleshooting

#### Services stay in 'pending' status forever
> You have to be inside the VM (vagrant ssh)

This may be for two things:
- Container image is been download. You can check it:

```
ps ax | grep wget
```
If you get an output then grab a coffee/tea/mate and wait

- Firewall is still enabled
Disable it, destroy environment and re-bootstrap it

```
sudo ufw disable
juju destroy-environment local
juju bootstrap
```

Redeploy for example Juju GUI

```
juju deploy juju-gui
juju expose juju-gui
```





