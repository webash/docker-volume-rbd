# docker-volume-rbd
Docker volume plugin for ceph rbd.

This plugin uses the official ceph/ceph image with a simple node script as docker volume plugin api endpoint. The node script uses the standard ceph commandline tools to perform the rbd create, map, unmap, remove and mount operations. This release aligns with the Ceph Octopus release (v15.2).

For normal use, setup the /etc/ceph folder on the host and install with:

```
% docker plugin install robkaandorp/rbd:v15.2 RBD_CONF_POOL="rbd"
```

where RBD_CONF_POOL is optional and defaults to "rbd".

Build with or use the build.sh build script (_do not do this on a production system!_):

```
% docker build . -t robkaandorp/rbd:v15.2

% id=$(docker create robkaandorp/rbd:v15.2 true)
% mkdir rootfs
% docker export "$id" | sudo tar -x -C rootfs
% docker rm -vf "$id"
% docker rmi robkaandorp/rbd:v15.2

% docker plugin create robkaandorp/rbd:v15.2 .
% rm -rf rootfs

% docker plugin enable robkaandorp/rbd:v15.2
```

Example of how to create a volume:

```
% docker volume create -d robkaandorp/rbd:v15.2 -o size=150M -o fstype=xfs test2
```

size and fstype are optional and default to 200M and xfs respectively.

In my development setup (hyper-v virtualized ceph and docker nodes), the xfs filesystem gives me better write performance over ext4, read performance is about the same.

**WARNING**: do _NOT_ mount a volume on multiple hosts at the same time to prevent filesystem corruption!