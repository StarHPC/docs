---
sort: 2
---

# Submitting Jobs

This page is mainly dedicated to examples of different job types. For a more comprehensive explanation on different job types, please refer to [Jobs Overview]({{site.baseurl}}{% link jobs/Overview.md %})

## Batch jobs (Non-interactive)

Batch jobs allow users to execute tasks without direct interaction with the computing environment. Jobs are written as scripts that consist of the commands to be executed and a specification of the requested resources.

### BATCH directives

BATCH directives are essentially instructions embedded at the beginning of a batch job script and are interpreted by the scheduler (like Slurm in our case). These lines are prefixed with `#SBATCH` for Slurm and inform the scheduler about the resources needed for the job and any other execution preferences.

Here are a list of common directives: <br>

- `#SBATCH --nodes=<some-value>`: Requests a specific number of nodes to run your job on.
- `#SBATCH --mem=<some-value>`: Specifies the amount of RAM required.
- `#SBATCH --time=<some-value>`: Sets the maximum runtime.
- `#SBATCH --output=<some-value>`: Directs the job's output to a specific file.

**Note:** These bullets are just for a better basic understanding on the topic. Complete examples and line-by-line explanations are provided further down in this page.

### Queues and partitions

Queues (or partitions in Slurm terminology) are categories within the cluster that organize jobs based on their resource requirements, priority, and other factors.

Our cluster is partitioned into the following categories: <br>

- **Standard Partition (future_parition_name)**: For general-purpose jobs with moderate resource requirements.
- **High-Memory Partition (future_parition_name)**: For jobs requiring significant amounts of memory.
- **GPU Partition (future-partition_name)**: For jobs that need GPU resources, such as gpu1 and gpu2 in our setup.

Choosing the right partition ensures your job is queued in an environment suited to its needs, and can potentially reduce wait times.

### Basic batch job example

In this example we are going to run a Python program with specified resource limits through our batch script. <br>
Create two files, one containing your `.sbatch` script and the other containing your `.py` program: <br>
I'm going to name them `my_script.sbatch` and `my_script.py`. <br>
Then add the following to `my_script.sbatch`:

```bash
#!/bin/bash
#SBATCH --job-name=test_job
#SBATCH --output=test_job.out
#SBATCH --error=test_job.err
#SBATCH --nodes=1
#SBATCH --time=10:00
#SBATCH --mem=1G

module load python3
python3 my_script.py
```

And add a simple `print` statement to `my_script.py`:

```python
print("Hello World!")
```

**NOTE:** In this example it's assumed that both your batch and Python script files are in the same directory. If that is not the case, please make sure you are providing the full path (starting from the root directory `/`) to your Python file, inside of your script. For example:

```bash
python3 /path/to/python_script/my_script.py
```

Now let's walk through `my_script.sbatch` line by line to see what each directive does.

- `#!/bin/bash`: This line needs to be included at the start of **all** your batch scripts. It basically specifies the script to be run with a shell called `bash`.

Lines 2-7 are your `SBTACH` directives. These lines are where you specify different options for your job including its name, output and error files path/name, list of nodes you want to use, resource limits, and more if required. Let's walk through them line by line:

- `#SBATCH --job-name=test_job`: This directive gives your job a name that you can later use to easier track and manage your job when looking for it in the queue. In this example, we've called it `test_job`. You can read about job management at [Monitoring jobs]({{ site.baseurl }}{% link jobs/monitoring-jobs.md %}).
- `#SBATCH --output=test_job.out`: Used to specify where your output file is generated, and what it's going to be named. In this example, we have not provided a path, but only provided a name. When you use the `--output` directive without specifying a full path, just providing a filename, Slurm will store the output file in the current working directory from which the `sbatch` command was executed.
- `#SBATCH --error=test_job.err`: Functions similar to `--output` except it contains error messages generated during the execution of your job, if any. **The `.err` file is always going to be generated even if your job execution is successful; however, it's going to be empty if there are no errors.**
- `#SBATCH --nodes=1`: Specifies your job to run on one available node. This directive basically tells the scheduler "Run my job on any available node you find, and I don't care which one". **It's also possible to specify the name of the node(s) you'd like to use which we will cover in future examples.**
- `#SBATCH --time=10:00`: This line specifies how long you want your job to run, after it's out the queue and starts execution. In this case, the job will be **terminated** after 10 minutes. Acceptable time formats include `mm`, `mm:ss`, `hh:mm:ss`, `days-hh`, `days-hh:mm` and `days-hh:mm:ss`.
- `#SBATCH --mem=1G` Specifies the maximum main memory required _per_ node. In this case we set the cap to 1 gigabyte. If you don't use a memory unit, Slurm automatically uses MegaBytes: `#SBATCH --mem=4096` requests 4096MB of RAM. **If you want to request all the memory on a node, you can use** `--mem=0`.

After the last `#SBATCH` directive, commands are ran like any other regular shell script.

- `module load python3`: Loads necessary files and modules in order for the command `python3` to be valid when used. Please refer to [Environment modules]({{ site.baseurl }}{% link software/env-modules.md %}) for more detail on how the command `module` works.
- `python3 my_script.py`: Just like any other `python3` command, this line runs the `my_script.py` file using Python. **Later, the output(s) and/or error(s) of this operation is written to the files we have specified in our directives.**

### Batch Job Submission

This script as discussed previously, is a non-interactive job. Non-interactive jobs are submitted to the queue with the use of the `sbatch` command. In this case, we submit our job using `sbatch my_script.sbatch`.

### Jupyter Notebook batch job example

As you know, there is no Graphical User Interface (GUI) available when you connect to the cluster through your shell, hence in order to have access to some application's GUI, port fortforwarding is necessary [(What is SSH port forwarding?)](https://www.youtube.com/watch?v=x1yQF1789cE&ab_channel=TonyTeachesTech). In this example, we will do port forwarding to access Jupyter Notebook's web portal. You will basically send and receive your data through a specified port on your local machine that is tunneled to the port on the cluster where the Jupyter Notebook server is running. This setup enables you to work with Jupyter Notebooks as if they were running locally on your machine, despite actually being executed on a remote cluster node. After a successful setup, you can access Jupyter's portal through your desired browser through a generated link by Jupyter **on your local machine**.

First, create your sbatch script file. I'm going to call mine `jupyterTest.sbatch`. Then add the following to it:

```bash
#!/bin/bash

#SBATCH --nodelist=cn01
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=1
#SBATCH --time=00:30:00
#SBATCH --job-name=jupyterTest1
#SBATCH --output=/home/mani/outputs/jupyterTest1.out
#SBATCH --error=/home/mani/outputs/jupyterTest1.err

# get tunneling info
XDG_RUNTIME_DIR=""
node=$(hostname -s)
user=$(whoami)
port=9001

# print tunneling instructions to jupyterTest1
echo -e "
Use the following command to set up ssh tunneling:
ssh -p5010 -N -f -L ${port}:${node}:${port} ${user}@binary.star.hofstra.edu"

module load jupyter

jupyter notebook --no-browser --port=${port} --ip=${node}
```

Replace `/home/username/outputs/` with your actual directory path for storing output and error files.

First, let's take a look at what the new directives and commands in this script do:

Note that most of the directives at the start of this script have previously been discussed at "Basic batch job example", so we are only going to discuss the new ones:

- `--nodelist=cn01`: Using `--nodelist` you can specify the exact name(s) of the node(s) you want your job to run on. In this case, we have specified it to be `cn01`.
- `--ntasks=1`: This directive tells Slurm to allocate resources for one task. A "task" in this context is essentially an instance of your application or script running on the cluster. For many applications, especially those that don't explicitly parallelize their workload across multiple CPUs or nodes, specifying a single task is sufficient. However, if you're running applications that can benefit from parallel execution, you might increase this number. This directive is crucial for optimizing resource usage based on the specific needs of your job. For instance, running multiple independent instances of a data analysis script on different subsets of your data could be a scenario where increasing the number of tasks is beneficial.
- `--cpus-per-task=1`: This sets the number of CPUs allocated to each task specified by `--ntasks`. By default, setting it to 1 assigns one CPU to your task, which is fine for tasks that are not CPU-intensive or designed to run on a single thread. However, for applications that are multi-threaded and can utilize more than one CPU core for processing, you would increase this value to match the application's capability to parallelize its workload.
- The variable initializations such as `node=...`, `user=...` are used to retrieve some information from the node you are running your job on to produce the right command for you to later run **locally**, and set up the SSH tunnel. You don't need to worry about these.
- The `echo` command is going to write the ssh tunneling command to your `.out` file with the help of the variables. We will explain how to use that generated command further below.
- `module load jupyter`: Loads the required modules to add support for the command `jupyter`.
- `jupyter notebook --no-browser --port=${port} --ip=${node}` runs a jupyter notebook and makes it listen on our specified port and address to later be accessible through your local machine's browser.

Then, submit your Batch job using `sbatch jupyterTest.sbatch`. Make sure to replace `jupyterTest.sbatch` with whatever file name and extension you choose.

At this stage, if you go and read the content of `jupyterTest.out`, there is a generated command that must look like the following:

```bash
ssh -p5010 -N -f -L 9001:cn01:9001 <your-username>@binary.star.hofstra.edu
```

Copy that line and run it in your local machine's command line. Then, enter your login credentials for `binary` and hit enter. You should not expect anything magical to happen. In fact, if everything is successful, your shell would go to a new line without generating any output.

You can now access Jupyter's GUI through a browser of your choice on your local machine, at the address that jupyter notebook has generated for you. For some reason, Jupyter writes the address to `stderr`, so you must look for it inside your `jupyterTest.err` file. Inside that file, there must be a line containing a link similar to the following:

```bash
http://127.0.0.1:9001/?token=...(your token is here)...
```

Copy that address and paste it into your browser, and you must successfuly access Jupyter's GUI.

### Apptainer TensorFlow batch job example

This example shows how to execute a TensorFlow script, `tfTest.py`, that trains a simple neural network on the MNIST dataset using GPUs.

First, create a Python script called `tfTest.py` with the provided content:

```python
import tensorflow as tf

physical_devices = tf.config.list_physical_devices(device_type=None)

print("Num of Devices:", len(physical_devices))

print("Devices:\n", physical_devices)

print("Tensorflow version information:\n",tf.__version__)

print("begin test...")

mnist = tf.keras.datasets.mnist
mnist = tf.keras.datasets.mnist

(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0

model = tf.keras.models.Sequential([
   tf.keras.layers.Flatten(input_shape=(28, 28)),
   tf.keras.layers.Dense(128, activation='relu'),
   tf.keras.layers.Dropout(0.2),
   tf.keras.layers.Dense(10)
 ])

predictions = model(x_train[:1]).numpy()

loss_fn = tf.keras.losses.SparseCategoricalCrossentropy(from_logits=True)

loss_fn(y_train[:1], predictions).numpy()

model.compile(optimizer='adam',
                loss=loss_fn,
                metrics=['accuracy'])

model.fit(x_train, y_train, epochs=10)

model.evaluate(x_test,  y_test, verbose=2)
```

Next, create a Slurm batch job script named `job-test-nv-tf.sh`. This script requests GPU resources, loads necessary modules, and runs your TensorFlow script inside an Apptainer container:

```bash
#!/bin/bash

#SBATCH --job-name=tensorflow_test_job
#SBATCH --output=result.txt
#SBATCH --nodelist=gpu1
#SBATCH --gres=gpu:A100:2
#SBATCH --ntasks=1
#SBATCH --time=10:00
#SBATCH --mem-per-cpu=1000

module load python3
module load apptainer

echo "run Apptainer TensorFlow GPU"
apptainer run --nv tensorflowGPU.sif python3 tfTest.py
```

This script runs the `tfTest.py` script inside the TensorFlow GPU container (`tensorflowGPU.sif`)

You can now submit your job to Slurm using `sbatch job-test-nv-tf.sbatch`.

After the job completes, you can check the output in `result.txt`. The output should include information about the available physical devices (GPUs), the TensorFlow version, and the output from training the model on the MNIST dataset.

The beginning and end of the file might look something like this:

```text
run Apptainer TensorFlow GPU
Num of Devices: X
Devices:
 [PhysicalDevice(name='/physical_device:CPU:0', device_type='CPU'), PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU'), ...]
Tensorflow version information:
 X.XX.X
begin test...
...
313/313 - 0s - loss: X.XXXX - accuracy: 0.XXXX
```

## Interactive jobs

### Starting an Interactive job

To start an interactive job, you use the `srun` command with specific parameters that define your job's resource requirements. Here's an example:

```bash
srun --pty --nodelist=cn01 --ntasks=1 --cpus-per-task=1 --time=01:00:00 --mem=4G /bin/bash
```

Here's a break down of what each part of this command does:
\*\*All of these options except the command `srun` and the option `--pty` are discussed in great detail under the "Batch jobs" section of this page. Refer back to them if you need a more comprehensive explanation.

- `srun`: The command used to start an interactive session on the cluster.
- `--pty`: Requests a pseudo-terminal, which is necessary for interactive sessions.
- `--nodelist=cn01`: Specifies the node on which to run the interactive session. Here, cn01 is used as an example.
- `--ntasks=1`: Allocates resources for one task.
- `--cpus-per-task=1`: Assigns one CPU to the task. This is enough for tasks that don't require parallel processing across multiple CPUs.
- `--time=01:00:00`: Sets the maximum duration of the interactive session to 1 hour.
- `--mem=4G`: Specifies that the job requires 4 gigabytes of memory.
- `/bin/bash`: After allocating the requested resources, srun starts a Bash shell in the interactive session.

When your job exits the queue, you will see your shell prompt change, as you receive a shell on the allocated compute node. This shift means that you're now directly interacting with the HPC environment through an interactive session and you are now enabled to execute commands and run applications using the cluster's computational resources in real-time.

### Additional `srun` Options for Interactive jobs

**Specifying a Partition**: If your job has specific resource requirements, you may want to specify a partition that matches those needs.

```bash
--partition=your_partition_name
```

**Allocating GPUs**: For tasks requiring GPU acceleration, you can request specific GPUs.

```bash
--gres=gpu:2
```

This option requests 2 GPUs for your job. Adjust the number according—and within the supported limits—to your task's requirements.

**Exclusive Node Access**: To ensure that no other jobs share your allocated node, you can request exclusive access.

```
--exclusive
```

**Memory per CPU**: If your job requires a specific amount of memory per CPU, this can be specified.

```bash
--mem-per-cpu=4G
```

This option requests 4 GB of memory per allocated CPU.

**Quality of Service (QoS)**: For prioritizing jobs, you can specify the Quality of Service.

```bash
--qos=your_qos
```

**Email Notifications**: Stay informed about your job's status with email notifications.

```bash
--mail-type=ALL
--mail-user=your_email@example.com
```

This configuration sends an email to the specified address at the start, completion, and failure of the job.

### Interactive Job Submission

To submit an interactive job, simply run the `srun` command with your desired options directly in the terminal. For example:

```bash
srun --pty --nodelist=cn01 --ntasks=1 --cpus-per-task=1 --time=01:00:00 --mem=4G /bin/bash
```

Once submitted, you'll be placed in an interactive shell on the allocated node when resources become available. Your prompt will change to indicate you're on the compute node.

## Array jobs

To submit an array job, you use the `--array` as a part of your `sbatch`. This option specifies a range of indices that Slurm uses to create multiple tasks from a single job submission. Each task in the array is assigned a unique SLURM_ARRAY_TASK_ID that can be used within your scripts to differentiate between them.

### Array job example

Suppose you have a dataset split into multiple files and you want to process each file independently. Instead of submitting a separate job for each file, you can submit a single array job where each task processes a different file.

Now, let's have a simple, low-resource Python task that you can run as part of an array job, let's create a Python script that generates a basic report based on the `SLURM_ARRAY_TASK_ID`. This script will read from a specific input file based on the task ID, perform a simple operation (like counting the number of lines or words), and output the results to a file.

First, let's create your input files. You can make these as simple text files with a few lines of content. For example:
`input1.txt`, `input2.txt`, and `input3.txt`.

Each file could contain a few lines of arbitrary text. You can create these files manually or use the command line:

```bash
echo "This is a simple file." > input1.txt
echo "This file contains\nseveral lines of text." > input2.txt
echo "Each file will have\na different\nnumber of lines." > input3.txt
```

Now here's the content of the Python script, `process_data.py`, which reads from one of these input files based on the `SLURM_ARRAY_TASK_ID` and counts the number of lines:

```python
import sys

# Get the task ID from the command line arguments
task_id = sys.argv[1]

# Construct the filename based on the task ID
filename = f"input{task_id}.txt"

# Try to open and read the file
try:
    with open(filename, 'r') as file:
        lines = file.readlines()
        num_lines = len(lines)
    # Output the results
    output_filename = f"output{task_id}.txt"
    with open(output_filename, 'w') as outfile:
        outfile.write(f"File: {filename}\nNumber of lines: {num_lines}\n")
    print(f"Processed {filename} successfully.")
except FileNotFoundError:
    print(f"File {filename} not found.")
```

This script basically takes an argument from the command line (Expected to be the `SLURM_ARRAY_TASK_ID`). Then constructs a filename from this ID, reads the corresponding input file, counts its lines, writes the count to an output file, and in case of any missing files it handles them by printing a message instead of crashing.

Finally, to run this script as part of an array job on 3 files, adjust the `--array` option in your Slurm script (`process_array.sbatch`) to `1-3`.

```bash
#!/bin/bash
#SBATCH --job-name=array_job
#SBATCH --output=array_job_%A_%a.out
#SBATCH --error=array_job_%A_%a.err
#SBATCH --array=1-3
#SBATCH --mem=1G
#SBATCH --time=00:10:00

module load python3
python3 process_data.py $SLURM_ARRAY_TASK_ID
```

In the context of Slurm job submission scripts, %A and %a are special placeholders used within directives like --output and --error to dynamically generate filenames based on the job's array ID and the individual task ID within the array. Here's what each placeholder represents:

- `%A`: This placeholder is replaced by the Slurm job array's ID. The job array ID is a unique identifier assigned by SLURM to the entire array job at the time of submission. It helps you group and identify all tasks belonging to the same array job.
- `%a`: This placeholder is substituted with the specific task ID within the job array. Since an array job consists of multiple tasks, each with a unique task ID (determined by the `--array` option when the job is submitted), `%a` allows you to create distinct output or error files for each task, making it easier to troubleshoot and analyze the results of individual tasks.

For example, if you submit an array job with the --array=1-10 option and use the following in your script:

```bash
#SBATCH --output=job_output_%A_%a.out
#SBATCH --error=job_error_%A_%a.err
```

Slurm will create separate output and error files for each of the ten tasks in the array. If the array job's ID is 12345, the files for the first task will be named job_output_12345_1.out and job_error_12345_1.err, the files for the second task will be job_output_12345_2.out and job_error_12345_2.err, and so on.

Now submit this job using `sbatch process_array.sbatch` and you must see 6 different output files (3 ending in `.out` and 3 in `.err`). The `.out` files each contain the content of the relevant text file they read from, and the `.err` files are expected to be empty if everything has run smoothly.

### Array Job Submission

To submit an array job, use the `sbatch` command with your Slurm script that includes the `--array` option. For example:

```bash
sbatch process_array.sbatch
```

This will submit the entire array job. Slurm will then manage the execution of individual tasks within the array based on available resources.

## Parallel jobs

### Open MPI job example

First, you need to create your MPI program. In this example, we are calling it `mpi_hello_world.c`. This program initializes the MPI environment, gets the rank of each process, the total number of processes, and the name of the processor, then prints a greeting from each process.

```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char** argv) {
    MPI_Init(NULL, NULL);
    int PID;
    MPI_Comm_rank(MPI_COMM_WORLD, &PID);
    int number_of_processes;
    MPI_Comm_size(MPI_COMM_WORLD, &number_of_processes);
    char processor_name[MPI_MAX_PROCESSOR_NAME];
    int name_length;
    MPI_Get_processor_name(processor_name, &name_length);
    printf("Hello MPI user: from process PID %d out of %d processes on machine %s\n", PID, number_of_processes, processor_name);
    MPI_Finalize();
    return 0;
}
```

Next, create the compilation and execution script. <br>
Create a Bash script named mpi_hello_world.sh to compile and run the MPI program. This script takes a parameter for the number of processes to spawn.

```bash
#!/bin/bash
SRC=mpi_hello_world.c
OBJ=mpi_hello_world
NUM=$1
mpicc -o $OBJ $SRC
mpirun -n $NUM ./$OBJ
```

This script compiles the MPI program using `mpicc` and runs it with `mpirun`, and specifies the number of processes with `-n`.

Next, prepare a Slurm batch job script named `job-test-mpi.sbatch` to submit your MPI job. This script requests cluster resources and runs your MPI program through `mpi_hello_world.sh`:

```bash
#!/bin/bash
#SBATCH --job-name=mpi_job_test
#SBATCH --output=result.txt
#SBATCH --error=error.txt
#SBATCH --nodelist=gpu1,gpu2,cn01
#SBATCH --time=10:00
#SBATCH --mem-per-cpu=1000

module load openmpi4
echo "run mpi program using parallel processes"
sh mpi_hello_world.sh $1
```

This script sets up a job with the name mpi_job_test, specifies output and error files, requests resources (All 3 nodes from the cluster), and loads the OpenMPI module. It then runs the `mpi_hello_world.sh` script and passes the number of processes as an argument.

### Parallel Job Submission

Submit your parallel MPI job to Slurm using the `sbatch` command `sbatch job-test-mpi.sbatch`, specifying the desired number of parallel processes with `-n`. For example, to run with 8 parallel processes:

```bash
sbatch -n 8 job-test-mpi.sh 8
```

After the job completes, check the output in `result.txt`. You should see greetings from each MPI process. The content might look something like this, shortened with `...` for brevity:

```bash
Hello MPI user: from process PID 0 out of 8 processes on machine gpu1
...
Hello MPI user: from process PID 7 out of 8 processes on machine cn01
```
