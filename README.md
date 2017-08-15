# docker-swarm-local

This is a Vagrant environment using `VirtualBox` with REX-Ray `Virtual Media`
as  persistent storage for a docker swarm.  This can be used as a quick way
to get started and learn how a docker swarm works with persistant storage enabled.

Requirements
- Vagrant installed
- VirtualBox 5.1.26+
- Internet connection

## Installation
### VirtualBox SOAP Web Service

There are two suggested steps to prepare the `VirtualBox` host OS to be able to
receive communication from the guests that are running REX-Ray.  

1. Remove authentication
 - `VBoxManage setproperty websrvauthlibrary null`
2. Start the web service in the background using the following command. 
 - `vboxwebsrv -H 0.0.0.0 -v >dev/null 2>&1 &`

>NOTE: Forground the process and kill it when done.  `fg`

### Clone the repo
```
git clone https://github.com/danielscholl/docker-swarm-local
cd docker-swarm-local
vagrant up
```

## Usage
When the Vagrant environment is up and running, you can run `vagrant ssh manager1`
to get into the VM.  Docker volumes should now be functioning using REX-Ray to create
and remove volumes as needed. By default Volumes will be created in a directory called
`Volumes` in the root directory of the repo.  

## Options
There are optional fields in the `Vagrantfile` that can be modified to adjust the
number of servers, as well as cpu and memory of those servers.

- Determine how many managers to start
 - `nummanagers`
- Determine how many workers to start
 - `nummanagers`
- Determine how many cpus to allocate to the servers
 - `cpu`
- Determine how much memory to allocate to the servers
 - `cpu`

>Note: Sometimes provisioning of machines with low host memory will cause the vagrant provisioning to time out. Run `vagrant provision` to reprovision ansible scripts.

## REX-Ray
Consult the full REX-Ray documentation [here](http://rexray.readthedocs.org/en/stable/).

List Volumes:

`rexray volume ls`

Create a new volume:

`rexray volume create --size=1 testVolume`

Delete a volume:

`rexray volume rm b4219d9e-c835-431f-8ed8-a7f4a3838ddf`

>Caution: VirtualBox also exposes the OS vhd's as Volumes

## Docker
Consult the Docker documentation [here](https://docs.docker.com/engine/admin/volumes/volumes/#choosing-the--v-or-mount-flag).  

You can also create volumes directly from the Docker CLI.  The following command created
a volume of `1GB` size with name of `testVolume`.

```
sudo docker volume create --driver=rexray --opt=size=1 testVolume
```

Start a new container with a REX-Ray volume attached, and detach the volume when the
container stops:

```
docker run -it --volume-driver=rexray -v testVolume:/data busybox /bin/sh
# ls /
bin   dev   etc   home  proc  root  sys   *data*  tmp   usr   var
# exit
```

>Note: Volumes are exposed across all nodes in the swarm.

