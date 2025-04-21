# Storage and Backup

## Available File Systems

Star HPC Cluster offers a number of file systems for different storage needs:

- **Home Directories**: Located on the shared storage system, accessible from all nodes. These directories are not designed for high-performance needs.
- **Work/Scratch (Local Storage)**: Directly attached storage specific to each compute node with high-performance local storage.
- **Data (HPE PFSS)**: A high-capacity, high-performance shared storage system.

## Home Directories

The home directories on Star serves as personal storage spaces for users. They are globally accessible from both the login nodes and all compute nodes. The size of these directories will be limited to 50GB per user by default. Users are advised to use these directories for storing essential files and scripts, and not for large datasets or computationally intensive tasks.

## Work/Scratch Areas

The Star HPC Cluster provides two types of work/scratch areas:

- **Work/Scratch (Local Storage)**: This high-performance local storage is directly attached to each compute node. It offers several terabytes of capacity without any imposed quota. It is ideal for temporary data storage during computations.
- **Data (HPE PFSS)**: This shared storage solution has a high capacity of 64 terabytes and offers high performance. The quota for this system is a few terabytes per user which is suitable for larger datasets and critical research data.

Users are encouraged to manage their data efficiently, using the home directories for persistent but small-scale storage needs and the work/scratch spaces for temporary data.

## Backup and Recovery

### Backup Policies

The Star HPC Cluster's backup policies vary across different storage systems:

- **Home Directories**: No backup services provided.
- **Work/Scratch**: No backup services provided.
- **Data (HPE PFSS)**: Backup may be available on a per-project basis as determined by specific project requirements.

Users can request backup services as needed, particularly for critical data stored in the Data (HPE PFSS) system.

## Compression of data

Data which is not accessed frequently like results of finished projects
should be compressed in order to reduce storage space.

We recommend `xz` and `tar` to compress single files or whole folder
structures. To compress a single file:

    $ xz file

To decompress:

    $ xz --decompress file

To create a archive multiple files or folder:

    $ tar cfJv archive.tar.xz files

It is recommended to use the file suffix `.tar.xz` to make it clear that
archive was compressed with `xz`.

To extract a archive (use `-C folder` to extract the files in folder):

    $ tar xvf archive.tar.xz

## Data Archiving

Data archiving services are available on the Star HPC Cluster to comply with NSF requirements for published research data. Specific policies and procedures for data archiving are currently under development and will align with NSF regulations and user demands.

## Closing of User Account

Upon account closure, users are notified to transfer any essential data they wish to retain. Data stored in the user's spaces will be eventually deleted following account termination.

## Privacy and Security of User Data

The Star HPC Cluster maintains strict policies regarding the privacy and security of user data. Users are responsible for ensuring the confidentiality of their data and are advised not to share their account credentials. The default permissions for new accounts allow user data to be readable by others on the system. Users can easily change these permissions using the `chmod` command to suit their privacy needs. The most commonly used is:

-   only user can read their home directory:

        chmod 700 /home/$USER

-   User and their group can read and execute files on the home
    directory:

        chmod 750 /home/$USER

-   User and all others including the group can read and execute the
    files:

        chmod 755 /home/$USER

-   everybody can read, execute, and WRITE to directory:

        chmod 777 /home/$USER

## Support

For assistance with storage and backup issues or any other inquiries, users can contact the Star HPC Cluster support team at <support@starhpc.hofstra.io>.
