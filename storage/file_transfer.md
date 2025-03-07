# Transfering Files to/from Star

Star supports file transfers primarily through SCP and SFTP, both of which operate over SSH.

## File Transfer Address

Currently, there are no dedicated nodes for file transfers at Star. All transfers should be conducted over the login node using the standard server address.

## Basic Tools (SCP, SFTP)

Standard SCP and SFTP clients can be used for secure file transfers. Here are the basic commands for using these tools:

```bash
# run this on your local computer to transfer a file to Star
scp -P 5010 /path/to/my/local/file <username>@<login-node>.star.hofstra.edu:/destination/path/to/file/on/star

# run this on your local computer to transfer a file from Star
scp -P 5010 <username>@<login-node>.star.hofstra.edu:/path/to/file/on/star /destination/path/to/file/on/local/computer

sftp <username>@<login-node>.star.hofstra.edu
```

## Mounting the File System on Your Local Machine Using SSHFS

Star HPC Cluster allows users to mount remote file systems on their local machines. For Linux, the command would look like this:

```bash
sshfs [user@]<login-node>.star.hofstra.edu:[dir] mountpoint [options]
```

For example:

```bash
sshfs yourusername@login1.star.hofstra.edu: /home/yourusername/star-fs/
```

Windows and Mac users can use [Cyberduck](https://cs.hofstra.edu/docs/pages/guides/cyberduck_setup.html) for similar functionality. WinSCP is another option for Windows, and FileZilla can be used across Windows, Mac, and Linux.

### High-Performance Tools

For large data transfers, the performance can vary greatly depending on the source's location and bandwidth. Hofstra does not have unlimited Internet bandwidth, so transfers from external sources might be slower. For high-performance transfers, users are encouraged to use utilities like rsync, which is supported and recommended for its efficiency.

## Rsync

Rsync is a particularly useful tool and is recommended for transferring files to and from the Star HPC Cluster. It provides an efficient way to sync files and directories across different locations while minimizing data transfer.

## Guidelines for Large File Transfers

When transferring very large files or datasets, it is advised to use rsync and to calculate and confirm checksums to ensure data integrity.

## Cyberduck

Cyberduck is a file transfer application with an intuitive graphical interface for transfering files to or from a remote machine. Cyberduck is available for both Windows and Mac. Download it from [cyberduck.io](https://cyberduck.io/).

Click "Open Connection" and a new window will be displayed like below. Select "SFTP (SSH File Transfer Protocol)" from the top dropdown menu. Enter the server, port number, your username, and Linux Lab password. Then click "Connect".

   ![3-connection.png]({{ site.baseurl }}/images/cyberduck_setup_images/3-connection.png "3-connection.png")

If you see a window asking about an "Unknown fingerprint", click "Always" and then "Allow".

   ![4-fingerprint.png]({{ site.baseurl }}/images/cyberduck_setup_images/4-fingerprint.png "4-fingerprint.png")

You should now be able to see your user's home directory on the cluster. You can transfer files to and from it by dragging and dropping files between this window and your "Finder" windows.

## Network Interfaces and Bandwidth

All file transfer access to the Star HPC Cluster is currently through the login node's 1GbE interface. Users should be aware of potential bandwidth limitations, especially when transferring large amounts of data.

## User Authentication and Permissions

File transfers are authenticated in the same way as SSH access. SSH keys are the preferred method for secure authentication, although password authentication is currently allowed. Plans for implementing Multi-Factor Authentication (MFA) are being considered for future security enhancements.

