---
sort: 3
---

# Apptainer

## Why use Apptainer?

Apptainer is a tool available on the Star cluster for running containers.

Containers are isolated software environments that run applications packaged in an image format, which bundles the application and its dependencies. Similar to virtual machine images, the applications are already installed and are typically pre-configured. However, containers are lighter than virtual machines as containers run directly on the host operating system, while virtual machines include a full operating system of their own.

The use of containers not only allows for quicker and easier deployment of pre-configured applications, but since it isolates the application from the host system, it also simplifies dependency management, prevents potential version or dependency conflicts, and ensures consistency and reproducibility. This is especially critical with scientific applications, applications that have complex dependencies, and systems where multiple versions of the same software are needed, which is common in an HPC environment. Without the use of containers, ensuring that applications run consistently across different systems can be quite challenging due to varying software dependencies and configurations.

This approach allows you to bring already-built applications and workflows from other Linux environments to the Star cluster, and run them without any reconfiguration or additional installation. You can build a container image on your own local system and then run it on the cluster without any other setup, knowing that the application will be installed and configured the same way on both systems. An extensive ecosystem of container images is also available, so this allows you to run containerized applications without any of the hassle of setting them up or installing their dependencies in the first place.

## Why not use Docker?

Docker is probably the container platform you're most familiar with. It is widely used for development, but it was not built for HPC enviornments and is not compatible with HPC resource management or the security model of HPC clusters.

This is where Apptainer comes in. Apptainer is a Linux Foundation-supported fork of Singularity, a purpose built container platform for use in HPC environments. Like Docker, Apptainer/Singularity provides a solution for encapsulating applications and their dependencies within lightweight portable container images. Unlike Docker, Apptainer is designed with the needs of high-performance computing in mind, which makes it the go-to choice for researchers and institutions with data-intensive applications.

Apptainer has some differences from Docker. Don't worry though. It is designed to be fully compatible with Docker and it can pull and run Docker images. So, you can still run Docker locally and then bring over the same images onto Star.

## Where can you get container images?

Apptainer can run containers from any Docker compatible image repository (e.g. DockerHub). Users of the Star cluster can also leverage the large collection of HPC-tailored container images from the NVIDIA GPU Cloud (NGC) repository.

Apptainer runs `.sif` files in the Singularity Image Format, which is different from the image files that Docker uses. If you're already familiar with `.sif` files and Apptainer/Singularity, you can skip to the examples section. Otherwise, here are two ways to get containers for Apptainer:

### Converting Existing Containers or Images

If you have existing Docker container images, you can convert them to `.sif` format using Apptainer's command-line tool. Here's an example:

1. First, ensure your local Docker image is available in your Docker Daemon:
```bash
docker images
```

2. Once you've confirmed the image is available locally, you can use Apptainer to build an `sif` file from the local Docker image:
```bash
apptainer build tensorflow.sif docker-daemon://tensorflow/tensorflow:latest
```

**Note** that the `tensorflow` image is just an example. Make sure you replace it with your own image name.

This command tells Apptainer to use a locally available Docker image, make an `.sif` image file out of it, and save it as `tensorflow.sif`.

### Nvidia GPU Cloud (NGC)

But what if we want ready-to-pull HPC-tailored containers?

Nvidia GPU Cloud (NGC) containers are basically pre-configured and optimized Docker containers that include GPU-accelerated software for AI, machine learning, and high-performance computing (HPC) applications. These containers provide a ready-to-use environment with NVIDIA drivers, CUDA toolkit, and popular deep learning frameworks, that are also scanned for security vulnerabilities and exposures.

If you just pull an NGC container for the HPC software suite you have in mind, you don't need to spend time configuring complex software stacks or worry about compatibility issues between different libraries and frameworks.

You can take a look at the containers available on NGC [here](https://catalog.ngc.nvidia.com/containers?filters=&orderBy=weightPopularDESC&query=&page=&pageSize=).

### Pulling from Docker Hub

[Docker Hub](https://hub.docker.com/) is basically a cloud-based registry that allows users to store, share, and manage Docker container images.
Apptainer can also pull containers directly from Docker and automatically convert them to `.sif` format. Here's how you can do it:

```bash
apptainer pull --name tensorflow.sif docker://tensorflow/tensorflow
```

**Note** that `docker://tensorflow/tensorflow` is just an example link that is available on Docker Hub. You need to make sure you replace it with _your_ desired container link.

This command pulls the latest `tensorflow` image from Docker Hub and creates an `tensorflow.sif` file in your **current** directory (where you ran the command), which is ready for use with Apptainer.

**Warning:** Have in mind that in most cases, you will benefit from using NGC containers as they are tailored for HPC workloads and there is a very vast range of software that you could use for HPC applications.}}}

However, in some cases, you might benefit from some existing Docker containers that may not be available on NGC.

## Apptainer job examples

We will go through two examples:

1. We will setup an NGC account, use it to pull the Pytorch container, and run a sample job with it.

2. We will use a TensorFlow container and run a sample job.

### NGC's Pytorch container example

#### Create and setup an NGC account

1. Visit [this link](https://ngc.nvidia.com/signin) and enter the email you'd like to sign up with (or sign in if you have an account already and skip to step 3).

   **Note:** Don't get misguided by the "login" page. If the email you enter is not found as an already-existing account, you will automatically be redirected to the sign up page.

2. Fill out all the required fields and verify your E-mail afterwards, as prompted.

3. Login to your account, and later follow the steps at [this link](https://docs.nvidia.com/ngc/gpu-cloud/ngc-user-guide/index.html#generating-api-key) to setup an API key with which you will pull your containers.

   **Note:** You need to make sure you save your API key once it's revealed after its generation. You later need to save it on the login node.

4. Once you have your API key ready, go ahead and login to the login node (Binary).

Run the following command:
```bash
ngc config set
```

You will be prompted to enter your API key. Go ahead and paste it, and then hit Enter.

For the rest of the prompts, just enter the default available option, and hit Enter.

Upon a successful set, you will be shown the path at which the configuration has been saved to.

#### Pull and run Pytorch

If you don't find Pytorch useful, you can pull any other container found at [this link](https://catalog.ngc.nvidia.com/containers?filters=&orderBy=weightPopularDESC&query=&page=&pageSize=).

Once you find your desired container, click on it, and look for the "**Get Container v**" button at the top right of the screen.

In this example's case, our container's link is `nvcr.io/nvidia/pytorch:23.05-py3`.

Run the following command, which both pulls your container, and converts it to an `.sif` file which Apptainer can work with.
```bash
apptainer pull pytorch_23.05.sif docker://nvcr.io/nvidia/pytorch:23.05-py3
```

#### Container Tags

When pulling containers from Docker Hub or NGC, you'll notice that images often have tags (e.g., `:latest`, `:latest-gpu`, `:23.05-py3`). These tags help you specify exactly which version of a container you want to use. 

For example, when pulling the TensorFlow container, you might see different tags like:
```bash
tensorflow/tensorflow:latest        # Latest CPU-only version
tensorflow/tensorflow:latest-gpu    # Latest version with GPU support
tensorflow/tensorflow:2.13.0-gpu    # Specific version (2.13.0) with GPU support
```

Here's how you would pull a GPU-enabled version of TensorFlow using Apptainer:
```bash
apptainer pull --name tensorflow-gpu.sif docker://tensorflow/tensorflow:latest-gpu
```

**Note:** When working on the Star cluster with GPU nodes, you should generally use GPU-enabled containers (indicated by tags like `-gpu` or similar) to take full advantage of the available hardware.

If you don't specify a tag, Apptainer will default to using the `:latest` tag, which might not include GPU support. Always check the container's documentation to ensure you're using the appropriate tag for your needs.

**Remember:** Apptainer is very similar to Docker, with the most crucial difference that it runs under user privileges rather than root.

Note the `nvcr.io/nvidia/pytorch:23.05-py3` section of the command. If you are pulling another container, make sure you replace it with the proper link.

In Pytorch's case, this command is going to take a while, as the container is quite large.

Wait and let everything download and unpack as necessary. And remember this operation's time varies from container to container, based on the container size.

Once the operation is completed successfully, you will be able to see the `pytorch_23.05.sif` file at the path you ran the command in.

Now be careful! Don't get tempted to execute the container on the login node (you can't even if you try to), but the whole purpose of the cluster is to use the compute nodes and just use the login node as the entry/access point.

You can now write a script which uses the container we just installed (`pytorch_23.05.sif`) to execute some Python program. Here's how:

First, let's make our sample Pytorch program and save it inside a file called `pytorch_test.py`:
```python
import torch

def main():
    # Create a random tensor
    x = torch.rand(5, 3)
    print("Random Tensor:")
    print(x)

    # Perform a simple operation
    y = x + 2
    print("\nTensor after adding 2:")
    print(y)

    # Check if CUDA is available and use it
    if torch.cuda.is_available():
        device = torch.device("cuda")
        x = x.to(device)
        y = y.to(device)
        print("\nUsing CUDA")
    else:
        print("\nCUDA not available")

    print(f"\nCurrent device: {torch.cuda.current_device()}")
    print(f"Device count: {torch.cuda.device_count()}")
    print(f"Device name: {torch.cuda.get_device_name(0)}")

if __name__ == "__main__":
    main()
```

Now, make a script called `run_pytorch.sbatch` and save it with the following content:
```bash
#!/bin/bash
#SBATCH --job-name=pytorch_test
#SBATCH --output=/home/mani/dev/pytorch_test.out
#SBATCH --error=/home/mani/dev/pytorch_test.err
#SBATCH --nodelist=gpu1

start_time=$(date)

# Run the PyTorch script
echo "starting: $start_time"; echo ""

apptainer exec --nv /home/mani/dev/pytorch_23.05.sif python /home/mani/dev/pytorch_test.py

end_time=$(date)
echo ""
echo "ended: $end_time"
```

This `.sbatch` script simply tells Slurm to run this script on node `gpu1` which has 8x A100 GPUs, and save the output in a file called `/home/mani/dev/pytorch_test.out`. 

**Note:** You need to change the path for both the `.out` and `.err` file to _your_ desired/accessible path.

You can see at line `apptainer exec --nv /home/mani/dev/pytorch_23.05.sif python /home/mani/dev/pytorch_test.py` we have provided _our_ working path to both the container's `.sif` file as well as the python program. 

You need to make sure you change these to where _you_ have saved those files.

After everything is ready to go, submit your `run_pytorch.sbatch` to SLURM:
```bash
sbatch run_pytorch.sbatch
```

This job takes somewhere from 10-15 seconds to complete.

If you run `squeue -u $USER` before it's completed, you will be able to see your job listed in the queue.

Once you don't see your job in the queue any more, it means it has completed. It's now time to check the `.out` file! 

You should expect something like:
```text
[mani@binary dev]$ cat pytorch_test.out
starting: Tue Oct 15 17:15:42 EDT 2024

Random Tensor:
tensor([[0.6414, 0.6855, 0.5599],
        [0.5254, 0.2902, 0.0842],
        [0.0418, 0.1184, 0.9758],
        [0.7644, 0.6543, 0.0109],
        [0.9723, 0.4741, 0.8250]])

Tensor after adding 2:
tensor([[2.6414, 2.6855, 2.5599],
        [2.5254, 2.2902, 2.0842],
        [2.0418, 2.1184, 2.9758],
        [2.7644, 2.6543, 2.0109],
        [2.9723, 2.4741, 2.8250]])

Using CUDA

Current device: 0
Device count: 8
Device name: NVIDIA A100-SXM4-80GB

ended: Tue Oct 15 17:15:53 EDT 2024
```

**Note:** Even if your script's execution is successful, you will see the `.err` file; however, it will be empty.

If your output file is empty, try seeing if there is anything informational in the `.err` file to diagnose the issue.

### TensorFlow container job example

In this example, we assume you have a TensorFlow `.sif` file available, through either of the methods we have explained previously in this page.

Now let's proceed with how we can execute a TensorFlow script (`tfTest.py`), that trains a simple neural network on the MNIST dataset using GPUs.

First, create a Python script called `tfTest.py` with the provided content:

```python
import tensorflow as tf

physical_devices = tf.config.list_physical_devices(device_type=None)

print("Num of Devices:", len(physical_devices))

print("Devices:\n", physical_devices)

print("TensorFlow version information:\n",tf.__version__)

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

Next, create a SLURM batch job script named `job-test-nv-tf.sh`. This script requests GPU resources, loads necessary modules, and runs your TensorFlow script inside an Apptainer container:

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

**Note** that you need to replace the name and path of _your_ TensorFlow `.sif` image and `tfTest.py` file at line `apptainer run --nv tensorflowGPU.sif python3 tfTest.py` if not within the same path as the `sbatch` script's.

This script runs the `tfTest.py` script inside the TensorFlow GPU container (`tensorflowGPU.sif`)

You can now submit your job to Slurm using `sbatch job-test-nv-tf.sbatch`.

After the job completes, you can check the output in `result.txt`. The output should include information about the available physical devices (GPUs), the TensorFlow version, and the output from training the model on the MNIST dataset.

The beginning and end of the file might look something like this:

```text
run Apptainer TensorFlow GPU
Num of Devices: X
Devices:
 [PhysicalDevice(name='/physical_device:CPU:0', device_type='CPU'), PhysicalDevice(name='/physical_device:GPU:0', device_type='GPU'), ...]
TensorFlow version information:
 X.XX.X
begin test...
...
313/313 - 0s - loss: X.XXXX - accuracy: 0.XXXX
```
