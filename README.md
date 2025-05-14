# ROCm-setup-tutorial

Welcome to my setup tutorial, everything explained here is how I got ROCm set up on my [device](#my-specs-and-os) 
You may want to have some washing ready to fold while waiting for the image download

## Table of Contents

- [Introduction](#introduction)
- [Setting up the BIOS](#setting-up-the-bios)
- [Installing docker](#installing-docker)
- [Downloading ROCm and pytorch image](#dowloading-rocm-and-pytorch-image)
- [Managing the docker containers](#managing-the-docker-containers)

## Introduction

### *ALWAYS MAKE A BACKUP OF ANY IMPORTANT DATA BEFORE STARTING*

I performed this on a clean install of Ubuntu 24.04 however this should work on both 24.04 and 22.04 and shouldn't require a fresh install. However, I would recommend if issues arise to use a clean install since this is what worked for me and should prevent any potential data loss.
It is possible that this won't work for everyone however this should work with any [gfx1100 gpu](#gfx1100-list) or [gfx1030 gpu](#gfx1030-list). Bear in mind I have only tested this on 7900xtx so your mileage may vary

## Setting up the BIOS

To enter your BIOS search you board manufacturer's BIOS key and press that key repeatedly on startup until you enter into your bios.
Inside of your BIOS you want to look for your igpu (integrated graphics) if you have one and disable it. This will help avoid any conflicts

## Installing docker

### What is docker?

When you run an image file through docker you are essentially running another OS (operating system) inside of your own as a virtual environment. This means anything you install inside that docker will be separate from your main machine. 
So for example if you accidentally install something that breaks the docker or stops it from working correctly, you can delete the docker and create a new one from the same original image and start again. An image is a snapshot of the OS with everything it has installed such as the required python libraries and everything needed, in our case to run ROCm and pytorch

### 1. Ensure system is up to date

Open a terminal by searching terminal or pressing Ctrl+ALT+T
Run these commands 1 after another
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install docker

```bash
sudo apt install docker.io -y
```

### 3. Start and enable docker

```bash
sudo systemctl enable --now docker
```

### 4. Verify installation

```bash
docker --version
```

This should output something similar to: 
```bash
Docker version 26.1.3, build 26.1.3-0ubuntu1~24.04.1+esm1
```

Docker should now be set up on your pc and ready to download the image

## Downloading ROCm and pytorch image

### 1. Downloading the image

```bash
docker pull rocm/pytorch:latest
```

Go fold your washing and come back when the download has installed.

### 2. Starting the docker container

You can change the name of your container to be what you want it to be, you should also change the path of your folder you want the docker to have access to

```bash
docker run -it \
  --name my_rocm_container \                # Name your container
  -v /home/your_username/Desktop/my_folder:/workspace \  # Mount desktop folder for the container to have access to
  --device=/dev/kfd --device=/dev/dri \     # GPU access
  --group-add=video \                       # Required for ROCm
  rocm/pytorch:latest \                     # Image to use
  bash                                      # Launch shell
```

## Managing the docker containers

### Exiting the container

While inside of the container enter: 

```bash
exit
```

This will exit the docker container, saving its state and closing it down for reopening later

### Restarting the container

From outside the container in a terminal enter:

```bash
docker start -ai my_rocm_container # Replace this with the name of your container
cd /workspace
```

This opens the container and navigates directly into it's workspace, from there you can navigate into whatever directory the container shares with your main OS

### List all docker containers

This will list any docker containers that you have, running or stopped

```bash
docker ps -a
```

### Deleting a docker container

First you should [exit](#exiting-the-container) the container, and [list](#list-all-docker-containers) all containers, then enter:

```bash
docker rm my_docker_container
```

Just change the name of the container to the name of your actual container

### Deleting unused images

If you have any images that you downloaded and no longer want to use, as in no containers are based off of that image, to remove it you can enter:

```bash
docker image prune -a
```



## My Specs and OS

- RX 7900 xtx
- Ryzen 9 7950x
- 32gb ddr5, cl36, 5200
- MSI B650-S WIFI
- 2Tb SSD + 1Tb SSD
- Ubuntu (Ubuntu 24.04.2 LTS)
- [Back to top](#rocm-setup-tutorial)

## gfx1100 list

- 7900 XTX
- 7900 XT
- 7900 GRE
- PRO W7900 Dual Slot
- PRO W7900
- PRO W7800 48GB
- PRO W7800
- [Back to introduction](#introduction)

## gfx1030 list

- 6900 XT
- 6800 XT
- 6800
- PRO W6800
- PRO V620
- [Back to introduction](#introduction)
