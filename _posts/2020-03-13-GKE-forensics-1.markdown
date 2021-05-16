---
layout: post
title:  "GKE forensics 1 - How to take a snapshot"
date:   2020-03-13 18:55:27 +0000
categories: forensics GKE
published: true

---


This is a blog post as part of a series in how to collect and analyze GKE nodes in case of an incident. You can collect the virtual disk of a GKE node and analyze the data that contains this.

This guide only explains how to collect the filesystem but an extension to doing forensics on nodes would be to collect the memory.

# GKE Node snapshot collection

## What is a GKE Node?

A GKE (Google Kubernetes Engine) nodes are the machine in which pods and containers will run when you create a Kubernetes cluster with the GCP Kubernetes managed service.

This node are bare bones but are based on a linux system, the most important details you will find in these systems are logs (journal) and the docker repositories and container metadata. This can be useful if you are investigating if a pod has been breached and illicit pods have been spun out.

## Create Snapshot from GKE node

The first step of this will be to create a snapshot from the GKE node (Which will appear as an VM instance). You need to go to [`Compute Engine > Storage > Snapshots`](https://console.cloud.google.com/compute/snapshots)

In this view you need to go to `CREATE SNAPSHOT`

![Screenshot_from_2021-03-13_15-31-27.png](/images/a6882c7d55f649ae826f200e4d352d0c/Screenshot_from_2021-03-13_15-31-27.png)

Once in `CREATE SNAPSHOT` you need to select the disk from a GKE virtual machine.
<p align="center">
 <img src="/images/a6882c7d55f649ae826f200e4d352d0c/Untitled.png" alt="Create Snapshot"/>
</p>

This process will create a snapshot, this can be used to create a new machine, but there is no way of extracting a snapshot from GCP.

## Create Image from Snapshot

To be able to extract the information you will need to turn the snapshot into an image. Images can be used to create completely new virtual machines.

Moreover, Images can be exported. Which is not the case with snapshots.

You need to go to the [`Compute Engine > Storage > Images`](https://console.cloud.google.com/compute/images)

![Screenshot_from_2021-03-13_15-31-54.png](/images/a6882c7d55f649ae826f200e4d352d0c/Screenshot_from_2021-03-13_15-31-54.png)

There you need to `CREATE IMAGE`

![Screenshot_from_2021-03-13_15-32-29.png](/images/a6882c7d55f649ae826f200e4d352d0c/Screenshot_from_2021-03-13_15-32-29.png)

Give a clear name and use the source snapshot the GKE node you want to analyze.

The image once is complete will look like this.

![Screenshot_from_2021-03-13_15-32-57.png](/images/a6882c7d55f649ae826f200e4d352d0c/Screenshot_from_2021-03-13_15-32-57.png)

Take note of the `EXPORT` function, This will be used to move the image to a bucket. (If you have already a bucket to store the exported files you can skip the next chapter)

## Create new Bucket

You still cannot download the image directly, you need to store it into a bucket. To create one you need to go to [`Storage > Browser`](https://console.cloud.google.com/storage/browser)

![Screenshot_from_2021-03-13_15-36-55.png](/images/a6882c7d55f649ae826f200e4d352d0c/Screenshot_from_2021-03-13_15-36-55.png)

Here you can see `CREATE BUCKET`. Make sure it's on the region closes to your operations and also it should only be accessible by the analysts that need access to the forensic images.

The images shouldn't contain secrets but if the docker images are configured improperly. Proprietary information could be present.

<p align="center">
 <img src="/images/a6882c7d55f649ae826f200e4d352d0c/Screenshot_from_2021-03-13_15-37-48.png" alt="Screenshot_from_2021-03-13_15-37-48.png"/>
</p>

## Move Image to Bucket

The last step is to move the image to a bucket, so it can be imported.

On oath you will see all the available bucket. Use the one create for the forensic analysis.

![Screenshot_from_2021-03-13_15-33-40.png](/images/a6882c7d55f649ae826f200e4d352d0c/Screenshot_from_2021-03-13_15-33-40.png)

This process will take a while but after it's completed you will find the file in your bucket.

![Untitled1.png](/images/a6882c7d55f649ae826f200e4d352d0c/Untitled1.png)

From here you can download the file into your filesystem and initiate the investigation.

# Conclusions

**This process can be used for any virtual machine in GCP, not only GKE.** Collecting a file that can be used for forensics is not a straightforward process but with this mini guide you should be able to do it without much problem.

I will see you in the next piece of this guide, in which we will learn to extract and mount the VMDK file created in the guide.

[GKE forensics 2 - How to Extract and Mount GKE VMDK files](https://www.notion.so/GKE-forensics-2-How-to-Extract-and-Mount-GKE-VMDK-files-1b0a1d9ae98d4cc3a4b7506dbe38dc99)

# References

[https://cloud.google.com/compute/docs/disks/create-snapshots](https://cloud.google.com/compute/docs/disks/create-snapshots)[https://cloud.google.com/compute/docs/images/export-image](https://cloud.google.com/compute/docs/images/export-image)

[https://cloud.google.com/kubernetes-engine/docs/concepts/security-overview](https://cloud.google.com/kubernetes-engine/docs/concepts/security-overview)

[https://cloud.google.com/blog/products/containers-kubernetes](https://cloud.google.com/blog/products/containers-kubernetes)

[https://cloud.google.com/blog/products/gcp/introducing-google-container-engine-gke-node-pools](https://cloud.google.com/blog/products/gcp/introducing-google-container-engine-gke-node-pools)