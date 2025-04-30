# Star HPC LONG Start Guide

Welcome to Star HPC! This guide will help you log in, run your first job, and start using the cluster. For an overview of policies, see the [Account & Access Overview](https://starhpc.hofstra.io/account-policies/).

## Connect to Star HPC

### Step 1: SSH into the Login Node

Use your provided credentials to connect:

```bash
ssh -p 5010 your_username@[login_node].hofstra.edu
```

> If you're using a Windows system, you can connect using [PuTTY](https://www.putty.org/) or [WSL + OpenSSH].

## Submit Your First Job

### Step 2: Load the Sample Python Job Script

Copy the sample job script into your home directory:

```bash
cp -r /fs1/shared/docs/examples/python_hello ~
cd ~/python_hello
```

### Step 3: Submit the Job

#### Using a Batch Job (Non-interactive)

To submit a Batch job or a non-interactive job, simply run the sbatch command in the terminal. For example:

```bash
sbatch python_hello.sh
```

For more information look at [Batch jobs](https://docs.starhpc.hofstra.io/jobs/submitting-jobs.html#batch-jobs-non-interactive)

#### Using a Interactive Job 

To submit an interactive job, simply run the srun command with your desired options directly in the terminal. For example:

```bash
srun --pty python ~/python_hello/hello_world.py
```

Once submitted, you'll be placed in an interactive shell on the allocated node when resources become available. Your prompt will change to indicate you're on the compute node.

For more information look at [Interactive jobs](https://docs.starhpc.hofstra.io/jobs/submitting-jobs.html#interactive-jobs)

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

## How to run a Jupyter Notebook

### Step 5: Load the Jupyter Notebook Job Script

Copy the job script into your home directory:

```bash
cp -r /fs1/shared/docs/examples/jupyter ~
cd ~/jupyter
```

### Step 6: Submit the Job
 
```bash
sbatch jupyter_notebook.sbatch
```

Upon your job's submission to the queue, you will see the output indicating your job's ID. You need to replace your job ID value with the `<jobid>` placeholder throughout the Jupyter Notebook section. 

Additionaly, if you run squeue immediately after submitting your job, you might see a message such as Node Unavailable next to your job. Before proceeding, wait until your job has changed to the RUNNING state as reported by the squeue command.

### Step 7: Check your output file for the SSH command

```bashmodu
cat jupyter_notebook_<jobid>.out  # Run this command in the directory the .out file is located.
```

### Step 8: Run the SSH port-forwarding command

Open a new terminal on your local machine and run the SSH command provided in the output file. If prompted for a password, use your Linux lab password if you haven't set up SSH keys. You might be requested to enter your password multiple times. **Note** that the command will appear to hang after successful connection - this is the expected behavior. Do not terminate the command (Ctrl + C) as this will disconnect your Jupyter notebook session.

### Step 9: Find and open the link in your browser 

Make sure you wait about 30 seconds after executing the SSH port-forwarding command on your local machine. It takes the .err file a little time to be updated and include your link.

Check the error file on the login node for your Jupyter notebook's URL:

```bash
cat jupyter_notebook_<jobid>.err  | grep '127.0.0.1' # Run this command in the directory the .err file is located.
```

Copy the URL from the error file, either of the two lines printed works, and paste it into your **local machine's browser**.

### Step 10: Clean up

If you're done prior to the job's termination due to the walltime, clean up your session by running this command on the login node:

```bash
scancel <jobid>
```

Afterwards, press Ctrl + C on your local computer's terminal session where you ran the port forwarding command to terminate the SSH connection.

Learn more about running and creating a job script with [Jupyter Notebook](https://docs.starhpc.hofstra.io/software/jupyter-notebook.html)

## Next Steps

- Learn about job scheduling with [Slurm](https://docs.starhpc.hofstra.io/jobs/submitting-jobs.html)
- Explore available software with `module avail`
- Store large datasets in project directories (request access if needed)

For help or questions, visit: [https://github.com/StarHPC/Issues](https://github.com/StarHPC/Issues) or email: `starhpc-support@hofstra.edu`.