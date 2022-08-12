# Docker Storage

There are two basic storage concepts in Docker.  

- Storage Drivers
- Volume Drivers

## Docker Volumes

Docker stores data in `/var/lib/docker`

```bash
# Docker folder structure
/var/lib/doocker
  aufs
  condainers
  image
  volumes  
```

### Volume Mounting

Here we create a Docker volume and mount it into a container

```bash
# Create a Docker volume.
# Note: if you mount a volume that does not exist Docker will auto-create it for you.
docker volume create data_volume

# This creates a folder in
# /var/lib/docker/volumes/data_volume

# Mounting the volume in Docker
docker run -v data_volume:/var/lib/mysql mysql
```

### Bind Mounting

We can also use a directory on the host instead of a Docker volume.  This directory could be another mounted volume.

```bash
docker run -v /data/mysql:/var/lib/mysql mysql
```

### New Way of Mounting Volumes

The `-v` parameter is the old way of mounting volumes.  The `--mount` parameter is now the preferred way of doing it.

```bash
docker run \
  --mount type=bind,source=/data/mysql,target=/var/lib/mysql \
  mysql
```

## Storage Drivers

Docker uses storage drivers to enable all of the volume features such as layering, copy on write, etc.  Some supported storage drivers are:

- AUFS
- ZFS
- BTRFS
- Device Mapper
- Overlay
- Overlay2

## Volume Drivers

- local: Used by Docker to create volumes in /var/lib/docker/volumes/
- Azure File Storage
- Convoy
- Digital Ocean Block Storage
- Flocker
- gce-docker
- GluserFS
- NetApp
- RexRay
- Portworx
- VMware vSphere Storage
- ....

Choosing a volume driver during runtime.

```bash
# This uses rexray to provision a driver in AWS EBS
docker run -it \
  --volume-driver rexray/ebs \
  --mount src=ebs-vol,target=/var/lib/mysql
  mysql
```