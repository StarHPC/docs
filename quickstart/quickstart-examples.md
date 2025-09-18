# Star HPC Quick Start Guide

Welcome to Star HPC! This guide will help you log in, run your first job, and start using the cluster. For an overview of policies, see the [Account & Access Overview].

## Connect to Star HPC

### Step 1: SSH into the Login Node
Use your provided credentials to connect:

```bash
ssh -p 5010 your_username@binary.star.hofstra.edu
```

> If you're using a Windows system, you can connect using [PuTTY](https://www.putty.org/) or [WSL + OpenSSH].

## Set Up Your Environment

### Step 2: Load a Module
Star uses environment modules to manage software. Load a basic module like Python:

```bash
module avail         # View available modules
module load python   # Load Python module
```

You can use `module list` to see what you have loaded.

## Submit Your First Job

### Step 3: Create a Simple Job Script
Create a file called `test_job.slurm`:

```bash
nano test_job.slurm
```

Paste this content [These lines at the beginning of a job script are not just comments, but are actually processed by sbatch]:

```bash
#!/bin/bash
#SBATCH --job-name=test_job
#SBATCH --output=test_job.out
#SBATCH --error=test_job.err
#SBATCH --nodes=1
#SBATCH --time=10:00
#SBATCH --mem=1G

what you wish to run in the script.
```

Save and exit.
### Example

Create a file called `hello.slurm`:

```bash
nano hello.slurm
```

Paste this content [These lines at the beginning of a job script are not just comments, but are actually processed by sbatch]:

```bash
#!/bin/bash
#SBATCH --job-name=hello
#SBATCH --output=hello.out
#SBATCH --time=00:01:00
#SBATCH --ntasks=1

echo "Hello from Star HPC!"
```

Save and exit.

### Step 4: Submit the Job

### Using a Batch job (Non-interactive)

```bash
sbatch hello.slurm
```
For more information look at [Batch jobs] (https://docs.starhpc.hofstra.io/jobs/submitting-jobs.html#batch-jobs-non-interactive)

### Using a Interactive Job 

To submit an interactive job, simply run the srun command with your desired options directly in the terminal. For example:

```bash
chmod +x ~/python_hello/python_hello.sh
srun --pty --ntasks=1 --cpus-per-task=1 --time=01:00:00 --mem=4G ~/python_hello/python_hello.sh

srun --pty --ntasks=1 --cpus-per-task=1 --time=01:00:00 --mem=4G python ~/python_hello/python_hello.sh
```

Once submitted, you'll be placed in an interactive shell on the allocated node when resources become available. Your prompt will change to indicate you're on the compute node.

For more information look at [Interactive jobs] (https://docs.starhpc.hofstra.io/jobs/submitting-jobs.html#interactive-jobs)

### Step 5: Monitor Your Job

```bash
squeue -u your_username
```

Once complete, check the output:

```bash
cat hello.out
```

### Cancel a job

```bash
scancel job_id
```

## Transfer Files

Use `scp` or `rsync` to transfer files to/from the Star cluster:

```bash
scp -P 5010 myfile.txt your_username@binary.star.hofstra.edu:/home/your_username/
```

### How to run a Jupyter Notebook – Include the necessary commands, a sample sbatch job template (cp /fs1/shared/job-examples/juperter-notebook.sbatch /fs1/projects/<your_project>), and an ssh port-forwarding command.

-copy into their own directory and have them submit that
change that one from 1-30 to anything cause if its down it wouldnt work(alex may have changed)

Learn more about running [Jupyter Notebook] (https://docs.starhpc.hofstra.io/software/jupyter-notebook.html)

## Next Steps

- Learn about job scheduling with [Slurm] (https://docs.starhpc.hofstra.io/jobs/submitting-jobs.html)
- Explore available software with `module avail`
- Store large datasets in project directories (request access if needed)

For help or questions, visit: [https://github.com/StarHPC/Issues] or email: `starhpc-support@hofstra.edu`.
