---
sort: 3
---

# Quickstart

## How to SSH to the cluster

To access the cluster, connect to the login node as follows:

```bash
ssh -J USERNAME@adams204XX@hofstra.edu:5010 -p 5010 USERNAME@binary.star.hofstra.edu
```

where `USERNAME` is replaced with your username or h700 ID, and `XX` is replaced with a number from 1 to 30 (inclusive).

If you recieve the error "The user has no tokens assigned", 
that indicates an incorrect password was entered.

Alternatively, you can connect to the login node directly if you are on a wired Hofstra computers, 
connected to HUVPN, or connected to Eduroam with employee credentials:

```bash
ssh -p 5010 USERNAME@binary.star.hofstra.edu
```

where `USERNAME` is replaced with your username or H700 ID.

### What is my password?

Your Linux password is used to login to the cluster. If a temporary password was provided, you will need to set a new password the first time you login. Please note it will ask you to first enter your current (temporary) password again 
before promtping you to enter a new password twice.

The new password:
- Must be at least 8 characters long
- Must include at least 3 different character types (uppercase, lowercase, numbers, special characters)
- Cannot contain your name or common dictionary words

**Note**: No characters will appear when typing your password.

You will see the following when it is successful:
```
passwd: all authentication tokens updated successfully.
Connection to binary.star.hofstra.edu closed.
```

After seeing this message, retry the first step to SSH and use your password to log in.

## How to load a module

A module is simply a collection of pre-installed software and its dependencies 
that you can access on the cluster.

You can load a module using the `module` command, for instance:
```bash
module load python3
```

This command loads necessary files and modules in order for the command `python3`
to be valid when used. Please refer to [Environment modules]({{ site.baseurl }}{% link software/env-modules.md %}) for more detail on how the command `module` works.

## "Hello, World!" for Slurm

**Slurm** is the workload manager on the cluster that handles whose job(s) runs when, with what hardware resources,
for how long, etc.

In order for Slurm to know your job's requirements (i.e. resource requirements),
you would need to write a job submission script. This script tells Slurm everything it needs to know about your job,
from how many CPUs you need to how much memory you require. It's a simple text file that contains
both Slurm directives (starting with `#SBATCH`) and the commands you want to run.

```bash
#!/bin/bash
#SBATCH --job-name=hello_world
#SBATCH --output=hello_world.out
#SBATCH --error=hello_world.err
#SBATCH --nodes=1
#SBATCH --time=10:00
#SBATCH --mem=1G

echo "Hello, World!"
```

The directives in this script tell Slurm:
- `--job-name`: Names your job "hello_world" when shown in the queue
- `--output`: Writes standard output to a file "hello_world.out"
- `--error`: Writes error messages (stderr) to "hello_world.err"
- `--nodes`: Requests 1 compute node (since not granularly specified, it'll be chosen by Slurm)
- `--time`: Sets a time limit of 10 minutes (format is `minutes:seconds`)
- `--mem`: Allocates 1 GigaByte of main memory

To submit this job:

1. Save the script to a file named `hello_world.sbatch`
2. Submit it with the command:
   ```bash
   sbatch hello_world.sbatch
   ```
3. Slurm will respond with a job ID number, like:
   ```
   Submitted batch job 12345
   ```

Once your job completes, you'll find the output files in the directory you submit the job.

Read more about Slurm and examples covering various job types at [Jobs overview and submission]({{site.baseurl}}{% link jobs/Overview.md %}).

## How to check your job's status

You can find your job's status within the list provided by the `squeue` command's output:

```bash
squeue
```

Or only see jobs related to your username using:

```bash
squeue -u $USER
```

Furthermore, if you know your job's ID (it is reported by Slurm after your submission), 
you can do:
```
squeue -j <your-job-id>
```

where `<your-job-id>` is replaced with your job's ID.

## How to transfer a file to/from the cluster

You can use the SSH File Transfer Protocol (SFTP) and Secure Copy Protocol (SCP) to transfer 
files to and from the cluster to another machine of your choice.

Please see the [Transfer files to/from Star]({{site.baseurl}}{% link storage/file_transfer.md %}) 
page for a comprehensive guide for this operation.

## Where from here?

If you want to read more about Slurm and job submission, see [Jobs overview and submission]({{site.baseurl}}{% link jobs/Overview.md %}).

If you want to read about using containerization software to run some applications, see [Apptainer]({{site.baseurl}}{% link software/apptainer.md %}).

Also try looking around the sidebar. There are plenty of pages that cover a wide 
variety of tutorials and resources to help you make the most of the cluster.
