---
sort: 4
---

# Monitoring Jobs

Here you can see how to manage and monitor your jobs on our HPC cluster. Whether you're running batch jobs, interactive sessions, or array jobs, these tools and commands will help you keep track of your work and manage your resources.

## Checking Job Status

### Using `squeue`

The `squeue` command is possibly your most common tool for viewing the status of jobs in the queue. Here's a basic usage:

```bash
squeue
```

Sample output:

```bash
  JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   1234     batch   my_job   jsmith  R       5:23      1 cn01
   1235     batch  arr_job     jdoe  R       2:45      1 cn02
   1236       gpu gpu_task   asmith PD       0:00      1 (Resources)
```

To see **only** your job:

```bash
squeue -u your_username
```

To see jobs on a specific partition:

```bash
squeue -p partition_name
```

### Jobs States

These are common job states that you might see under the `ST` column of `squeue`'s output:

- R: Running
- PD: Pending
- CG: Completing
- CD: Completed
- F: Failed
- TO: Timeout
- CA: Cancelled

## Detailed Job Information

### Using `scontrol`

To get detailed informatnio about a specific job:

```bash
scontrol show job job_id
```

Sample output:

```bash
JobId=1234 JobName=my_job
   UserId=jsmith(1001) GroupId=users(1001) MCS_label=N/A
   Priority=4294901758 Nice=0 Account=default QOS=normal
   JobState=RUNNING Reason=None Dependency=(null)
   Requeue=1 Restarts=0 BatchFlag=1 Reboot=0 ExitCode=0:0
   RunTime=00:10:12 TimeLimit=01:00:00 TimeMin=N/A
   SubmitTime=2023-06-01T10:00:00 EligibleTime=2023-06-01T10:00:00
   AccrueTime=2023-06-01T10:00:00
   StartTime=2023-06-01T10:05:00 EndTime=2023-06-01T11:05:00 Deadline=N/A
   PreemptEligibleTime=2023-06-01T10:05:00 PreemptTime=None
   SuspendTime=None SecsPreSuspend=0 LastSchedEval=2023-06-01T10:05:00
   Partition=batch AllocNode:Sid=login01:12345
   ReqNodeList=(null) ExcNodeList=(null)
   NodeList=cn01
   BatchHost=cn01
   NumNodes=1 NumCPUs=1 NumTasks=1 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
   TRES=cpu=1,mem=4G,node=1,billing=1
   Socks/Node=* NtasksPerN:B:S:C=0:0:*:* CoreSpec=*
   MinCPUsNode=1 MinMemoryNode=4G MinTmpDiskNode=0
   Features=(null) DelayBoot=00:00:00
   OverSubscribe=OK Contiguous=0 Licenses=(null) Network=(null)
   Command=/home/jsmith/my_script.sh
   WorkDir=/home/jsmith
   StdErr=/home/jsmith/my_job.err
   StdIn=/dev/null
   StdOut=/home/jsmith/my_job.out
   Power=
```

### Cancelling Jobs

To cancel a job:

```bash
scancel job_id
```

To cancel all your jobs:

```bash
scancel -u your_username
```

To cancel all your pending jobs:

```bash
scancel -t PENDING -u your_username
```

## Modifying Jobs

If you initially submit a job and then remember some attribute needs to be changed, you don't need to cacnel and resubmit the whole job. You can modify certain attributes of a job that's already in the queue using the `scontrol update` command.

For example, to change the time limit of a job:

```bash
scontrol update JobId=job_id TimeLimit=2:00:00
```

To change the number of CPUs:

```bash
scontrol update JobId=job_id NumCPUs=4
```

## Monitoring Resource Usage

### Using `sstat`

For running jobs, you can use `sstat` to get resource usage statistics:

```bash
sstat -j job_id --format=JobID,AveCPU,AveRSS,AveVMSize
```

Sample output:

```bash
JobID     AveCPU        AveRSS        AveVMSize
-------- ------------ ------------ -----------
1234.0      00:05:23      1234K         4567K
```

### Using `sacct`

For completed jobs, use `sacct` o view accounting data:

```bash
sacct -j job_id --format=JobID,JobName,MaxRSS,Elapsed
```

Sample output:

```bash
JobID    JobName     MaxRSS    Elapsed
------------ ---------- ---------- ----------
1234         my_job        4096K   00:15:23
```

## Monitoring Cluster Status

### Using `sinfo`

To see the overall status of the cluster:

```bash
sinfo
```

Sample output:

```bash
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
defq*        up   infinite      1    mix gpu1
defq*        up   infinite      2   idle cn01,gpu2
```

To see more detailed node information on for example `gpu1`:

```bash
sinfo -n gpu1 -o "%n %c %m %t %f %G %D %P %C %O"
```

Sample output:

```bash
HOSTNAMES CPUS MEMORY STATE AVAIL_FEATURES GRES NODES PARTITION CPUS(A/I/O/T) CPU_LOAD
gpu1 128 1 mix location=local (null) 1 defq* 5/123/0/128 1.67
```

## Job Arrays

For job arrays, you can use most of the above commands with some modifications.

To see the status of all tasks in a job array:

```bash
squeue -j array_job_id
```

Sample output:

```bash
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
1234_1     batch array_job    jdoe  R       5:23      1 cn01
1234_2     batch array_job    jdoe  R       5:23      1 cn02
1234_3     batch array_job    jdoe PD       0:00      1 (Resources)
```

## Live monitoring of GPUs usage (clock/mem) via [nvtop](https://github.com/Syllo/nvtop)

While running a job launched with `sbatch`, it can be useful to monitor the GPU load on the assigned computing node. One quick way to do this is by accessing the computing node and running a command within it.  

Since our computing nodes have no internet access and direct SSH login from the login node is not allowed for security reasons, the following script provides a workaround. It allows you to attach an interactive session to a running job, identified by `<job_id>`, and check GPU usage from within the node.

```bash
#!/bin/bash
if [ -z "$1" ]; then
    echo "Usage: $0 <jobid>"
    exit 1
fi

JOBID=$1

srun --jobid=${JOBID} --pty bash -c '
    # Try to load the nvtop module
    if ! module add nvtop/3.0.1-gcc-8.5.0-tqonkuf 2>/dev/null; then
        echo "Error: Failed to load module nvtop/3.0.1-gcc-8.5.0-tqonkuf"
        exit 1
    fi

    # Check that nvtop is available
    if ! command -v nvtop >/dev/null 2>&1; then
        echo "Error: nvtop not found in PATH after loading module"
        exit 1
    fi

    # Run nvtop
    exec nvtop
'
```

---

This can be executed (after running `chmod +x <script_name>`) with

1. `./gtop <id_job>` where `<id_job>` is retrieved using `squeue`, or
2. with `./gtop $(squeue --name="job_name" --noheader --format="%i" | head -n 1)` in case one wants to retrieve the job id via the name.

Note that a limitation of the second method is that Slurm does not guarantee that job names are unique, so multiple jobs may share the same name.

## Troubleshooting

If a job fails, try checking the following:

1. Look at the job's output and error files.
2. Check the job state and exit code:

   ```

sacct --brief

```
   Sample output:
   ```

JobID             State ExitCode
------------ ---------- --------
1040            TIMEOUT      0:0
1041             FAILED      6:0
1042            TIMEOUT      0:0
1043             FAILED      1:0
1046          COMPLETED      0:0
1047            RUNNING      0:0

```
   `FAILED` indicates the process terminated with with a non-zero exit code.
   The first number in the ExitCode column is the exit code and the number after the colon is the signal that caused the process to terminate if it was terminated by a signal.

3. Check the job's resource usage with `sacct`
4. Verify that you requested sufficient resources, and your job did not get terminated due to needing more resources than requested.

If you face persistent issues, please do not hesitate to reach out to us for help.
