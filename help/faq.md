---
sort: 1
---

# Frequently asked questions

## Passwords

### I forgot my password - what now?

{% comment %}You can reset it here: [link to be provided]{% endcomment %}

Please contact the [support team]({{site.baseurl}}{% link help/contact.md %}).

### How do I change my password on Star?

You can run the `passwd` command on the login node to change your password. Please note `passwd` will have no affect from the compute nodes.

{% comment %}A web portal is currently under development. Once launched, your password can also be changed from the password reset page, [link to be provided]. Log in using your username on Star.{% endcomment %}

### What is the ssh key fingerprint for star.hofstra.edu?

The SHA256 key fingerprint is:
`SHA256:W0NKVfQBl5FeOlOkoEIKIVsp1+47yIvzJAYMx6ECpwM`

If you are more of a visual person, run
`ssh -p 5010 -o VisualHostKey=yes binary.star.hofstra.edu` and compare it to this visual
key:

    +--[ED25519 256]--+
    |E + ++  . .o=.+=.|
    |o=.=o..o . . +ooo|
    |* +o .. o o  .o+ |
    |+o    .. +    =  |
    |..   .  S o    o |
    | .    .  o .     |
    |  o... ..        |
    | ..+o o          |
    |  .oo. .         |
    +----[SHA256]-----+

## Installing software

### I need Python package X but the one on Star is too old or I cannot find it

You can choose different Python versions with either the module system
or using Anaconda/Miniconda. See [Environment modules]({{site.baseurl}}{% link software/env-modules.md %}).

In cases where this still doesn't solve your problem or you would like
to install a package yourself, please read the next section below about
installing without sudo rights.

If we don't have it installed, and installing it yourself is not a good
solution for you, please contact us and we will do our best to help you.

### Can I install Python software as a normal user without sudo rights?

Yes. Please see [Virtual environments]({{site.baseurl}}{% link software/virtual-env.md %}).

## Compute and storage quota

### How can I check my disk quota and disk usage?

To check the disk quota of your home directory ( /home/username ), you can use the repquota command which prints a summary of the disc usage and quotas for the specified file systems.

    $ /usr/sbin/repquota -a -s
    $                    Block limits                 File limits
    $ User              used    soft    hard  grace    used   soft  hard  grace
    $ cchave6    --     116M   1024M   1280M           1922      0     0

Here,

Soft Limit -> This is a warning threshold. A user can exceed this limit temporarily, but they must reduce usage back under this limit within a "grace period."

Hard Limit -> This is the absolute maximum disk space or number of files a user can use. The user cannot exceed this limit at all.

Grace Period -> The amount of time a user is allowed to exceed the soft limit before they are required to get back under it. If this period expires, the soft limit becomes enforced like a hard limit.

File limits (inodes) -> These limit the number of files a user can create, regardless of their size.



To check the quota of the main project storage (parallel file system - `/fs1/proj/<project>`), you can use this command:

    $ mmlsquota -j <fileset_name> <filesystem_name>

The -j option specifies that you are querying a fileset. Filesets in GPFS are similar to directories that can have independent quota limits.

fileset_name -> This is the name of the fileset whose quota you want to check.

filesystem_name -> The name of the GPFS filesystem in which the fileset resides.

example: mmlsquota -j project_fileset gpfs1


### How many CPU hours have I spent?

This command gives you a report of account utilization, including CPU hours, for the specified period.

    $ sreport cluster AccountUtilizationByUser start=YYYY-MM-DD end=YYYY-MM-DD

## Connecting via ssh

### How can I export the display from a compute node to my desktop?

If you need to export the display from a compute node to your desktop
you should

1.  First login to Star with display forwarding.
2.  Then you should reserve a node, with display forwarding, trough the
    queuing system.

Here is an example:

    $ ssh -Y binary.star.hofstra.edu                 # log in with port forwarding
    $ srun -N 1 -t 1:0:0 --pty bash -I     # reserve and log in on a compute node

This example assumes that you are running an X-server on your local
desktop, which should be available for most users running Linux, Unix
and Mac Os X. If you are using Windows you must install some X-server on
your local PC.

### How can I access a compute node from the login node?

Please read about Interactive jobs at [Submitting jobs]({{site.baseurl}}{% link jobs/submitting-jobs.md %}).

### My ssh connections are dying / freezing

How to prevent your ssh connections from dying / freezing.

If your ssh connections more or less randomly are dying / freezing, try
to add the following to your *local* `~/.ssh/config` file:

    ServerAliveCountMax 3
    ServerAliveInterval 10

(*local* means that you need to make these changes to your computer, not
on star)

The above config is for [OpenSSH](https://www.openssh.org), if you're
using
[PUTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)
you can take a look at this page explaining
[keepalives](https://the.earth.li/~sgtatham/putty/0.60/htmldoc/Chapter4.html#config-keepalive)
for a similar solution.

### Where can I find an example of job script?

You can find job script examples at [Submitting jobs]({{site.baseurl}}{% link jobs/submitting-jobs.md %}).

### When will my job start?

To find out approximately when the job scheduler thinks your job will
start, use the command:

    squeue --start -j <job_id>

This command will give you information about how many CPUs your job
requires, for how long, as well as when approximately it will start and
complete. It must be emphasized that this is just a best guess, queued
jobs may start earlier because of running jobs that finishes before they
hit the walltime limit and jobs may start later than projected because
new jobs are submitted that get higher priority.

### How can I see the queing situation?

In the command line, see the job queue by using `squeue`.

For a more comprehensive list of commands to monitor/manage your jobs, please see [Monitoring jobs]({{site.baseurl}}{% link jobs/monitoring-jobs.md %}).

### Why does my job not start or give me error feedback when submitting?

Most often the reason a job is not starting is that Star is full at
the moment and there are many jobs waiting in the queue. But sometimes
there is an error in the job script and you are asking for a
configuration that is not possible on Star. In such a case the job
will not start.

To find out how to monitor your jobs and check their status see [Monitoring jobs]({{site.baseurl}}{% link jobs/monitoring-jobs.md %}).

Below are a few cases of why jobs don't start or error messages you
might get:

**Memory per core**

> "When I try to start a job with 2GB of memory pr. core, I get the
> following error:
> `sbatch: error: Batch job submission failed: Requested node configuration is not available`
> With 1GB/core it works fine. What might be the cause to this?"

On Star we have two different configurations available; 16 core and 20
core nodes - with both a total of 32 GB of memory/node. If you ask for
full nodes by specifying both number of nodes and cores/node together
with 2 GB of memory/core, you will ask for 20 cores/node and 40 GB of
memory. This configuration does not exist on Star. If you ask for 16
cores, still with 2GB/core, there is a sort of buffer within Slurm no
allowing you to consume absolutely all memory available (system needs
some to work). 2000MB/core works fine, but not 2 GB for 16 cores/node.

The solution we want to push in general is this:

    #SBATCH -ntasks=80 # (number of nodes * number of cores, i.e. 5*16 or 4*20 = 80)

If you then ask for 2000MB of memory/core, you will be given 16
cores/node and a total of 16 nodes. 4000MB will give you 8 cores/node -
everyone being happy. Just note the info about PE `accounting`;
mem-per-cpu 4000MB will cost you twice as much as mem-per-cpu 2000MB.

Please also note that if you want to use the whole memory on a node, do
not ask for 32GB, but for 31GB or 31000MB as the node needs some memory
for the system itself.

**Step memory limit**

> "Why do I get `slurmstepd: Exceeded step memory limit` in my
> log/output?"

For slurm, the memory flag is a hard limit, meaning that when each core
tries to utilize more than the given amount of memory, it is killed by
the slurm-deamon. For example `$SBATCH --mem-per-cpu=2GB` means that you
maximum can use 2 GB of memory per core. With memory intensive
applications like Comsol or VASP, your job will likely be terminated.
The solution to this problem is to specify the number of tasks
irrespectively of cores/node and ask for as much memory you will need.

For instance:

    #SBATCH --ntasks=20
    #SBATCH --time=0-24:05:00
    #SBATCH --mem-per-cpu=6000MB

**QOSMaxWallDurationPerJobLimit**

QOSMaxWallDurationPerJobLimit means that MaxWallDurationPerJobLimit has
been exceeded. Basically, you have asked for more time than allowed for
the given QOS/Partition.

**Priority vs. Resources**

Priority means that resources are in principle available, but someone
else has higher priority in the queue. Resources means the at the moment
the requested resources are not available.

### How can I customize emails that I get after a job has completed?

Use the mail command and you can customize it to your liking but make
sure that you send the email via the login node.

As an example, add and adapt the following line at the end of your
script:

    echo "email content" | ssh star-1.local 'mail -s "Job finished: ${SLURM_JOBID}" firstname.lastname@hofstra.edu'

### How can I run many short tasks?

The overhead in the job start and cleanup makes it unpractical to run
thousands of short tasks as individual jobs on Star.

The queueing setup on Star, or rather, the accounting system generates
overhead in the start and finish of a job of about 1 second at each end
of the job. This overhead is insignificant when running large parallel
jobs, but creates scaling issues when running a massive amount of
shorter jobs. One can consider a collection of independent tasks as one
large parallel job and the aforementioned overhead becomes the serial or
unparallelizable part of the job. This is because the queuing system can
only start and account one job at a time. This scaling problem is
described by [Amdahls Law](https://en.wikipedia.org/wiki/Amdahl's_law).

If the tasks are extremly short (e.g. less than 1 second), you can use the example below.

If you want to spawn many jobs without polluting the queueing system, please
have a look at [array jobs]({{site.baseurl}}{% link jobs/submitting-jobs.md %}#array-jobs).

By using some shell trickery one can spawn and load-balance multiple
independent task running in parallel within one node, just background
the tasks and poll to see when some task is finished until you spawn the
next:

```bash
#!/usr/bin/env bash

# Jobscript example that can run several tasks in parallel.
# All features used here are standard in bash so it should work on
# any sane UNIX/LINUX system.
# Author: roy.dragseth@uit.no
#
# This example will only work within one compute node so let's run
# on one node using all the cpu-cores:
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=20

# We assume we will (in total) be done in 10 minutes:
#SBATCH --time=0-00:10:00

# Let us use all CPUs:
maxpartasks=$SLURM_TASKS_PER_NODE

# Let's assume we have a bunch of tasks we want to perform.
# Each task is done in the form of a shell script with a numerical argument:
# dowork.sh N
# Let's just create some fake arguments with a sequence of numbers
# from 1 to 100, edit this to your liking:
tasks=$(seq 100)

cd $SLURM_SUBMIT_DIR

for t in $tasks; do
  # Do the real work, edit this section to your liking.
  # remember to background the task or else we will
  # run serially
  ./dowork.sh $t &

  # You should leave the rest alone...

  # count the number of background tasks we have spawned
  # the jobs command print one line per task running so we only need
  # to count the number of lines.
  activetasks=$(jobs | wc -l)

  # if we have filled all the available cpu-cores with work we poll
  # every second to wait for tasks to exit.
  while [ $activetasks -ge $maxpartasks ]; do
    sleep 1
    activetasks=$(jobs | wc -l)
  done
done

# Ok, all tasks spawned. Now we need to wait for the last ones to
# be finished before we exit.
echo "Waiting for tasks to complete"
wait
echo "done"
```

And here is the `dowork.sh` script:

```bash
#!/usr/bin/env bash

# Fake some work, $1 is the task number.
# Change this to whatever you want to have done.

# sleep between 0 and 10 secs
let sleeptime=10*$RANDOM/32768

echo "Task $1 is sleeping for $sleeptime seconds"
sleep $sleeptime
echo "Task $1 has slept for $sleeptime seconds"
```

Source: [HPC-UiT FAQ](https://hpc-uit.readthedocs.io/en/latest/help/faq.html)
