---
layout: post
title:  "GKE forensics 2 - How to Extract and Mount GKE VMDK files"
date:   2020-05-30 18:55:27 +0000
categories: forensics GKE
published: true

---
# GKE forensics 2 - How to Extract and Mount GKE VMDK files

# Introduction

Now you have a .vmdk file  (otherwise you can check the previous article) and an Ubuntu machine, but how to actually do something useful with this?

This guide is the second part of the GKE forensics series. So, you have a snapshot of a GKE node and you have downloaded the result as a .vmdk file.

This article will only explain how to mount and be able to analyse the .vmdk file, the next article explains how to analyse and extract insights from this.

# VMDK format

The .vmdk (Virtual Machine Disk) file is a an image that contains metadata and virtual hard drives that can be used with virtual machines. This was created by VMware and it's mostly used by their hypervisor to store the data of a running virtual machine.

## File Structure

We could use vmware (or other hypervisor) to understand the file. On the other hand there is an easy way out.

We can just extract the files using 7zip. This will "extract" all the partitions inside the vmdk file.

There is 2 important commands related to 7zip.

~~~~~~~~
7zip -l gke-forensics.vmdk
~~~~~~~~

This command will list all the metadata files and hard drives.

- Add output of files

The most important of which is the STATE.img file.

There are also the ROOT and KERNEL files, but these files should be analysed only if the GKE node is deeply compromised. The state.img is the best one to start with.

## Extract partitions

~~~~~~~~
7zip -x gke-forensics.vmdk
~~~~~~~~

This command will extract the files inside the vmdk file.

# Mount partitions

Once you have the files extracted from the .vmdk you will want to mount them in your own file system. This will make the hard drive a normal folder in your filesystem so all files can be read.

~~~~~~~~
Add format to hard drive
~~~~~~~~

This will add the necesary format to the hard drive so we can do the following command,

~~~~~~~~
guestmount
~~~~~~~~

This command has the following operators:

 [`-o  means read only`]

output folder

Finally when you are done analysing the information you can use the following command to unmount the filesystem.\

~~~~~~~~
use the umount the remove the folder
~~~~~~~~

# Conclusions

I hope this guide is useful, there are many ways to access the information in the .vmdk. I have found however this is the easiest.

Now in the next article I will explain how to collect information from the STATE.img file.

[GKE forensics 3 - STATE.img and how to do forensics on a GKE Node ](https://www.notion.so/GKE-forensics-3-STATE-img-and-how-to-do-forensics-on-a-GKE-Node-faeff495d27a4dc4b46ea3adb5ed2426)

# References

- [https://www.nakivo.com/blog/extract-content-vmdk-files-step-step-guide/](https://www.nakivo.com/blog/extract-content-vmdk-files-step-step-guide/)
- [https://www.altaro.com/vmware/extract-content-vmdk-files/](https://www.altaro.com/vmware/extract-content-vmdk-files/)
- [https://www.poftut.com/mount-vm-images-guestmount/](https://www.poftut.com/mount-vm-images-guestmount/)
- [https://linux.die.net/man/1/guestmount](https://linux.die.net/man/1/guestmount)
- [http://manpages.ubuntu.com/manpages/precise/man1/guestmount.1.html](http://manpages.ubuntu.com/manpages/precise/man1/guestmount.1.html)
