---
sort: 4
---

# Jupyter Notebook

Jupyter Notebook is an interactive web application that provides an environment where you can create and share documents with live code, equations, visualizations, and narrative text. It is great for data analysis, scientific computing, and machine learning tasks. You can run Python code in cells, see results right away, and document your work all in one place.

```note
This example uses local storage as we are not dealing with large amounts of data. If you need to import and use large datasets, make sure to use `/fs1/projects/{project-name}/` which lives on the parallel file-system storage, and ensure your scripts and notebooks use the correct paths to manage your data.
```

## Running Jupyter Notebook

Jupyter Notebook is started on the cluster like any other workload, by submitting a job through Slurm.

As the compute nodes (where workloads run on the cluster) are not directly reachable from the campus network, we need to setup SSH port forwarding to access the Jupyter Notebook instance. The following script starts Jupyter Notebook on an available port and then provides you the necessary SSH command to reach it.

### Step 1: Create the Slurm script

On the login node, save the following script as `jupyter.sbatch`:
```bash
#!/bin/bash

#SBATCH --nodelist=<compute-node>
#SBATCH --gpus=2
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --time=00:30:00
#SBATCH --job-name=jupyter_notebook
#SBATCH --output=/home/%u/%x_%j.out
#SBATCH --error=/home/%u/%x_%j.err

# Connection variables
LOGIN_NODE="<login-node-address>"  # Replace between the " " with the login node's address from the welcome email
LOGIN_PORT="<login-port>"          # Replace between the " " with port number from welcome email
XX="<xx>"                          # Replace between the " " with a number from 01-30

module load jupyter

# Check for a port's availability
check_port() {
   local port=$1
   nc -z localhost $port
   if [ $? -eq 0 ]; then
       return 1
   else
       return 0
   fi
}

# Find an available port
port=8888
while ! check_port $port; do
   port=$((port + 1))
done

# Get node information
compute_node=$(hostname -f)
user=$(whoami)

# Print connection instructions
echo "==================================================================="
echo "To connect to your Jupyter notebook, run this command on your local machine:"
echo ""
echo "ssh -N -L ${port}:${compute_node}:${port} -J ${user}@adams204${XX}.hofstra.edu:${LOGIN_PORT},${user}@${LOGIN_NODE}:${LOGIN_PORT} ${user}@${LOGIN_NODE}"
echo ""
echo "When finished, clean up by running this command on the login node:"
echo "scancel ${SLURM_JOB_ID}"
echo "==================================================================="

# Start Jupyter notebook
jupyter notebook --no-browser --port=${port} --ip=0.0.0.0
```
The script uses these Slurm settings:
- `--nodelist`: Specifies which compute node to use (e.g., `gpu1` or `cn01`)
- `--gpus=2`: This enables us to use 2 of the GPUs on the specified node. See each node's GPU information [here]({{site.baseurl}}{% link quickstart/about-star.md %}). Without this specification, you cannot see or use the GPUs on the compute node. Feel free to replace this number with another **valid option**.
- `--ntasks=1`: Runs one instance of Jupyter
- `--cpus-per-task=1`: Uses one CPU thread (Hyperthreading is enabled on the compute nodes)
- `--time=00:30:00`: Sets a 30-minute time limit for the job (The format is `hh:mm:ss`)

### Step 2: Replace the placeholders

The `<...>` placeholders need to be replaced with what _you_ need:

- `<login-node-address>` needs to be replaced with the address of the login node provided in your welcome email
- `<login-port>` needs to be replaced with the port number from your welcome email
- `<xx>` needs to be replaced with a number between 01-30 (inclusive)
- `<compute-node>` needs to be replaced with an available compute node from the cluster nodes list. You can find the full list of nodes on the [About Star page]({{site.baseurl}}{% link quickstart/about-star.md %}).
- Make sure you change the path for both the `--output` and `--error` directives to where _you_ would like the files to be saved at. If you are fine with the script saving them to your home directory, leave them as is.

### Step 3: Submit the job

```bash
sbatch jupyter.sbatch
```
Upon your job's submission to the queue, you will see the output indicating your job's ID. You need to replace _your_ job ID value with the `<jobid>` placeholder throughout this documentation.

```warning
### Your job may not start right away!
If you run `squeue` after submitting your job, you might see a message such as `Node Unavailable` next to your job. Another job may be actively using those resources, and your job will be held in the queue until your request could be satisfied by the available resources.
```

In such case, you cannot see the `.out` or `.err` files, as your job hasn't been submitted yet.
Before proceeding to **Step 4**, make sure your job's status is set to `RUNNING` when checking with `squeue`.

### Step 4: Check your output file for the SSH command

```bash
cat jupyter_notebook_<jobid>.out  # Run this command in the directory the .out file is located.
```
Replace `<jobid>` with the job ID you received after submitting the job.

### Step 5: Run the SSH Port-forwarding command

Open a new terminal on your local machine and run the SSH command provided in the output file. If prompted for a password, use your Linux lab password if you haven't set up SSH keys. You might be requested to enter your password multiple times. **Note** that the command will appear to hang after successful connection - this is the expected behavior. Do not terminate the command (`Ctrl + C`) as this will disconnect your Jupyter notebook session (unless you intend to do so).

### Step 6: Find and open the link in your browser

Check the error file on the login node for your Jupyter notebook's URL:
```bash
cat jupyter_notebook_<jobid>.err  | grep '127.0.0.1' # Run this command in the directory the .err file is located.
```
Replace `<jobid>` with the job ID you received after submitting the job.

```warning
### Wait a moment!
Make sure you wait about 30 seconds after executing the SSH portforwarding command on your local machine. It takes the `.err` file a little time to be updated and include your link.
```

You might see two lines being printed. Either link works.
Copy the URL from the error file and paste it into your **local machine's browser**.

### Step 7: Clean up

If you're done prior to the job's termination due to the walltime, clean up your session by running this command on the login node:
```bash
scancel <jobid>
```
Replace `<jobid>` with the job ID you received after submitting the job.

Afterwards, press `Ctrl + C` on your local computer's terminal session, where you ran the port forwarding command. This would terminate the SSH connection.

## Working on the Same Node

Need to run commands directly on the compute node(s)? Use `srun` to get an interactive shell.

Check out the Interactive jobs section on [this page]({{site.baseurl}}{% link jobs/submitting-jobs.md %}) for more information.

## Using Existing Container Images

You can also run Docker images on the cluster through [Apptainer](https://apptainer.org/) (a variant of Singularity). This is great when you want an environment with everything pre-installed. Check out [the Apptainer Guide]({{site.baseurl}}{% link software/apptainer.md %}) to learn more.
