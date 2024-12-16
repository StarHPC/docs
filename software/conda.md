---
sort: 5
---

# Miniforge

## Why Miniforge and not Ana/Miniconda?

Miniforge is our recommended Python distribution and package manager. While Anaconda and Miniconda are popular choices,
they have licensing restrictions that make them unsuitable for academic and research environments.
Miniforge provides the same functionality through conda-forge, a community-driven repository of packages
that are free and open source.

Here are some of the benefits of Miniforge:
- Free and open-source distribution
- Defaults to conda-forge channels
- Smaller initial download (~100MB) compared to Anaconda (~3GB)
- Community-driven package repository
- Compatible with existing conda environments and commands

## Loading Miniforge

On Star cluster, Miniforge is available through the module system. To load it:
```bash
module load miniforge3
```

This gives you access to the `conda` command and Python. You can try:
```bash
conda --version
python --version
```

## Creating a Conda Environment

When using Miniforge, you must create and use conda environments for installing additional packages.
This keeps your projects isolated and prevents conflicts between different package versions.

To create a new environment:

```bash
conda create -n myenv python=3.10
```

Replace `myenv` with your preferred environment name and adjust the Python version as needed.

For example, to create an environment for data science with common packages:

```bash
conda create -n datasci numpy pandas scikit-learn matplotlib jupyter
```

## Managing Environments

### Activating an Environment

After creating an environment, activate it with:

```bash
conda activate myenv
```

Your prompt will change to show the active environment:
```bash
(myenv) [username@node ~]$
```

### Deactivating an Environment

To return to the base environment:
```bash
conda deactivate
```

### Installing Packages

With your environment activated, install packages using:
```bash
conda install package_name
```

For example:
```bash
conda install numpy pandas matplotlib
```

### Listing Environments

View all your environments:
```bash
conda env list
```

### Removing an Environment

To delete an environment:
```bash
conda env remove -n myenv
```

## Best Practices

### 1. Environment Location

By default, environments are created in your home directory under `~/.conda/envs/`.
For larger environments or shared projects, consider creating them in your project directory on the **parallel file system storage**:
```bash
conda create --prefix /fs1/projects/myproject/env
```

### 2. Environment Files

Export your environment configuration for reproducibility:

```bash
conda env export > environment.yml
```

Create an environment from a configuration file:

```bash
conda env create -f environment.yml
```

### 3. Channel Priority

A channel is a location (a URL) where `conda` will look for packages.

When you run conda config --add channels bioconda, you're actually adding the URL https://conda.anaconda.org/bioconda
to your channel list. The command just uses a shorthand name "bioconda" instead of making you type the full URL.

Miniforge defaults to conda-forge, but you can add additional channels:
```bash
conda config --add channels bioconda
```

List configured channels:

```bash
conda config --show channels
```

### 4. Clean Up

Regularly clean unused packages and caches using:
```bash
conda clean --all
```

## Common Issues and Solutions

### Package Conflicts

If you encounter conflicts when installing packages:

1. Try installing packages together in one command
2. Use `conda install package=version` for specific versions
3. Consider creating a new environment for conflicting requirements

### Environment Activation Fails

If `conda activate` fails:
1. Ensure Miniforge is properly loaded (`module load miniforge3`)
2. Check if the environment exists (`conda env list`)
3. Verify environment path permissions
