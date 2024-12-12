---
sort: 4
---

# Scheduler Policies

This guide aims to help users understand the scheduling system and resource allocation policies of the Star HPC cluster to allow users to strategize their job submissions and optimize their usage of the cluster. By submitting job and requesting resources effectively, you can enhance job efficiency, reduce wait times, and make the most of the cluster’s capabilities.

## Why Is My Job Not Running Yet?

You can use the `squeue -j <jobid>` command to see the status of your job and the reason why it is not running. There are a number of possible reasons why your job could have a long queue time or could even be prevented from running indefinately. The queue time is based on several [scheduling priority factors](#scheduling-priority-factors) and may be impacted by the availability of high-demand or scarce resources, or dependency constraints. It is also possible that your job is asking for more resources than exist or have been allotted, in which case it will never start.

To help identify issues with a job and to optimize your job submissions for faster execution, you should understand how the scheduler works and the factors that are at play. Key scheduling concepts to understand include priority factors such as [fairshare](https://slurm.schedmd.com/priority_multifactor.html#fairshare){:target="_blank"} and [QoS](https://slurm.schedmd.com/qos.html){:target="_blank"}, and [backfilling](https://slurm.schedmd.com/sched_config.html#backfill){:target="_blank"}. More advanced concepts include [reservations](https://slurm.schedmd.com/reservations.html){:target="_blank"}, [oversubscription](https://slurm.schedmd.com/cons_tres_share.html){:target="_blank"}, [preemption](https://slurm.schedmd.com/preempt.html){:target="_blank"}, and [gang scheduling](https://slurm.schedmd.com/gang_scheduling.html){:target="_blank"}.

### When Will My Job Start?

While exact start times cannot be guaranteed due to the dynamic nature of the cluster's workloads, you can get an estimate:

```bash
squeue -j <jobid> --start
```

This command will give you information about how many CPUs your job requires, for how long, as well as when approximately it will start and complete. It must be emphasized that this is just a best guess, queued jobs may start earlier because of running jobs that finishes before they hit the walltime limit and jobs may start later than projected because new jobs are submitted that get higher priority.

You can also look at the Slurm queue to get a sense of how many other jobs are pending and where your job stands in the queue:

```bash
squeue -p <partition_name> --state=PD -l
```

To see the jobs run by other users in your group, specify the account name:

```bash
squeue -A <account>
```
Review your fairshare score using sshare to understand how your recent resource usage might be affecting your job's priority.

### Scheduling Priority Factors

If your job is sitting in the queue for a while, its priority could be lower than other jobs due to one or more factors such as high fairshare usage from previous jobs, a high number of in-demand resources being requested, or a long wall time being requested. This is because the Star cluster leverages Slurm's Backfill scheduler and Multifactor Priority plugin, which considers several factors in determining a job's priority, unlike simple First-In, First-Out (FIFO) scheduling. The backfill scheduler with the priority/multifactor plugin provides a more balanced and performant approach than FIFO.

There are nine factors that influence the overall [job priority](https://slurm.schedmd.com/priority_multifactor.html#general), which affects the order in which the jobs are scheduled to run. The job priority is calculated from a weighted sum of all the following factors:

- **Age**: the length of time a job has been waiting in the queue and eligible to be scheduled
- **Association**: a factor associated with each association
- **Fairshare**: the difference between the portion of the computing resource that has been promised and the amount of resources that has been consumed
- **Nice**: a factor that can be set by users to prioritize their own jobs. This factor is currently not enabled for our cluster.
- **Job size**: the number of nodes or CPUs a job is allocated
- **Partition**: a factor associated with each node partition
- **QoS**: a factor based on the priority of the Quality of Service (QoS) associated with the job
- **Site**: a factor dictated by an administrator or a site-developed job_submit or site_factor plugin
- **TRES**: A TRES is a resource that can be tracked for usage or used to enforce limits against. Each TRES type has its own factor for a job which represents the number of requested/allocated TRES type in a given partition.

#### i) Fairshare

The fairshare factor reflects the recent resource usage of an account relative to its allotted share. An account's allotted share is determined by values set at multiple levels in the account hierarchy that represent the relative amount of the computing resources assigned to each account relative to others.

The fairshare factor influences the priority of jobs based on the amount of resources that have been previously consumed in relation to the share of resources allocated for the given account, so as to ensure all accounts have a "fair-share" of the resources.

As a result, the more resources your recent jobs have used relative to your account's allocation, the lower the priority will be for future jobs submitted through your account in comparison other accounts that have used fewer resources. This allows underutilized accounts to gain higher priority over heavily utilized accounts that have been allocated the same or similar amount of resources. As the fairshare value is typically set at the account level and multiple users may belong to the same account, the usage of one user can negatively affect other users in that same account. So, if there are two members of a given account, and one user runs many jobs under that account, the priority of any future jobs submitted by the other user (who may never even have run any jobs at all) would also be negatively affected. This ensures that the combined usage of an account matches the portion of resources that has been allocated to it.


##### Command line examples:

1. **Displaying the sharing and fairshare information of your user in your account.**
   ```bash
   $ sshare -l -U -o Account,User,NormShares,RawUsage,NormUsage,EffectvUsage,FairShare,TRESRunMins%100
   ```

   **Sample Output:**
   ```text
   Account              User     NormShares  RawUsage NormUsage  EffectvUsage   FairShare                                        TRESRunMins
   -----------------------------------------------------------------------------------------------------------------
   ResearchGroup1 <user>       0.000705       100942    0.000081       0.000003     0.997186   cpu=127851,mem=465054140,gres/gpu=27134
   ```

2. **Displaying the FairShare information of all users of your account**
   ```bash
   $ sshare -a --accounts=<account>
   ```

   **Sample Output:**
   ```text
   Account                        User      RawShares     NormShares    RawUsage  EffectvUsage    FairShare
   ------------------------------------------------------------------------------------------------------------------------------------
   ResearchGroup1                                           1              0.023256           117684        0.000094      0.997199
     ResearchGroup1    <user1>                        1              0.000705             16765       0.000003      0.997193
     ResearchGroup1    <user2>                        1              0.000705           100918       0.000003      0.997187
   ```

3. **Displaying a summary of the six factors configured that comprise each job’s scheduling priority**

   The sprio -w option displays the weights (PriorityWeightAge, PriorityWeightFairshare, etc.) for each factor as it is currently configured.

   ```bash
   $ sprio -w
   ```

   **Sample Output:**
   ```text
   JOBID   PARTITION   PRIORITY   SITE    FAIRSHARE   JOBSIZE   PARTITION   QOS
   ---------------------------------------------------------------------------------------------------------------------
   Weights                                                1               10000          1000              1000    1000
   ```

#### ii) Backfilling

Backfilling is a technique to optimize resource utilization. If a large job is waiting for specific resources, the scheduler allows smaller jobs to run in the meantime, provided they don't delay the start of the higher-priority job. This approach keeps the cluster busy and reduces idle time.

### Resource Availability

The required resources may not be available at the moment. Jobs might have to wait longer for sufficient resources to free up. Resources are allocated to accounts through the fairshare mechanism. I.e., accounts have a number of shares that determine their entitled resource allocation. The number of resources that a given job may consume is also constrained by the job's QoS policy.

## Are Slurm accounts the same as Star HPC user accounts?

No, a Slurm account is something entirely different. Users can belong to multiple Slurm accounts and multiple users can belong to a single Slurm account. Slurm accounts typically correspond with a group of users and we generally create them on a project basis. Slurm accounts are used for tracking usage and enforcing resource limits. When new project accounts are created, they will consume a portion of the shares allocated for the parent account, which is typically associated with a broader administrative entity, such as an institution, department, or research group.

## What QoS Should I Choose for My Job?

QoS (Quality of Service) define job with different priorities and resource limits. Selecting the appropriate QoS can influence your job’s priority in the queue. Be mindful of the tradeoff that comes with the long QoS. While long QoS allows more runtime for your jobs, they may result in longer wait times due to lower scheduling priority.

- **short**: For jobs up to 1 hour, with higher priority, suitable for testing and quick tasks.
- **medium**: For jobs up to 48 hours, balanced priority for standard workloads.
- **long**: For jobs up to 120 hours, lower priority due to resource demands, suitable for extensive computations.


## Can I Have More Resources?

Non-technical explanation:

It depends. We don’t have unlimited resources, so please try to make the most of the resources available. Moreover, it is quite possible that you are not using the resources that you have completely. Using your current resources completely may suffice your needs.

Before requesting additional resources, make sure you are optimally using the resources you have already been allocated. To request an additional allocation, provide a brief justification, which may include how you are using your current allocation.

Technical explanation:

The fairshare mechanism used to ensure fair usage between accounts does not actually limit the amount of resources that can be requested or consumed. It only adjusts each job's scheduling priority based on resource usage history and the account's fair-share entitlement. Resource limits may be imposed on an account, user, partition, or job by association or QoS policy though. These usage limits will be reevaluated periodically and may be adjusted based on legitimate need or usage patterns.

## How Can I Make Sure That I Am Using My Resources Optimally?

You can tailor your job according to its duration and the resources it needs.

When submitting a **short job**, consider using the **short QoS** to gain higher priority in the queue. Request only the necessary resources to keep the job lightweight and reduce wait times.

**Example:**

```bash
#!/bin/bash
#SBATCH --job-name=debug_job
#SBATCH --partition=mixed-use
#SBATCH --qos=short
#SBATCH --time=00:30:00
#SBATCH --mem=1G

module load python3
python3 quick_task.py
```

Note that `quick_task.py`'s name and location needs to be changed relative to _your_ file(s). quick_task.py is the actual job script that you want to run.

## **How Can I Submit a Long Job?**

For long jobs, select the **long QoS**, which allows for extended runtimes but may have lower scheduling priority. Note that while specifying the qos you still need to specify the walltime (\--time=). The difference between walltime and cpu time from this simple example: If a job is running for one hour using two CPU cores, the walltime is one hour while the cpu-time is 1hr x 2CPUs = 2 hours.

It’s advisable to implement **checkpointing** in your application if possible. Checkpointing allows your job to save progress at intervals, so you can resume from the last checkpoint in case of interruptions, mitigating the risk of resource wastage due to unexpected failures.

Be aware of **fairshare implications**; consistently running long jobs can reduce your priority over time. Plan your submissions accordingly to balance resource usage.

**Example:**

```bash
#!/bin/bash
#SBATCH --job-name=long_job
#SBATCH --partition=gpu-large
#SBATCH --qos=long
#SBATCH --time=120:00:00
#SBATCH --nodes=2
#SBATCH --mem=64G

module load python3
python3 my_long_job.py
```

This .sbatch script requests sufficient time and resources for an extended computation, using the **long QoS**. my_long_job.py is the python file which is the job that you want to run

## **What Can I Do to Get My Job Started More Quickly? Any Other PRO Tips?**

1. **Shorten the time limit** on your job, if possible. This may allow the scheduler to fit your job into a time window while it is trying to make room for a larger job (using Slurm's **backfill functionality**).

2. **Request fewer nodes** (or fewer cores on partitions scheduled by core), if possible. This may also allow the scheduler to fit your job into a time window while it is waiting to make room for larger jobs.

3. **Resource Estimation**:
   Monitor the resource usage of your previous jobs to inform future resource requests. Use tools like `sacct` to review past job statistics.


4. **Efficient Job Scripts**:
   Simplify your job scripts by removing unnecessary module loads and commands. This reduces overhead and potential points of failure.

5. **Implement Checkpointing**:
   For long-running jobs, incorporate checkpointing to save progress at intervals. This allows you to resume computations without starting over in case of interruptions.

6. **Avoid Over-Requesting Resources**:
   Requesting more CPUs, memory, or time than needed can increase your job’s wait time and negatively impact **fairshare calculations**.

7. **Understand Scheduling Policies**:
   Familiarize yourself with the cluster’s **scheduling policies**, including **fairshare** and **backfilling**. This knowledge can help you strategize your job submissions for better priority.

8. **Communicate with Your Group**:
   If you’re part of a research group, coordinate resource usage to avoid collectively lowering your group’s **fairshare priority**.

