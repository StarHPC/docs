---
sort: 1
---

# About Star

## Overview

The Star cluster is a High-Performance Computing (HPC) system at the Science and Innovation Center (SIC) that is designed for a variety of advanced research and computational tasks. It combines NVIDIA HGX-based **compute nodes**, a high-speed all-flash parallel file system-based **storage system**, an ultra-high-throughput/low-latency HDR200Gb/s Infiniband **network fabric**, and a suite of **software applications**. The compute nodes feature high-end H100 and A100 GPUs, AMD EPYC and Intel Xeon processors, and over 7 Terabytes of combined RAM.

The cluster runs SLURM (Simple Linux Utility for Resource Management), a job scheduler and queueing system that efficiently allocates the cluster's resources to manage competing resource demands.

Users run many different applications on the cluster based on their needs, such as Python projects via Jupyter Notebooks, OpenMPI-based parallel jobs, NetCDF (often used to manage large datasets in climatology, meteorology, oceanography, and GIS applications). Programs are run directly on the hardware (bare-metal) to maximize performance and minimize overhead.

Containerization is also increasingly popular in HPC it provides isolated environments that allow for the reuse of images for better reproducibility and software portability without the performance impact of other methods or the hastle of manualy installing dependencies. Containers are run using Apptainer (formerly Singularity), a containerization platform similar to Docker with the major difference that it runs under user privileges instead of `root`. Users can deploy images from NGC (NVIDIA GPU Cloud), which provides access to a wide array of pre-built images with GPU-optimized software for diverse applications. Leveraging container images can save a lot of time as users don’t need to set up the software applications from scratch and can just pull and use the NGC images with Apptainer.


## Hardware

### Login Node

- IBM System x3550 with 128GB RAM
- DL325 Gen10+ with an 8-core EPYC processor, 128GB RAM
- DL385 Gen10+ v2 with 2x AMD 32-core EPYC processors, 256GB RAM

### Compute Nodes

{%comment%}
- 1x HPE DL385 Gen10+ v2 node with 2x NVIDIA A30 24GB PCIe GPUs, 2x AMD EPYC 32-core processors, 256GiB DDR4 RAM
- 2x HPE XL675d Gen10+ nodes (Apollo 6500 Gen10+ chassis), each with 8x NVIDIA A100 80GB SXM GPUs, 1TiB 3200 DDR4 RAM, 2x 480GB SSD
- 2x HPE DL380a Gen11, each with 2x NVIDIA H100 80GB PCIe GPUs, 2x Intel Xeon 32-core processors, 512GiB DDR5 RAM
- 2x Cray XD665 nodes, each with 4x NVIDIA H100 80GB SXM GPUs, 2x AMD EPYC 32-core processors, 768GiB DDR5 RAM
- 1x Cray XD670 node with 8x NVIDIA H100 80GB SXM GPUs, 2x Intel Xeon 32-core processors, 2TiB DDR5 RAM
{%endcomment%}

#### HPE Apollo 6500 Gen10

| Attribute\Node Name           | gpu1 and gpu2                                                           |
| ----------------------------- | -------------------------------------------------------------- |
| Model Name                    | HPE ProLiant XL675d Gen10 Plus; Apollo 6500 Gen10 Plus Chassis |
| Processors                 | AMD EPYC 7513                                               |
| Sockets                       | 2                                                              |
| Cores per Socket              | 32                                                             |
| Threads per Core              | 2                                                              |
| Memory                        | 1024 GiB Total Memory (16 x 64GiB DIMM DDR4)                   |
| GPU                           | 8 SXM NVIDIA A100s                                             |
| Local Storage (Scratch space) | 6.4TB (5.8TiB) SSD                                                          |

#### HPE DL385 Gen10

| Attribute\Node Name           | cn01                                       |
| ----------------------------- | ------------------------------------------ |
| Model Name                    | HPE ProLiant DL385 Gen10 Plus v2           |
| Processors                  | AMD EPYC 7513 32-Core Processor            |
| Sockets                       | 2                                          |
| Cores per Socket              | 32                                         |
| Threads per Core              | 2                                          |
| Memory                        | 256GiB Total Memory (16 x 16GiB DIMM DDR4) |
| GPU                           | 2 SXM NVIDIA A30s                          |
| Local Scratch | None                                       |

#### HPE DL380a Gen11

| Attribute\Node Name           | gpu3 and gpu4                             |
| ----------------------------- | -------------------------------------------- |
| Model Name                    | HPE DL380a Gen11                             |
| Processors              | 64 Physical cores / 128 Logical Cores (2 x Intel Xeon-P 8462Y+ @ 2.8GHz)  |
| Memory                        | 512 GiB DDR5 RAM                             |
| GPU                           | 2 NVIDIA H100 80GB GPUs (NVAIE subscription) |
| Network                       | 4-port GbE, 1-port HDR200 InfiniBand         |
| Local Storage (Scratch Space) | None                                    |

#### Cray XD665 Nodes

| Attribute\Node Name           | gpu5 and gpu6                      |
| ----------------------------- | -------------------------------------- |
| Model Name                    | Cray XD665                             |
| Processors  | 64 Physical cores / 128 Logical Cores (2 x AMD EPYC Genoa 9334 @ 2.7GHz)                  |
| Memory                        | 768 GiB DDR5 RAM                       |
| GPU                           | 4 NVIDIA HGX H100 80GB SXM GPUs        |
| Network                       | 2-port 10GbE, 1-port HDR200 InfiniBand |
| Local Storage (Scratch Space) | None                          |

#### Cray XD670 Node

| Attribute\Node Name           | gpu7                                |
| ----------------------------- | -------------------------------------- |
| Model Name                    | Cray XD670                             |
| Processors  | 64 Physical cores / 128 Logical Cores (2 x Intel Xeon-P 8462Y+ @ 2.8GHz)                  |
| Memory                        | 2048 GiB DDR5 RAM                      |
| GPU                           | 8 NVIDIA HGX H100 80GB SXM GPUs        |
| Network                       | 2-port 10GbE, 1-port HDR200 InfiniBand |
| Local Storage (Scratch Space) | None                                |

### Storage System

Our storage system contains of four HPE PFSS nodes, collectively offering a total of 63TB of storage. You can think of these four nodes as one unified 63TB storage unit as it is a **Parallel File System Storage** component. These nodes work in parallel and are all mounted under **one** mount point on the gpu nodes only (`/fs1`).

## Our vision

Making complex and time-intensive calculations simple and accessible.

### Our Goal

Our heart is set on creating a community where our cluster is a symbol of collaboration and discovery. We are wishing to provide a supportive space where researchers and students can express their scientific ideas and explore unchanted areas. We aim to make the complicated world of computational research a shared path of growth, learning, and significant discoveries for the ones that are eager to learn.

## Operations Team

- Alexander Rosenberg
- Mani Tofigh

## The Board

- Edward H. Currie
- Daniel P. Miller
- Adam C. Durst
- Jason D. Williams
- Thomas G. Re
- Oren Segal
- John Ortega
