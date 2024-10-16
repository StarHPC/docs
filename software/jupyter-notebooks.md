# Jupyter Notebooks

Jupyter notebooks are interactive web-based environments that allow you to create and share documents containing live code, equations, visualizations, and narrative text. They're particularly useful for data analysis, scientific computing, and machine learning tasks, as they enable you to execute Python code in cells, see the results immediately, and document your workflow all in one place.

## Jupyter Notebook job example

As you may know, there is no Graphical User Interface (GUI) available when you connect to the cluster through your shell, hence in order to have access to some application's GUI, port forwarding is necessary [(What is SSH port forwarding?)](https://www.youtube.com/watch?v=x1yQF1789cE&ab_channel=TonyTeachesTech). In this example, we will do port forwarding to access Jupyter Notebook's web portal. You will basically send and receive your data through a specified port on your local machine that is tunneled to the port on the cluster where the Jupyter Notebook server is running. This setup enables you to work with Jupyter Notebooks as if they were running locally on your machine, despite actually being executed on a remote cluster node. After a successful setup, you can access Jupyter's portal through your desired browser through a generated link by Jupyter **on your local machine**.

First, create your `sbatch` script file. I'm going to call mine `jupyterTest.sbatch`. Then add the following to it:

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
- `--ntasks=1`: This directive tells SLURM to allocate resources for one task. A "task" in this context is essentially an instance of your application or script running on the cluster. For many applications, especially those that don't explicitly parallelize their workload across multiple CPUs or nodes, specifying a single task is sufficient. However, if you're running applications that can benefit from parallel execution, you might increase this number. This directive is crucial for optimizing resource usage based on the specific needs of your job. For instance, running multiple independent instances of a data analysis script on different subsets of your data could be a scenario where increasing the number of tasks is beneficial.
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

Copy that address and paste it into your browser, and you must successfully access Jupyter's GUI.
