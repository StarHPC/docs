# SLURM Workload Manager

Slurm Workload Manager, or SLURM (Simple Linux Utility for Resource Management), is a free and open-source job scheduler for managing workloads on Linux and Unix-based clusters, such as Star.

There are two ways of starting jobs with SLURM; either interactively
with `srun` or as a script with `sbatch`.

Interactive jobs are a good way to test your setup before you put it
into a script or to work with interactive applications like MATLAB or
python. You immediately see the results and can check if all parts
behave as you expected. See `interactive` for more details.

## SLURM Parameter

SLURM supports a multitude of different parameters. This enables you to
effectivly tailor your script to your need when using Star but also
means that is easy to get lost and waste your time and quota.

The following parameters can be used as command line parameters with
`sbatch` and `srun` or in jobscript, see `job_script_examples`. To use
it in a jobscript, start a newline with `#SBTACH` followed by the
parameter. Replace \<....\> with the value you want, e.g.
`--job-name=test-job`.

### Basic settings:
There is a **Slurm Job Script Generator** at <a href="https://manitofigh.github.io/SlurmJobGeneration" target="_blank">this link</a>, which we suggest you use *after* reading this documentation in order to get a better understanding of how the Slurm directives work and how Slurm scripts are meant to be written.

<table>
<thead>
<tr class="header">
<th>Parameter</th>
<th>Function</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>--job-name=&lt;name&gt;</td>
<td>Job name to be displayed by <code>squeue</code></td>
</tr>
<tr class="even">
<td>--output=&lt;path&gt;</td>
<td><div class="line-block">Path to the file where the job
output is written to</div></td>
</tr>
<tr class="odd">
<td>--mail-type=&lt;type&gt;</td>
<td><div class="line-block">Turn on mail notification; type can be one
of BEGIN, END, FAIL, REQUEUE or ALL</div></td>
</tr>
<tr class="even">
<td>--mail-user=&lt;email_address&gt;</td>
<td>Email address to send notifications to</td>
</tr>
</tbody>
</table>

### Requesting Resources

| Parameter                       | Function                                                                                                                   |
|---------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| - -time=\<hh:mm:ss\>           | Time limit for job. Job will be killed by SLURM after time has run out. Format: hours:minutes:seconds                  |
| - -nodes=\<num_nodes\>           | Number of nodes. Multiple nodes are only useful for jobs with distributed-memory (e.g. MPI).                               |
| - -mem=\<MB/GB\>                    | Memory (RAM) per node. Number followed by unit prefix, e.g. 16G                                                            |
| - -mem-per-cpu=\<MB/GB\>            | Memory (RAM) per requested CPU core                                                                                        |
| - -ntasks-per-node=\<num_procs\> | Number of (MPI) processes per node. More than one useful only for MPI jobs. Maximum number depends nodes (number of cores) |
| - -cpus-per-task=\<num_threads\> | CPU cores per task. For MPI use one. For parallelized applications benchmark this is the number of threads.                |
| - -exclusive                     | Job will not share nodes with other running jobs. You will be charged for the complete nodes even if you asked for less.   |

### Accounting

See also `label_partitions`.

| Parameter            | Function                                                                                                   |
|----------------------|------------------------------------------------------------------------------------------------------------|
| --account=\<name\>   | Project (not user) account the job should be charged to.                                                   |
| --partition=\<name\> | Partition/queue in which o run the job.                                                                    |
| --qos=devel          | On star the *devel* QOS (quality of servive) can be used to submit short jobs for testing and debugging. |

### Advanced Job Control

<table>
<thead>
<tr class="header">
<th>Parameter</th>
<th>Function</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>--array=&lt;indexes&gt;</td>
<td>Submit a collection of similar jobs, e.g. <code>--array=1-10</code>.
(sbatch command only). See official <a
href="https://slurm.schedmd.com/job_array.html">SLURM
documentation</a></td>
</tr>
<tr class="even">
<td>--dependency=&lt;state:jobid&gt;</td>
<td>Wait with the start of the job until specified dependencies have
been satified. E.g. --dependency=afterok:123456</td>
</tr>
<tr class="odd">
<td>--ntasks-per-core=2</td>
<td><b>
<p>Enables hyperthreading. Only useful in special circumstances.</p>
</b></td>
</tr>
</tbody>
</table>

## Differences between CPUs and tasks

As a new users writing your first SLURM job script the difference
between `--ntasks` and `--cpus-per-taks` is typically quite confusing.
Assuming you want to run your program on a single node with 16 cores
which SLURM parameters should you specify?

The answer is it depends whether the your application supports MPI. MPI
(message passing protocol) is a communication interface used for
developing parallel computing programs on distributed memory systems.
This is necessary for applications running on multiple computers (nodes)
to be able to share (intermediate) results.

To decide which set of parameters you should use, check if your
application utilizes MPI and therefore would benefit from running on
multiple nodes simultaneously. On the other hand you have an non-MPI
enables application or made a mistake in your setup, it doesn't make
sense to request more than one node.

## Settings for OpenMP and MPI jobs

### Single node jobs

For applications that are not optimized for HPC (high performance
computing) systems like simple python or R scripts and a lot of software
which is optimized for desktop PCs.

#### Simple applications and scripts

Many simple tools and scripts are not parallized at all and therefore
won't profit from more than one CPU core.

| Parameter           | Function                                                           |
|---------------------|--------------------------------------------------------------------|
| --nodes=1           | Start a unparallized job on only one node                          |
| --ntasks-per-node=1 | For OpenMP, only one task is necessary                             |
| --cpus-per-task=1   | Just one CPU core will be used.                                    |
| --mem=\<MB\>        | Memory (RAM) for the job. Number followed by unit prefix, e.g. 16G |

If you are unsure if your application can benefit from more cores try a
higher number and observe the load of your job. If it stays at
approximately one there is no need to ask for more than one.

#### OpenMP applications

OpenMP (Open Multi-Processing) is a multiprocessing library is often
used for programs on shared memory systems. Shared memory describes
systems which share the memory between all processing units (CPU cores),
so that each process can access all data on that system.

| Parameter                       | Function                                                           |
|---------------------------------|--------------------------------------------------------------------|
| --nodes=1                       | Start a parallel job for a shared memory system on only one node   |
| --ntasks-per-node=1             | For OpenMP, only one task is necessary                             |
| --cpus-per-task=\<num_threads\> | Number of threads (CPU cores) to use                               |
| --mem=\<MB\>                    | Memory (RAM) for the job. Number followed by unit prefix, e.g. 16G |

### Multiple node jobs (MPI)

For MPI applications.

Depending on the frequency and bandwidth demand of your setup, you can
either just start a number of MPI tasks or request whole nodes. While
using whole nodes guarantees that a low latency and high bandwidth it
usually results in a longer queuing time compared to cluster wide job.
With the latter the SLURM manager can distribute your task across all
nodes of star and utilize otherwise unused cores on nodes which for
example run a 16 core job on a 20 core node. This usually results in
shorter queuing times but slower inter-process connection speeds.

We strongly advice all users to ask for a given set of cores when
submitting multi-core jobs. To make sure that you utilize full nodes,
you should ask for sets that adds up to both 16 and 20 (80, 160 etc) due
to the hardware specifics of Star i.e. submit the job with
`--ntasks=80` **if** your application scales to this number of tasks.

This will make the best use of the resources and give the most
predictable execution times. If your job requires more than the default
available memory per core (32 GB/node gives 2 GB/core for 16 core nodes
and 1.6GB/core for 20 core nodes) you should adjust this need with the
following command: `#SBATCH --mem-per-cpu=4GB` When doing this, the
batch system will automatically allocate 8 cores or less per node.

#### To use whole nodes

| Parameter                       | Function                                                                                                                      |
|---------------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| --nodes=\<num_nodes\>           | Start a parallel job for a distributed memory system on several nodes                                                         |
| --ntasks-per-node=\<num_procs\> | Number of (MPI) processes per node. Maximum number depends nodes (16 or 20 on Star)                                         |
| --cpus-per-task=1               | Use one CPU core per task.                                                                                                    |
| --exclusive                     | Job will not share nodes with other running jobs. You don't need to specify memory as you will get all available on the node. |

#### To distribute your job

| Parameter              | Function                                                                     |
|------------------------|------------------------------------------------------------------------------|
| --ntasks=\<num_procs\> | Number of (MPI) processes in total. Equals to the number of cores            |
| --mem-per-cpu=\<MB\>   | Memory (RAM) per requested CPU core. Number followed by unit prefix, e.g. 2G |

### Scalability

You should run a few tests to see what is the best fit between
minimizing runtime and maximizing your allocated cpu-quota. That is you
should not ask for more cpus for a job than you really can utilize
efficiently. Try to run your job on 1, 2, 4, 8, 16, etc., cores to see
when the runtime for your job starts tailing off. When you start to see
less than 30% improvement in runtime when doubling the cpu-counts you
should probably not go any further. Recommendations to a few of the most
used applications can be found in `sw_guides`.

## Job related environment variables

Here we list some environment variables that are defined when you run a
job script. These is not a complete list. Please consult the SLURM
documentation for a complete list.

Job number:

    SLURM_JOBID
    SLURM_ARRAY_TASK_ID  # relevant when you are using job arrays

List of nodes used in a job:

    SLURM_NODELIST

Scratch directory:

    SCRATCH  # defaults to /global/work/${USER}/${SLURM_JOBID}.star-adm.uit.no

We recommend to **not** use $SCRATCH but to construct a variable
yourself and use that in your script, e.g.:

    SCRATCH_DIRECTORY=/global/work/${USER}/my-example/${SLURM_JOBID}

The reason for this is that if you forget to sbatch your job script,
then $SCRATCH may suddenly be undefined and you risk erasing your entire
/global/work/${USER}.

Submit directory (this is the directory where you have sbatched your
job):

    SUBMITDIR
    SLURM_SUBMIT_DIR

Default number of threads:

    OMP_NUM_THREADS=1

Task count:

    SLURM_NTASKS

## Partitions (queues) and services

SLURM differs slightly from the previous Torque system with respect to
definitions of various parameters, and what was known as queues in
Torque may be covered by both `--partition=...` and `--qos=...`.

We have the following partitions:

normal:  
The default partition. Up to 48 hours of walltime.

singlenode:  
If you ask for less resources than available on one single node, this
will be the partition your job will be put in. We may remove the
single-user policy on this partition in the future. This partition is
also for single-node jobs that run for longer than 48 hours.

multinode:  
Request this partition if you ask for more resources than you will find
on one node and request walltime longer than 48 hrs.

highmem:  
Use this partition to use the high memory nodes with 128 GB. You will
have to apply for access to this partition by sending us an email
explaining why you need these high memory nodes.

To figure out the walltime limits for the various partitions, type:

    $ sinfo --format="%P %l"  # small L

As a service to users that needs to submit short jobs for testing and
debugging, we have a service called devel. These jobs have higher
priority, with a maximum of 4 hrs of walltime and no option for
prolonging runtime.

Jobs in using devel service will get higher priority than any other jobs
in the system and will thus have a shorter queue delay than regular
jobs. To prevent misuse the devel service has the following limitations:

-   Only one running job per user.
-   Maximum 4 hours walltime.
-   Only one job queued at any time, remark this is for the whole queue.

You submit to the devel-service by typing:

    #SBATCH --qos=devel

in your job script.

## General job limitations

The following limits are the default per user in the batch system. Users
can ask for increased limits by sending a request to
<support@metacenter.no>.

| Limit                      | Value          |
|----------------------------|----------------|
| Max number of running jobs | 1024           |
| Maximum cpus per job       | 2048           |
| Maximum walltime           | 28 days        |
| Maximum memory per job     | No limit \[1\] |

\[1\] There is a practical limit of 128GB per compute node used.

**Remark:** Even if we impose a 28 day run time limit on Star we only
give a weeks warning on system maintenance. Jobs with more than 7 days
walltime, will be terminated and restarted if possible.

See `about_star` chapter of the documentation if you need more
information on the system architecture.