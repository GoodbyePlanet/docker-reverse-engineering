---
author: ProductDock conference - speaker -> Nemanja Vasic / 
paging: Slide %d / %d
---

---

# INTRODUCTION

What is idea behind this topic ?

## Short overview 

* Insights into how Docker works behid the scene
* Where Docker keeps images
* Showcase on how to reverse engineer Docker image

---

# Docker image

Docker image is set of tar files organized as layers (layers are created based on commands that we specify in Dockerfile) which are overlayed over
each other. 

Docker image consists out of three components:

* `manifest.json` which is JSON-represented description of layers and
and `config.json`

* `config.json` contains metadata necessary for running container, like Docker version,
environment variables, mounts, size, etc...

* `layers` these are OFS layers which are named as hashes calculated out of layer content,
and when merged they form RootFS of the container (mount point for contianers).

---

# How Docker works behid the scene ?

## OverlayFS

In computing, OverlayFS is a union mount filesystem implementation for Linux. It combines multiple different underlying mount points
   into one, resulting in single directory structure that contains underlying files and sub-directories from all sources. Common
   applications overlay a read/write partition over a read-only partition, such as with LiveCDs and IoT devices with limited flash memory
   write cycles.

   Source Wiki https://en.wikipedia.org/wiki/OverlayFS


Overlay File System is the storage driver for Docker and it is well-suited for containers.

```docker info | grep "Storage Driver"```

Way to allow operations such as delete create, update, delete files on system
while not changing lower level (base) filesystem.

Docker uses OverlayFS to combine two filesystems (lower and upper) into one filesystem
by overlaying them on top of the other.
* `lower` is read-only filesystem and it is base filesystem.

* `upper` reflects any changes made to the `lower` filesystem while
leaving it unchanged.

Union of those two filesystems creates merged directory (RootFS)  which is effectively the containers mount point.

## Where Docker keeps images and all content ?

Have you ever asked yourself where Docker keeps images and all other content ?

To investigate Docker root directory

```bash
docker info | grep "Docker Root Dir"
```

```/var/lib/docker/image/overlay2```

```bash
cd ~/Library/Containers/com.docker.docker/Data/vms/0
```

To enter shell of the Docker VM

```bash
docker run -it --privileged --pid=host debian nsenter -t 1 -m -u -n -i sh
```

### Reverse engineering

To start with reverse engineering we will need to first download the image tar.
We can do that with ```docker save <image-name>``` command.

To unpack folder use the following command ```tar xvf <name>.tar```


