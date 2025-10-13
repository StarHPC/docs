---
sort: 4
---

# Jupyter Notebook

Jupyter Notebook is an interactive web application that provides an environment where you can create and share documents with live code, equations, visualizations, and narrative text. It is great for data analysis, scientific computing, and machine learning tasks. You can run Python code in cells, see results right away, and document your work all in one place.

## Running Jupyter Notebook

Jupiter Notebook is installed on the cluster and can be started like any other workload, by launching it through Slurm. Jupiter is available as an [environment module]({{site.baseurl}}{% link software/env-modules.md %}), so it would be loaded into the environment with the `module` command. The example script that follows shows you this.

Alternatively, you could run Jupyter in a container. That would make it easy to load the environment you need when there is a container image available with your desired toolset pre-installed. Check out [Apptainer]({{site.baseurl}}{% link software/apptainer.md %}) to learn more.

```note
Use Your Storage Effectively
{:.h4.mb-2}

The directory `/fs1/projects/{project-name}/` lives on the parallel file-system storage, where most of your work should reside. While your home directory (`/home/{username}/`) can be used for quick experiments and convenient access to scripts, keep in mind that it has limited capacity and worse performance. The parallel file-system storable is much faster and has way more space for your notebooks and data.
```

### Step 1: Create the Job Script

You would create a job script to launch Jupyter Notebook and most other applications on the cluster. As the compute nodes (where workloads run on the cluster) are not directly reachable from the campus network, you will need to perform SSH port forwarding to access your Jupyter Notebook instance. The following script starts Jupyter Notebook on an available port and provides you the SSH command needed to then reach it. You can copy and paste this example to get started. From the login node, save this as `jupyter.sbatch`:

```bash
#!/bin/bash

#SBATCH --gpus=2
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --time=00:30:00
#SBATCH --job-name=jupyter_notebook
#SBATCH --output=/fs1/projects/<project-name>/%x_%j.out
#SBATCH --error=/fs1/projects/<project-name>/%x_%j.err

# Connection variables
LOGIN_NODE="<login-node-address>"  # Set this to the login node's address from the welcome email
LOGIN_PORT="<login-port>"          # Set this to the port number from the welcome email
XX="<xx>"                          # Set this to a number from 01-30

module load jupyter

check_port() {
   nc -z localhost $1
   return $(( ! $? ))
}

# Find an available port
port=8888
while ! check_port $port; do
   port=$((port + 1))
done

compute_node=$(hostname -f)
user=$(whoami)

echo "==================================================================="
echo "To connect to your Jupyter notebook, run this command on your local machine:"
echo ""
echo "ssh -N -L ${port}:${compute_node}:${port} -J ${user}@adams204${XX}.hofstra.edu:${LOGIN_PORT} ${user}@${LOGIN_NODE} -p ${LOGIN_PORT}"
echo ""
echo "When finished, clean up by running this command on the login node:"
echo "scancel ${SLURM_JOB_ID}"
echo "==================================================================="

# Start Jupyter notebook
jupyter notebook --no-browser --port=${port} --ip=0.0.0.0
```

The script uses these Slurm parameters:

- `--nodelist`: Specifies which compute node to use (e.g., `gpu1` or `cn01`)
- `--gpus=2`: This enables us to use 2 of the GPUs on the specified node. See each node's GPU information [here]({{site.baseurl}}{% link quickstart/about-star.md %}). Without this specification, you cannot see or use the GPUs on the compute node. Feel free to replace this number with another **valid option**.
- `--ntasks=1`: Runs one instance of Jupyter
- `--cpus-per-task=1`: Use one CPU thread. Note hyperthreading may be enabled on the compute nodes.
- `--time=00:30:00`: Sets a 30-minute time limit for the job (The format is `hh:mm:ss`)

### Step 2: Replace the placeholders

The `<...>` placeholders need to be replaced with what _you_ need:

- `<login-node-address>` needs to be replaced with the address of the login node provided in your welcome email
- `<login-port>` needs to be replaced with the port number from your welcome email
- `<xx>` needs to be replaced with a number between 01-30 (inclusive)
- `<compute-node>` needs to be replaced with an available compute node from the cluster nodes list. You can find the full list of nodes on the [About Star]({{site.baseurl}}{% link quickstart/about-star.md %}) page.
- Change the path for the `--output` and `--error` directives to where _you_ would like these files to be saved.

### Step 3: Submit the job

```bash
sbatch jupyter.sbatch
```

Upon your job's submission to the queue, you will see the output indicating your job's ID. You need to replace _your_ job ID value with the `<jobid>` placeholder throughout this documentation.

_**Your job may not start right away!**_
{:.bg-yellow-light.color-orange-9.p-2}

If you run `squeue` immediately after submitting your job, you might see a message such as `Node Unavailable` next to your job. Another job may be actively using those resources, and your job will be held in the queue until your request can be satisfied by the available resources.

In such case, the `.out` or `.err` files will not be created yet, as your job hasn't run yet.
Before proceeding to **Step 4**, wait until your job has changed to the `RUNNING` state as reported by the `squeue` command.

### Step 4: Check your output file for the SSH command

```bash
cat jupyter_notebook_<jobid>.out  # Run this command in the directory the .out file is located.
```

Replace `<jobid>` with the job ID you received after submitting the job.

### Step 5: Run the SSH port-forwarding command

Open a new terminal on your local machine and run the SSH command provided in the output file. If prompted for a password, use your Linux lab password if you haven't set up SSH keys. You might be requested to enter your password multiple times. **Note** that the command will appear to hang after successful connection - this is the expected behavior. Do not terminate the command (`Ctrl + C`) as this will disconnect your Jupyter notebook session (unless you intend to do so).

### Step 6: Find and open the link in your browser

Check the error file on the login node for your Jupyter notebook's URL:

```bash
cat jupyter_notebook_<jobid>.err  | grep '127.0.0.1' # Run this command in the directory the .err file is located.
```

Replace `<jobid>` with the job ID you received after submitting the job.

_**Be patient!**_
{:.bg-yellow-light.color-orange-9.p-2}

Make sure you wait about 30 seconds after executing the SSH port-forwarding command on your local machine. It takes the `.err` file a little time to be updated and include your link.

You might see two lines being printed. Either link works.
Copy the URL from the error file and paste it into your **local machine's browser**.

### Step 7: Clean up

If you're done prior to the job's termination due to the walltime, clean up your session by running this command on the login node:

```bash
scancel <jobid>
```

Replace `<jobid>` with the job ID you received after submitting the job.

Afterwards, press `Ctrl + C` on your local computer's terminal session, where you ran the port forwarding command. This would terminate the SSH connection.

## Working on the Compute Node

Do you need to access the node running Jupyter Notebook? You can use `srun` to launch an interactive shell. Check out [interactive jobs]({{site.baseurl}}{% link jobs/submitting-jobs.md %}#interactive-jobs) for more information.

## Run a Jupyter Notebook directly on a computing node from Visual Studio Code

This is a list of steps required to run a Jupyter Notebook directly on a computing node from Visual Studio Code.
<!-- This section was made possible thanks to the generous help of Mikhail Khlystun. -->
1. In StarHPC prepare a SLURM script to create a `ssh` tunnel directly to an assigned computing node:

   ```bash
   #!/bin/bash

   #SBATCH --output="tunnel.log"
   #SBATCH --job-name="tunnel"
   #SBATCH --time=1:00:00     # walltime
   #SBATCH --cpus-per-task=4  # number of cores
   #SBATCH --mem-per-cpu=16G   # memory per CPU core
   #SBATCH --gres=gpu:A30:1


   # find open port
   PORT=$(python -c 'import socket; s=socket.socket(); s.bind(("", 0)); print(s.getsockname()[1]); s.close()')
   scontrol update JobId="$SLURM_JOB_ID" Comment="$PORT"
   # start sshd server on the available port
   echo "Starting sshd on port $PORT"
   /usr/sbin/sshd -D -p ${PORT} -f /dev/null -h ${HOME}/.ssh/<private_key_for_your_StarHPC_account>
   ```

2. On your local machine add the following lines to your `.ssh/config` file:

   ```bash
   Host login2
    HostName login2.star.hofstra.edu
    Port 5010

   Host hpcx
    ProxyCommand ssh login2 "nc \$(squeue --me --name=tunnel --states=R -h -O NodeList,Comment)"
    StrictHostKeyChecking no
    ```

   where `login2` is the login node to access StarHPC, and `hpcx` is the assigned computing node.

   You can now ssh into this `hpcx` and start a Jupyter server, e.g. using an Anaconda environment, and run it directly from the dedicated window in your local instance of Visual Studio Code.
