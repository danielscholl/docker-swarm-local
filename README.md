# docker-swarm-local

This is a Vagrant environment using `VirtualBox` with REX-Ray `Virtual Media` used
as a persistent volume store for a docker swarm.  This solution can be used as a quick 
way to get started and learn how a docker swarm works with _persistant storage_ capability.

Requirements
- Vagrant installed
- VirtualBox 5.1.26+
- Internet connection
- Python 2 or 3 _* 'See OS Modifications'_

## Installation
### Clone the repo

```
git clone https://github.com/danielscholl/docker-swarm-local
cd docker-swarm-local
vagrant up
```

### VirtualBox SOAP Web Service

There are two suggested steps to prepare the `VirtualBox` host OS to be able to
receive communication from the guests that are running REX-Ray.  

1. Remove authentication
 - `VBoxManage setproperty websrvauthlibrary null`
2. Start the web service in the background using the following command. 
 - `vboxwebsrv -H 0.0.0.0 -v > /dev/null 2>&1 &`

>NOTE: To shutdown forground the process and kill it.  `fg`

### OS Specific Modifications

This solution requires python on the localhost and the location needs to be specified.
File: playbooks/roles/reboot-server/tasks/main.yml
Default: ansible_python_interpreter: "/usr/local/bin/python"

## Usage
When the Vagrant environment is up and running, you can run `vagrant ssh node1` to get into the VM.  Docker volumes should now be functioning using REX-Ray to create and remove volumes as needed. By default Volumes will be created in a directory called `Volumes` in the root directory of the repo.  

## Options
By default there are 3 nodes (2 managers and 1 worker) configured in the Vagrant file. To modify the number of worker nodes modify the section required and categorize the node into the proper ansible group.

```ruby
  config.vm.define "node(x)" do |config|
    config.vm.hostname = "node(x)"
    config.vm.network "private_network", ip: "10.0.7.(x)"
  end
```

>Note: Server reboots can sometimes cause the vagrant provisioning to time out. Run `vagrant provision` to reprovision ansible scripts.

## REX-Ray
Consult the full REX-Ray documentation [here](http://rexray.readthedocs.org/en/stable/).

List Volumes:

`sudo rexray volume ls`

Create a new volume:

`sudo rexray volume create --size=1 testVolume`

Delete a volume:

`sudo rexray volume rm b4219d9e-c835-431f-8ed8-a7f4a3838ddf`

>Caution: VirtualBox also exposes all OS vhd's as Volumes

## Docker
Consult the Docker documentation [here](https://docs.docker.com/engine/admin/volumes/volumes/#choosing-the--v-or-mount-flag).  

You can also create volumes directly from the Docker CLI.  The following command created a volume of `1GB` size with name of `testVolume`.

```
docker volume create --driver=rexray --opt=size=1 testVolume
```

Start a new container with a REX-Ray volume attached, and detach the volume when the container stops:

```
docker run -it --volume-driver=rexray -v testVolume:/data busybox /bin/sh
# ls /
bin   dev   etc   home  proc  root  sys   *data*  tmp   usr   var
# exit
```

>Note: Volumes are exposed across all nodes in the swarm.

