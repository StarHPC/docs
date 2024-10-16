---
sort: 1
---

# Overview

## What are jobs

In the context of High-Performance Computing (HPC), a job is a unit of work that you submit to the cluster for execution. This work can range from a simple command to a complex series of computations that utilize the cluster's computing resources, including CPUs, GPUs, and memory. Jobs are executed on the cluster's computing nodes, rather than on the login or head node, to ensure that intensive computations do not interfere with the system's overall operation and performance.

**Example Scenario**: Suppose you're conducting a simulation. On the cluster, you would submit this task as a job. This job would detail the resources needed (e.g., "use 2 of the A100 GPUs on node `gpu1`"), the software to run (perhaps a simulation tool contained within an Apptainer container), and the input data. The job system then queues your request and executes it on the appropriate compute nodes when resources become available, without you needing to manually log into those nodes and start the computations.

### Why are jobs used?

* **Efficiency**: Jobs allow for the efficient use of the cluster's resources by managing the workload and ensuring that resources are not idle. This is especially important in environments where resources are shared among multiple users. 
* **Scalability**: Jobs can be scaled across multiple nodes and processors, based on your preference and resource requirements.
* **Control**: Through job specifications, you have control over the resources allocated to your task, such as the number of CPUs/GPUs, memory, and runtime. This ensures that your job runs under optimal conditions tailored to its requirements.

## Job types
In HPC environments, choosing the right job type is like choosing the right gear for your workload. Each job type is suited for different computing needs, ranging from straightforward tasks to complex, large-scale computations.

### 1. Interactive jobs
Interactive jobs serve as your direct path to the cluster's computing resources by enabling real-time interaction with your applications. This job type is typically used for tasks necessitating prompt feedback, such as exploratory data analysis or debugging phases of development.
#### Example Scenario
Imagine a user is optimizing a complex algorithm's parameters. By initiating an interactive job, the user can execute adjustments in real-time, directly observing the effects of their tweaks on the algorithm's performance.

### 2. Batch jobs
Batch jobs are submitted to a queue on the cluster and run without user interaction. This is the most common job type for tasks that don't require real-time feedback.
#### Example Scenario
You've developed a script for processing a large dataset that requires no human interaction to complete its task. By submitting this as a batch job, the cluster undertakes the task, allowing the job to run to completion and output the results to your desired location for you to view.
For a real example on Batch jobs, view [Submitting jobs]({{site.baseurl}}{% link jobs/submitting-jobs.md %}).

### 3. Array jobs
When you're faced with executing the same task multiple times with only slight variations, array jobs offer an efficient solution. This job type simplifies the process of managing numerous similar jobs by treating them as a single entity that varies only in a specified parameter.
#### Example Scenario
Suppose a user has to analyze 100 different genomic sequences with the same computational tool. Instead of submitting 100 separate batch jobs, they can submit a single array job that processes each sequence in parallel,which significantly simplifies job management and resource allocation.

### 4. Parallel jobs
Parallel jobs are designed to run computations concurrently across multiple nodes or processors, which is ideal for highly parallelizable tasks. These jobs can significantly reduce computation time for suitable tasks by taking advantage of the cluster's distributed resources.
#### Example Scenario
Imagine a fluid dynamics job that requires complex calculations spread over millions of grid points. By submitting a parallel job, the simulation can for instance be distributed across multiple A100 GPUs on both `gpu1` and `gpu2`, and enable the task to complete much faster than it would on a single node.

## Resources
Resources within an HPC environment are finite and include CPUs, GPUs, memory, and storage. <br>
For a list of the resources available at Star HPC, take a look at [About star]({{site.baseurl}}{% link quickstart/about-star.md %}).

### Common Errors
Strains on the cluster occur when resources are over-requested or misallocated, leading to potential bottlenecks, decreased system performance, and extended wait times for job execution. <br>
Common issues arise from: <br>
* **Resource Limit Exceeded**: Occurs when a job requests more resources than are available or exceeds the quota allocated to a user.
* **Job Timeout**: A job may exceed the maximum allowed runtime due to underestimated resource requirements and not finish on it, resulting in a forced termination.
* **Insufficient Memory**: Jobs that attempt to use more memory than allocated can crash or cause system instability.

### Unhealthy Jobs
#### Example Scenario 1
Imagine a user submits a job for data analysis that only needs a minimal set of resources but requests all 8 A100 GPUs on `gpu1`, anticipating that would speed up the process. Counterintuitively, this would make it take longer until the job is actually executed as the job scheduler must wait until all the requested resources are available for the job to run. Furthermore, if the job only actively uses one GPU, the remaining seven would be idle but left unavailable to other users. This is a significant overallocation of resources, leading to inefficiencies and potential delays for other tasks requiring GPU support.

#### Example Scenario 2
A user requests more resources than are physically available on the cluster. For instance, a user submits a job that requests 9 A100 GPUs on `gpu1`, where the maximum is 8. This would cause the job to fail immediately and produce an error.
