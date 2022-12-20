# Linux Namespaces
A Namespace (ns) wraps some global system resource to provide resource isolation.

## Linux support multiple NS types
* Mount | CLONE_NEWNS | 2002
* UTS | CLONE_NEWUTS | 2006
* IPC | CLONE_NEWIPC | 2006
* PID | CLONE_NEWPID | 2008
* Network | CLONE_NEWNET | 2009
* User | CLONE_NEWUSER | 2013
* Cgroup | CLONE_NEWCGROUP | 2016
* Time (proposed)

## Namespaces are Linux-specific feature
* For each NS type
    * Multiple instances of NS may exist on a system
        * At system boot, there is one instance of each NS type (initial namespace)
    * Each process resides in one NS instance.
    * To process inside NS instance, it appears that only they can see/modify corresponding global resource.
        * Processes are unaware of other instances of resource.

* When new processes is created via fork(), it resides in same set of NSs as parent.

> One of the most interesting types of namespaces is `user namespaces` and that's what I'm gonna sort of drill down to it in the second piece because user namespaces sort of bring all the other namespaces together in a way that let us do some quite powerful things things like nprivileged containers for example.

In a container-style frameworks, most or All  NS types are used in concert.
### How its possible to be:
* super user inside
* Unprivileged outside

### Unprivileged containers


## UTS Namespace
* Simplest namespace, Isolate two system identifiers returned by uname(2) `man 2 uname`
    * `nodename` - system hostname (set by sethostname)
    * `domainname` - NIS domain name / Yellow pages domain
* Container configuration scripts might tailor their actions based on these IDs.
    * `nodename` could be used with DHCP to obtain IP address for container.
* Running system may have multiple UTS NS instances.
* Process within single instance(UTS NS instance) access (get/set) same nodename and domainname.
* Each NS instance has its own nodename and domainname
    * Changes to nodename and domainname in one NS instance are invisible  to others instances.

### NIS domain name
An NIS domain is a collection of systems that are logically grouped together. A group of hosts that share the same set of NIS maps belong to the same domain. The hosts are usually grouped together in the domain for a common reason; for example, when working in the same group at a particular location. NIS focuses on making network administration more manageable by providing centralized control over a variety of network information.

### uname(2) `man 2 uname`
* uname - get name and information about current kernel
* Part of the utsname information is also accessible via `/proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}`.


## Namespaces API & Commands

## ls -la /proc/PID/ns
Each process has some symlink files in `/proc/PID/ns`
![proc/pid/ns](./screens/proc_pid_ns.jpg)
One symlink for each of the NS types

> tells us which namespace does this process belongs to

### Target of symblink tells us which NS instance process is in.
> `readlink /proc/290/ns/uts`
![readlink](./screens/readlink_proc_pid_ns_uts.jpg)

> Format of the above output: `ns_type : [magic_inode_number]` = uts:[4026532283]
 * magic_inode_number =  unique inode number correponding to this namespace.

### Various uses for the  /proc/PID/ns symlinks
 * If processes show same  symlink target, they are in the same NS.

### APIs and commands
Programs can use various system calls to work with NS/namespaces.
* clone: create new child process in the new NS(s).
* unshare: create  new NS(s) and move caller into it.
* setns: move calling process another existing NS(s) instance.

### The unshare and nsenter have flags for specifying each NS type.

> `unshare [options] [command/program [args]]`

* -C = Create new Cgroup NS
* -i = Create new IPC NS 
* -m = Create new Mount NS
* -n = Create new Network NS
* -p = Create new PID NS
* -u = Create new UTS NS
* -U = Create new user NS

#### nsenter

> `nsenter [options] [command/program [args]]`

* -t PID = PID of process which NSs should be entered
* -C = Enter cgroup NS of target process
* -i = Enter IPC NS of target process
* -m = Enter Mount NS of target process
* -n = Enter network NS of target process
* -p = Enter PID NS of target process
* -u = Enter UTS NS of target process
* -U = Enter user NS of target process
* -a = Enter all NSs of target process


### Previlege requirements for creating namespaces
* Creating user NS instances require no privileges.
* Creating instances of other (nonuser) NS types requires privilege.
    * CAP_SYS_ADMIN


* echo $$ - shows the PID of the shell
* readlink /proc/$$/ns/uts
![bash_ns_uts](./screens/bash_pid_uts_ns.jpg)

> `readlink /proc/$$/ns/mnt`\
> `readlink /proc/$$/ns/uts`\
> `readlink /proc/$$/ns/pid`

![2_bash_compare](./screens/bash_2_client.jpg)

> unshare -u bash

> sudo unshare -u bash

> hostname wania

> readlink /proc/$$/ns/uts

> readlink /proc/$$/ns/mnt

> readlink /proc/$$/ns/net

> nsenter -t 456 -u bash
![unshare_nsenter](./screens/unshare_nsenter_command.jpg)

### Persistent namespace
* https://breachlabs.io/unshare-linux-persistence-technique

> `unshare -pf --mount-proc /bin/bash`

Running unshare -m gives the calling process a private copy of its mount namespace, and also unshares file system attributes so that it no longer shares its root directory, current directory, or umask attributes with any other process.

### persistent UTS namespace
creates a new persistent UTS namespace and modifies the hostname
```
# touch /root/uts-ns
# unshare --uts=/root/uts-ns hostname wania
# nsenter --uts=/root/uts-ns hostname
wania
# umount /root/uts-ns
```

### persistent mount namespace
```
# mkdir /root/namespaces
# mount --bind /root/namespaces /root/namespaces
# mount --make-private /root/namespaces
# touch /root/namespaces/mnt
# unshare --mount=/root/namespaces/mnt
```

### Private mount points with unshare

> unshare --map-root-user bash

> unshare --uts bash

> /sbin/init

> echo `$!` PID of last job running in background.

> unshare --user &\
> mount --bind /proc/$!/ns/user /root/user-ns\
> kill -9 $!

### PID | Process Namespace
Every time a computer with Linux boots up, it starts with just one process, with process identifier (PID) 1. This process is the root of the process tree, and it initiates the rest of the system by performing the appropriate maintenance work and starting the correct daemons/services. All the other processes start below this process in the tree. The PID namespace allows one to spin off a new tree, with its own PID 1 process. The process that does this remains in the parent namespace, in the original tree, but makes the child the root of its own process tree.

With PID namespace isolation, processes in the child namespace have no way of knowing of the parent process’s existence. However, processes in the parent namespace have a complete view of processes in the child namespace, as if they were any other process in the parent namespace.


> findmnt -o+PROPAGATION

## FSTYPE: File System Type:
* rootfs
* overlay
* tmpfs
* cgroup
* proc
* ext4 - Extended File System
* 9p

## Host machine All NS
> readlink /proc/1/ns/uts

```bash
root@mhost:~# readlink /proc/1/ns/*
cgroup:[4026531835]
ipc:[4026532271]
mnt:[4026532282]
net:[4026531840]
pid:[4026532284]
pid:[4026532284]
user:[4026531837]
uts:[4026532283]
```

> Building containers by hand: The PID namespace
* https://www.redhat.com/sysadmin/pid-namespace

## The mount namespace | man 7 mount_namespaces
* https://manpages.ubuntu.com/manpages/impish/man7/mount_namespaces.7.html
* https://lwn.net/Articles/689856/
* https://www.redhat.com/sysadmin/mount-namespaces

By default, if you were to create a new mount namespace with unshare -m, your view of the system would remain largely unchanged and unconfined. That's because whenever you create a new mount namespace, a copy of the mount points from the parent namespace is created in the new mount namespace. That means that any action taken on files inside a poorly configured mount namespace will impact the host.

### Some setup steps for mount namespaces

Abusing access to mount namespaces through `/proc/pid/root`

One special feature of the mount namespace is that they can be accessed through the /proc/PID/root/ and /proc/PID/cwd/ folders. These folders allow processes in a parent mount namespace and PID namespace to temporarily view files in the mount namespace of another process. This access is a bit magical and has some restrictions – for example, setuid executables will not work and device files are still usable even when /proc is mounted with the ‘nodev’ option.

> By default, a Docker container has the following capabilities:
```
cap_chown,cap_dac_override,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,
cap_net_bind_service,cap_net_raw,cap_sys_chroot,cap_mknod,cap_audit_write,cap_setfcap+eip
```
Most of these capabilities are hard to abuse, for example, the cap_kill allows root in the container to kill all processes it can see, which is limited by the PID namespace, effectively only allowing processes within the container to be killed.

However, as the container has the cap_mknod, a root user within the container is allowed to create block device files. Device files are special files that are used to access underlying hardware & kernel modules. For example, the /dev/sda block device file gives access to read the raw data on the systems disk.

Docker ensures that block devices cannot be abused from within the container by setting a cgroup policy on the container that blocks read and write of block devices.

> However, if a block device is created within the container it can be accessed through the `/proc/PID/root/` folder by someone outside the container, the limitation being that the process must be owned by the same user outside and inside the container.

To show that the user develop now has full access to the filesystem we grab the root password hash from the /etc/shadow file.

This attack is easily prevented by following best practices by ensuring that nobody is root within the container and by running Docker with the parameter '–cap-drop=MKNOD'.


> ls -la /proc/485/root

I use an Alpine Linux tarball.

> wget https://dl-cdn.alpinelinux.org/alpine/v3.13/releases/x86_64/alpine-minirootfs-3.13.1-x86_64.tar.gz

> capsh --help

> cat /proc/$$/task/$$/status | grep Cap

> cat /proc/self/status

### Capability sets manipulated by
* Starting service - systemd
* Starting container - dockerd...
* Loading programs - execve() system call

### What are Capabilities?
Linux capabilities have been around in the kernel for some time. The idea is to break up the monolithic root privilege that Linux systems have had, so that smaller more specific privileges can be provided where they’re required. This helps reduce the risk that by compromising a single process on a host an attacker is able to fully compromise it.

### Practical use of capabilities
So how do we actually manipulate capabilities on a Linux system? The most basic way of handing this (without writing custom code) is to use the getcap and setcap binaries which come with the libcap2-bin package on debian derived systems.

If you use getcap on a file which has capabilities, you’ll see something like this

>  `getcap /usr/bin/ping`
```
/usr/bin/ping cap_net_raw=ep
```
> capsh --decode=0000003fffffffff
```
0x0000003fffffffff=cap_chown,cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_linux_immutable,cap_net_bind_service,cap_net_broadcast,cap_net_admin,cap_net_raw,cap_ipc_lock,cap_ipc_owner,cap_sys_module,cap_sys_rawio,cap_sys_chroot,cap_sys_ptrace,cap_sys_pacct,cap_sys_admin,cap_sys_boot,cap_sys_nice,cap_sys_resource,cap_sys_time,cap_sys_tty_config,cap_mknod,cap_lease,cap_audit_write,cap_audit_control,cap_setfcap,cap_mac_override,cap_mac_admin,cap_syslog,cap_wake_alarm,cap_block_suspend,cap_audit_read
```

> `capsh --decode=00000000a80425fb` | see what Docker provides by default
```
0x00000000a80425fb=cap_chown,cap_dac_override,cap_fowner,cap_fsetid,cap_kill,cap_setgid,cap_setuid,cap_setpcap,cap_net_bind_service,cap_net_raw,cap_sys_chroot,cap_mknod,cap_audit_write,cap_setfcap
```

### Capability Gotcha's
There are some gotcha's to be aware of when using capabilities. First up is that, to use file capabilities, the filesystem you’re running from needs have extended attribute (xattr) support. A notable exception here is some versions of aufs that ship with some versions Debian and Ubuntu. This can impact Docker installs, as they’ll use aufs by default.

Another one is that where you’re manipulating files you need to make sure that the tools you’re using understand capabilities. For example when backing up files with tar, you need to use the following switches to make it all work.

* [Practical use of Linux capabilities](https://www.youtube.com/watch?v=WYC6DHzWzFQ)
> https://raesene.github.io/blog/2017/08/27/Linux-capabilities-and-when-to-drop-all/

## Learning Resource
* https://lwn.net/Articles/531114/
* https://www.youtube.com/watch?v=YmbCfeVPHEI
* [Unsharing the user namespace for rootless containers](https://www.youtube.com/watch?v=YmbCfeVPHEI)
* https://superuser.com/questions/1729906/how-to-quit-from-linux-namespace
* [Linux File System Explained](https://www.youtube.com/watch?v=ePN5igV9ZpY)
* https://unix.stackexchange.com/questions/710809/why-is-the-linux-command-unshare-pid-p-mount-m-not-creating-a-persistent-n
* [Let's code a Linux Driver - 0: Introduction](https://www.youtube.com/watch?v=x1Y203vH-Dc&list=PLCGpd0Do5-I3b5TtyqeF1UdyD4C-S-dMa)
* https://www.toptal.com/linux/separation-anxiety-isolating-your-system-with-linux-namespaces
* [Write your own Operating System](https://www.youtube.com/watch?v=soQCkeBDOAY&list=PLHh55M_Kq4OAIL0fNVfvBt2VuX2cJeXjk)
* [Let's code a Linux Driver - 0: Introduction](https://www.youtube.com/watch?v=x1Y203vH-Dc&list=PLCGpd0Do5-I3b5TtyqeF1UdyD4C-S-dMa)