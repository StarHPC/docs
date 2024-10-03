---
sort: 2
---

# Quick Start Guide

## Account Access

A Star HPC account is required to access and submit jobs to the Star HPC cluster.

The application process may require justification of the need for HPC resources, detailing the kind of work you intend to do, the resources you expect to use, and sometimes, the anticipated outcomes of your research.

Access to the cluster comes with responsibilities and certain privileges. Your account comes the responsibility to use the resources wisely and efficiently, to respect the shared nature of the environment, and to contribute to the overall HPC community.

Users should understand the policies on data privacy and user responsibilities.

## Getting started on the cluster (account, quota, password)

Before you start using the Star cluster, please read Hofstra University's [Acceptable Use Guidelines](http://www.hofstra.edu/scs/aug).

A general introduction to the Star HPC cluster, research community, and support group be found at [starhpc.hofstra.io](https://starhpc.hofstra.io).

To be able to work on the Star cluster, you must have an approved account on the cluster.

## How to get an account on Star

Members of Hofstra University, Nassau Community College, or Adelphi University, and other researchers affiliated with these institutions in some way may apply for an account.

### Requesting an account

To get an account on Star, you need to complete the [Star HPC Cluster account request form](https://forms.gle/7gNgp4yqaC7cw2ko7).

## Login node

Access to the cluster is provided through SSH access to the login node. The login node serves as the gateway or entry point to the cluster. Note that most software tools are not available on the login node and it is not for prototyping, building software, or running computationally intensive tasks itself. Instead, the login node is specifically for accessing the cluster and performing only very basic tasks, such as copying and moving files, submitting jobs, and checking the status of existing jobs. For development tasks, you would use one of the development nodes, which are accessed the same way as the large compute nodes. The compute nodes are where all the actual computational work is performed. They are accessed by launching jobs through Slurm with `sbatch` or `srun`.

## Scheduler policies

Users should understand the cluster's policies regarding CPU and GPU usage, including time limits and priority settings.

Users are advised to learn how to check their usage statistics to manage the resource allocation efficiently.

## Storage policies

Storage quotas and usage limits are put in place to ensure fair use and equitable distribution of the resources among all users.

It is important to know where to store different types of data (such as large datasets or temporary files).

Your home directory provides a limited amount of storage for scripts, source code, and small datasets.

Project-specific directories may be created upon request for shared storage among multiple accounts.

## Further Reading

To make proper use of the cluster, please familiarize yourself with the basics of using Slurm, fundamental HPC concepts, and the cluster's architecture.

You may be familiar with the `.bashrc`, `.bash_profile`, or `.cshrc` files for environment customization. To support different environments needed for different software packages, [environment modules]({{site.baseurl}}{% link software/env-modules.md %}) are used. Modules allow you to load and unload various software environments tailored to your computational tasks.

