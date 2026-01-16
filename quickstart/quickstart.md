# Star HPC Quick Start Guide

Welcome to Star HPC! This guide will help you log in, run your first job, and start using the cluster. For an overview of policies, see the [Account & Access Overview](https://starhpc.hofstra.io/account-policies/).

## Connect to Star HPC

### Step 1: SSH into the Login Node

Login with the credentials provided in the Welcome email!

If you're using a Windows system, you can connect using [PuTTY](https://www.putty.org/) or [WSL + OpenSSH].

## Submit Your First Job

### Step 2: Load the Sample Python Job Script

Copy the sample job script into your home directory:

```bash
cp -r /fs1/shared/docs/examples/python_hello ~
cd ~/python_hello
```

### Step 3: Submit the Job

To submit a Batch job or a non-interactive job, simply run the sbatch command in the terminal. For example:

```bash
sbatch python_hello.sh
```

For more information look at [Batch jobs](https://docs.starhpc.hofstra.io/jobs/submitting-jobs.html#batch-jobs-non-interactive)

### Step 4: Check the Output

```bash
cat python_hello.out
```

## Common Job Monitoring Commands

### Check your job status

```bash
sacct --user=<your_username>
```

### Cancel a Job

```bash
scancel <jobid>
```

### Transfer Files

Use `scp` or `rsync` to transfer files to/from the cluster:

```bash
scp -P 5010 myfile.txt your_username@[login_node].hofstra.edu:/home/your_username/
```
Learn more about monitoring and managing jobs at [Monitoring Jobs](https://docs.starhpc.hofstra.io/jobs/monitoring-jobs.html)

## Next Steps

- Learn about running and creating a job script with [Jupyter Notebook](https://docs.starhpc.hofstra.io/software/jupyter-notebook.html)
- Learn about job scheduling with [Slurm](https://docs.starhpc.hofstra.io/jobs/submitting-jobs.html)
- Explore available software with `module avail`
- Store large datasets in project directories (request access if needed)

For help or questions, visit: [https://github.com/StarHPC/Issues](https://github.com/StarHPC/Issues) or email: `starhpc-support@hofstra.edu`.