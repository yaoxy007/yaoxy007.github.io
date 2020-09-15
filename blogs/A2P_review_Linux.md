# 1. Linux Architecture

-----------------

## 1. Linux System Components

<img src="/Users/yaoxuey/Library/Application Support/typora-user-images/image-20200722094434477.png" alt="image-20200722094434477"  />

* Bootloader: manages boot process of computer
* Kernal: responsible for all major activities of operating system
* System Library: functions or programs using which app program or system utilities accesses Kernel's features
* System Utility: responsible to do specialised, individual level tasks
* Advantages:
    * portable
    * open source
    * multi-user
    * Multi-programming
    * Hierarchical file system
    * shell: interpreter program which can be used to execute commands of the operating system.
    * security

## 2. Linux Distribution Differences

Linux distribution is a variation on Linux operating system that packages together different software that may meet different needs. often, the primary focus of each distribution is the desktop environment

* Desktop environments: GNOME, KDE, XFCE, etc

## 3. Process Cycle

when a program is loaded into memory, it becomes a process, it can be divided into 4 sections:

* Stack: temporary data, such as function parameters, return address and local variables
* Heap: dynamically allocated memory to a process during its run time
* Text: current activity represesnted by the value of program counter and the contents of the processor's registers
* Data: global and static variables

![image-20200722100235224](/Users/yaoxuey/Library/Application Support/typora-user-images/image-20200722100235224.png)

Process Control Block: data structure maintained by OS for every process, is defined by process id, it contains:

1. Process State: ready, running, waiting...
2. Process Privileges: allow/disallow access to system resources
3. Process ID: PID
4. Pointer: pointer to parent process
5. Program Counter: pointer to the address of next instruction to be executed for this process
6. CPU Registers: where process need to be stored for execution for running state
7. CPU Scheduling Information: process priority and other scheduling information
8. Memory Management information: page table, memory limits, segment table...
9. Accounting Information: amout of CPU used for process execution, time limits, execution ID...
10. IO Status Information: list of IO devices allocated to the process

# 2. Understanding CPU

---------------------

## 1. [Linux Load Averages and Monitor Performance of Linux](https://www.tecmint.com/understand-linux-load-averages-and-monitor-performance/)

- System load/CPU load: measurement of CPU over or under-utilisation in a Linux system; the number of processes which are being executed by the CPU or in waiting state
- Load average: average system load calculated over a given period of time of 1, 5, and 15 minutes. it's a running average of processes in it's kernel exection queue tagged as running or uninterruptible

How long the system has been running:

```shell
$ uptime

07:13:53 up 8 days, 19 min,  1 user,  load average: 1.98, 2.15, 2.21
```

Displays running Linux process in real time:

```shell
$ top

top - 12:51:42 up  2:11,  1 user,  load average: 1.22, 1.12, 1.26
Tasks: 243 total,   1 running, 242 sleeping,   0 stopped,   0 zombie
%Cpu(s): 17.4 us,  2.9 sy,  0.3 ni, 74.8 id,  4.6 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  8069036 total,   388060 free,  4381184 used,  3299792 buff/cache
KiB Swap:  3906556 total,  3901876 free,     4680 used.  2807464 avail Mem 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                                                                        
 6265 tecmint   20   0 1244348 170680  83616 S  13.3  2.1   6:47.72 Headset                                                                                                                        
 2957 tecmint   20   0 2644644 1.035g 137968 S   6.7 13.5  50:11.13 firefox                                                                                                                                        
 3208 tecmint   20   0  507060  52136  33152 S   6.7  0.6   0:04.34 gnome-terminal-                                                                                                                                
 3272 tecmint   20   0 1521380 391324 178348 S   6.7  4.8   6:21.01 chrome                                                                                                                                         
 6220 tecmint   20   0 1595392 106964  76836 S   6.7  1.3   3:31.94 Headset                                                                                                                                        
    1 root      20   0  120056   6204   3964 S   0.0  0.1   0:01.83 systemd                                                                                                                                        
    2 root      20   0       0      0      0 S   0.0  0.0   0:00.00 kthreadd                                                                                                                                       
    3 root      20   0       0      0      0 S   0.0  0.0   0:00.10 ksoftirqd/0
```

> **$Cpu(s):** 
> us->user, 
> id->idle, 
> sy->system(running kernel), 
> ni->nice(user space processes), 
> wa->wait(idle while waiting for IO to complete), 
> si/hi->how much time the processor has spent servicing interrupts, 
> hi->hardware interrupts, check /proc/interrupts
> si->software interrupts, 
> st->how long virtual CPU has spent waiting for hypervisor to service another virtual CPU

> **KiB Mem:**
>
> - total: total size of RAM mapped by running kernel
> - Cached: size of RAM used to cache the content of files being read recently
> - Buffer: in-mem copy of blocks as a result of kernel performing raw disk access
>     *buffer increase when bypass file system layer, cache increase when we do opposite

> **VIRT**: length of memory area
> **RES**: how many memory blocks (pages) are really allocated and mapped to process address space, it shows approximation of per-process memory consumption
> **SHR**: size of file-backed memory area
> *RES-SHR = annonymous memory area, actual memory consumption is between RES and annonymous mem
> **NI**: nice level
> **PR**: dynamic priority, PR=NI+20

Linux System Monitoring:

```shell
$ glances

TecMint (LinuxMint 18 64bit / Linux 4.4.0-21-generic)                                                                                                                                               Uptime: 2:16:06

CPU      16.4%  nice:     0.1%                                        LOAD    4-core                                        MEM     60.5%  active:    4.90G                                        SWAP      0.1%
user:    10.2%  irq:      0.0%                                        1 min:    1.20                                        total:  7.70G  inactive:  2.07G                                        total:   3.73G
system:   3.4%  iowait:   2.7%                                        5 min:    1.16                                        used:   4.66G  buffers:    242M                                        used:    4.57M
idle:    83.6%  steal:    0.0%                                        15 min:   1.24                                        free:   3.04G  cached:    2.58G                                        free:    3.72G

NETWORK     Rx/s   Tx/s   TASKS 253 (883 thr), 1 run, 252 slp, 0 oth sorted automatically by cpu_percent, flat view
enp1s0     525Kb   31Kb
lo           2Kb    2Kb     CPU%  MEM%  VIRT   RES   PID USER        NI S    TIME+ IOR/s IOW/s Command 
wlp2s0        0b     0b     14.6  13.3 2.53G 1.03G  2957 tecmint      0 S 51:49.10     0   40K /usr/lib/firefox/firefox 
                             7.4   2.2 1.16G  176M  6265 tecmint      0 S  7:08.18     0     0 /usr/lib/Headset/Headset --type=renderer --no-sandbox --primordial-pipe-token=879B36514C6BEDB183D3E4142774D1DF --lan
DISK I/O     R/s    W/s      4.9   3.9 1.63G  310M  2459 tecmint      0 R  7:12.18     0     0 cinnamon --replace
ram0           0      0      4.2   0.2  625M 13.0M  2301 tecmint    -11 S  2:29.72     0     0 /usr/bin/pulseaudio --start --log-target=syslog
ram1           0      0      4.2   1.3 1.52G  105M  6220 tecmint      0 S  3:42.64     0     0 /usr/lib/Headset/Headset 
ram10          0      0      2.9   0.8  409M 66.7M  6240 tecmint      0 S  2:40.44     0     0 /usr/lib/Headset/Headset --type=gpu-process --no-sandbox --supports-dual-gpus=false --gpu-driver-bug-workarounds=7,2
ram11          0      0      2.9   1.8  531M  142M  1690 root         0 S  6:03.79     0     0 /usr/lib/xorg/Xorg :0 -audit 0 -auth /var/lib/mdm/:0.Xauth -nolisten tcp vt8
ram12          0      0      2.6   0.3 79.3M 23.8M  9651 tecmint      0 R  0:00.71     0     0 /usr/bin/python3 /usr/bin/glances
ram13          0      0      1.6   4.8 1.45G  382M  3272 tecmint      0 S  6:25.30     0    4K /opt/google/chrome/chrome 
...
```

Load Average is read from /proc/loadavg file, can be reviewed by:

```shell
$ cat /proc/loadavg

2.48 1.69 1.42 5/889 10570
```

Show number of processing units, or CPU info:

```shell
$ nproc
4

OR
$ lscpu

OR
$ grep 'model name' /proc/cpuinfo | wc -l
```

## 2. Context Switching

storing the context or state of a process so that it can be reloaded when required and exection can be resumed from the same point as earlier

Context Switching Triggers:

- multitasking: state of old process is saved and state of new process is loaded
- interrupt handling: hardware switches a part of the context when an interrupt occurs. only some of the context is changed to minimize the time required to handle the interrupt
- user and kernel mode switching: transition between the user mode and kernel mode is required in the operating system

Context Switching Steps:

1. save the context of the process that's currently running on CPU, update PCB
2. move the PCB of above process into relevant queue
3. Select a new process for exection
4. update PCB of selected process
5. update memory management data structure
6. restore the context of the process that was previously running when it is loaded again

# 3. Linux Memory Management

-----------------

## 1. Memory Management

- Symbolic Address: address used in source code. the vaiable names, constants and instruction labels are basic elements of symbolic address space
- Relative Address: at compilation, compiler converts symbolic address into relative address
- Physical Address: generated when a program is loaded into main memory

> virtual and physical addresses are the same in compile time, and load time address binding schemes. they are different in execution time address binding scheme

---------------

- Static Loading: the absolute program and data is loaded into memory in order for execution to start
- Dynamic Loading: dynamic routines of the library are stored on a disk in relocatable form and are loaded into memory only when they are needed by the program

------------------

- Static Link: linker combines all other modules needed by a program into a single executable program to avoid runtime dependency
- Dynamic Link: do not require linking the actual module or library with the program

--------------

## 2. Paging

- memory management technique in which process address space is broken into blocks of the same size. size of process is mesured in number of pages
- logical address (page address) = page number + page offset
- physical address = frame number + page offset
- **page map table** keeps track of the relation between a page of a process to a frame in physical memory

- advatanges and disadvantages of paging
    - reduce external fragmentation, but still suffer from internal fragmentation
    - simple to implement and assumed as an efficient memory management technique
    - swapping is easy due to equal size of pages and frames
    - page table requires extra memroy space

----------

**Page Cache**

- storage area in which special pages are temporarily stored. If these pages lie in main memory, the system can avoid frequent access to the slow disks
- Today it includes:
    - Buffer cache: manages blocks belonging to disks and other block devices in main memory
    - Swap cache: keep tracks if swapped in pages were modified, to avoid additional write to disk

- Change Linux Kernel Swappiness:

    ```shell
    $ echo 0 > /proc/sys/vm/swappiness
    ```

    ranges from 0-100, changes kernel's aggressiveness to swap annonymous pages. system reboot will reset this setting

## 3. Memory Segments

<img src="/Users/yaoxuey/Library/Application Support/typora-user-images/image-20200723123012454.png" alt="image-20200723123012454" style="zoom: 50%;" />

- user mode program cannot touch Kernel Space, otherwise page fault will be triggered
- **Stack**: stores local variables and function parameters. stack frame is destroyed when stack frame returns values. Each thread in a process has its own stacks
    - when stack max size reached: stack overflow + segmentation fault
    - mapped stack only expands, it does not shrink
- **Memory Mapping Segment**: 

## 4. Memory usage commands

The /proc/meminfo file stores statistics about memory usage  on the Linux based system. The same file is used by free and other  utilities to report the amount of free and used memory (both physical  and swap) on the system as well as the shared memory and buffers used by the kernel.

```shell
$ cat /proc/meminfo
$ less /proc/meminfo
$ more /proc/meminfo
```



to display free memory size in MB:

```shell
$ free -m

             total       used       free     shared    buffers     cached
Mem:           750        625        125          0         35        335
-/+ buffers/cache:        254        496
Swap:          956          0        956
```

* Total: total RAM installed
* Used: total amount of RAM used, equals total - free - buffer - cache
* Free: the amount of unused or free memory
* Shared: amount of memory mostly used by the temps file system. shmem in /proc/meminfo
* Buffer/Cache: 
    * Buffer: memory used by Linux Kernel for buffers
    * Cache: memory used by the page cache and slabs
* Available: estimation of how much memory is available for starting new applications on system, without swapping

```shell
$ vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0      0 6701880   2088 1095716    0    0     0     4   12   16  0  0 100  0  0
```



# 4. Linux Storage and File System

-----

**everything in Linux is a file, it it's not a file, it's a process.**

## 1. Overview

### 1. sorts of files

* regular files: -
* Directory: d
* Special file: the mechanism used for input and output, mostly in /dev, c
* links: system to make a file or directory visible in multiple parts of system's file tree, l
* socket: special file type, provide inter-process networking, s
* named pipes: for processes to communicate with each other, p
* Block device: b

### 2. partitioning

disk partitions are a way of breaking up storage drive into smaller usable units, a partition is a section of a storage drive that can be treated in much the same way as a drive itself

* Data partition: normal Linux system data, containing all data to start up and run the system

    * /usr: partition for user programs
    * /home: containing user's personal data
    * /var: temporary data
    * /opt: third party and extra software

* swap partition: expansion of computer's physical memory, extra memory on hard disk

* Mount points: all mounted partitions can be found in **/etc/fstab**, 

    Df command only displays information about active non-swap partitions:

    ```shell
    $ df
    Filesystem     1K-blocks    Used Available Use% Mounted on
    devtmpfs         3957264       0   3957264   0% /dev
    tmpfs            3975516       0   3975516   0% /dev/shm
    tmpfs            3975516     384   3975132   1% /run
    tmpfs            3975516       0   3975516   0% /sys/fs/cgroup
    /dev/nvme0n1p1   8376300 1940100   6436200  24% /
    tmpfs             795104       0    795104   0% /run/user/1000
    ```

| /bin        | Common programs, shared by the system, the system administrator and the users. |
| ----------- | ------------------------------------------------------------ |
| /boot       | The startup files and the kernel, `vmlinuz`.  In some recent distributions also `grub` data.  Grub is the GRand Unified Boot loader and is an attempt to get rid of the many different boot-loaders we know today. |
| /dev        | Contains references to all the CPU peripheral hardware, which are represented as files with special properties. |
| /etc        | Most important system configuration files are in `/etc`, this directory contains data similar to those in the Control Panel in Windows |
| /home       | Home directories of the common users.                        |
| /initrd     | (on some distributions) Information for booting.  Do not remove! |
| /lib        | Library files, includes files for all kinds of programs needed by the system and the users. |
| /lost+found | Every partition has a `lost+found` in its upper directory.  Files that were saved during failures are here. |
| /misc       | For miscellaneous purposes.                                  |
| /mnt        | Standard mount point for external file systems, e.g. a CD-ROM or a digital camera. |
| /net        | Standard mount point for entire remote file systems          |
| /opt        | Typically contains extra and third party software.           |
| /proc       | A virtual file system containing information about system resources.  More information about the meaning of the files in `proc` is obtained by entering the command **man `\*proc\*`** in a terminal window.  The file `proc.txt` discusses the virtual file system in detail. |
| /root       | The administrative user's home directory.  Mind the difference between /,  the root directory and /root, the home directory of the *root* user. |
| /sbin       | Programs for use by the system and the system administrator. |
| /tmp        | Temporary space for use by the system, cleaned upon reboot, so don't use this for saving any work! |
| /usr        | Programs, libraries, documentation etc. for all user-related programs. |
| /var        | Storage for all variable files and temporary files created by users, such as  log files, the mail queue, the print spooler area, space for temporary  storage of files downloaded from the Internet, or to keep an image of a  CD before burning it. |

* partitioning format:
    * MBR: Master Boot Record, traditional partitioning system, cannot be used for disk over 2TB, only can have maximum of 4 primary partitions
    * GPT: GUID Partition Table, more modern approach,

### 3. block storage

AKA block device, a piece of hardware that can be used to store data, such as HDD, SSD, memory stick, etc.

### 4. Formatting and File Systems

formatting is the process of writing a file system to the disk and preparing it for file operations

a file system is the system that structures data and controls how information is written to and retrieved from the underlying disk

* Ext4: most popular default filesystem, 4th version of extended filesystem. it's journaled, backwards compatible with legacy systems
* Xfs: specialise in performance and large data files, use metadata journaling, potentially lead to data corruption in event of power loss
* Btrfs: copy-on-write filesystem, features can be integrated within filesystem layer, such as snapshots, cloning, volumes, etc. 
* Zfs: copy-on-write and volume manager with a robust and mature feature set, can handle large filesystem size.

### 5. storage device

all in `/dev` direcotry, start with `sd` or `hd` followed by a letter, such as `/dev/sda`

`/dev/disk` contains subdirecotries corresponding with different, more persistent ways to identify disks and partitions on the system:

* `by-label`: user specified names for a disk or partition
* `by-uuid`: guaranteed to be unique, even across systems, therefore good for system migration
* `by-partlabel`/`by-partuuid`: GPT tables offer their own set of labels and UUIDs
* `by-id`: hardware's own serial numbers
* `by-path`: system's interpretation of the hardware used to access the device

## 2. Orientation in file system

### 1. Set PATH environment variable in a session:

```shell
$ export PATH=/usr/local/bin:/usr/local/sbin:/usr/X11R6/bin:\
/usr/bin:/usr/sbin:/bin:/sbin:/home/jumper/bin
```

it only valid for this session. 

### 2. Most important files and directories

* Kernel: communicate between the underlying hardware and peripherals

* Shell: 

    * sh: bourne shell, original shell still used on UNIX
    * Bash: bourne Again SHell, standard GNU shell, compatible with sh
    * Csh: C Shell
    * tcsh: Turbo C Shell
    * ksh: Korn Shell

    ```shell
    $ cat /etc/shells
    /bin/bash
    /bin/sh
    /bin/tcsh
    /bin/csh
    ```

    ```shell
    $ echo $SHELL
    /bin/bash
    ```

* Home directory

    * Subdirectory of /home, can be found by  `$ echo $HOME`

### 3. important configuration files

| File                                        | Information/service                                          |
| ------------------------------------------- | ------------------------------------------------------------ |
| `aliases`                                   | Mail aliases file  for use with the Sendmail and Postfix mail server.  Running a mail  server on each and every system has long been common use in the UNIX  world, and almost every Linux distribution still comes with a Sendmail  package.  In this file local user names are matched with real names as  they occur in E-mail addresses, or with other local addresses. |
| `apache`                                    | Config files for the Apache web server.                      |
| `bashrc`                                    | The system-wide  configuration file for the Bourne Again SHell.  Defines functions and  aliases for all users.  Other shells may have their own system-wide  config files, like `cshrc`. |
| `crontab` and the `cron.*` directories      | Configuration of  tasks that need to be executed periodically -  backups, updates of the  system databases, cleaning of the system, rotating logs etc. |
| `default`                                   | Default options for certain commands, such as **useradd**.   |
| `filesystems`                               | Known file systems: ext3, vfat, iso9660 etc.                 |
| `fstab`                                     | Lists partitions and their *mount points*.                   |
| `ftp*`                                      | Configuration of the ftp-server: who can connect, what parts of the system are accessible etc. |
| `group`                                     | Configuration file for user groups.  Use the shadow utilities **groupadd**, **groupmod** and **groupdel** to edit this file.  Edit manually only if you really know what you are doing. |
| `hosts`                                     | A list of machines that can be contacted using the network, but without the need for a  domain name service.  This has nothing to do with the system's network  configuration, which is done in `/etc/sysconfig`. |
| `inittab`                                   | Information for booting: mode, number of text consoles etc.  |
| `issue`                                     | Information about the distribution (release version and/or kernel info). |
| `ld.so.conf`                                | Locations of library files.                                  |
| `lilo.conf`, `silo.conf`, `aboot.conf` etc. | Boot information for the LInux LOader, the system for booting that is now gradually being replaced with GRUB. |
| `logrotate.*`                               | Rotation of the logs, a system preventing the collection of huge amounts of log files. |
| `mail`                                      | Directory containing instructions for the behavior of the mail server. |
| `modules.conf`                              | Configuration of modules that enable special features (drivers). |
| `motd`                                      | Message Of The  Day: Shown to everyone who connects to the system (in text mode), may be used by the system admin to announce system services/maintenance etc. |
| `mtab`                                      | Currently mounted file systems.  It is advised to never edit this file. |
| `nsswitch.conf`                             | Order in which to contact the name resolvers when a process demands resolving of a host name. |
| `pam.d`                                     | Configuration of authentication modules.                     |
| `passwd`                                    | Lists local users.  Use the shadow utilities **useradd**, **usermod** and **userdel** to edit this file.  Edit manually only when you really know what you are doing. |
| `printcap`                                  | Outdated but still frequently used printer configuration file.  Don't edit this manually  unless you really know what you are doing. |
| `profile`                                   | System wide configuration of the shell environment: variables, default properties of new files, limitation of resources etc. |
| `rc*`                                       | Directories defining active services for each run level.     |
| `resolv.conf`                               | Order in which to contact DNS servers (Domain Name Servers only). |
| `sendmail.cf`                               | Main config file for the Sendmail server.                    |
| `services`                                  | Connections accepted by this machine (open ports).           |
| `sndconfig` or `sound`                      | Configuration of the sound card and sound events.            |
| `ssh`                                       | Directory containing the config files for secure shell client and server. |
| `sysconfig`                                 | Directory  containing the system configuration files: mouse, keyboard, network,  desktop, system clock, power management etc. (specific to RedHat) |
| `X11`                                       | Settings for the  graphical server, X.  RedHat uses XFree, which is reflected in the name  of the main configuration file, XFree86Config.  Also contains the  general directions for the window managers available on the system, for  example **gdm**, **fvwm**, **twm**, etc. |
| `xinetd.*` or `inetd.conf`                  | Configuration  files for Internet services that are run from the system's (extended)  Internet services daemon (servers that don't run an independent daemon). |



## 3. Storage Area Network (SAN) and Network Attached Storage (NAS)

* SAN: specialized high speed network that provides block level network access to storage, which composed of hosts, switches, storage elements, and storage devices that are interconnected using variety of technologies, topologies and protocols
    * Improve application availability
    * enhance application performance
    * increase storage utilization and effectiveness, also improve data protection and security
    * part of business continuity management
    * map directories/folders to SAN
* NAS: a NAS creates a small network all its own that any device with right credentials can access
    * faster than remote cloud storage
    * hosting files yourself, no 3rd parth involved
    * used as a shared directory/folder

# 5. Basic Linux Commands

-----

* `top`: performance monitoring program for CPU, memory, swap, cache, buffer, process PID, user, command usage

* `vmstat`: statistics of virtual memory, kernel threads, disks, system processes, IO blocks, interrupts, CPU activity

    ```shell
    $ vmstat
    procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
     r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
     1  0      0 13655140  14028 2172412    0    0     0     2    4    2  0  0 100  0  0
    ```

* `free`: most simple and easy way to cehck memory usage

* `/proc/meminfo`: memoery info file. `/proc/` file system does not contain real files, they have virtual files contain dynamic information about the kernel and the system 

* `lsof`: list open files and processes. including disk files, network sockets, pipes, devices and processes.

* `tcpdump`: network packet analyzer. capture or filter TCP/IP packets

    ```shell
    $ tcpdump -i eth0
    ```

* `netstat`: monitoring incoming and outgoing network packets statistics

    ```shell
    $ netstat -a | more
    $ netstat -i
    ```

* `htop`: interactive and real time linux process monitoring tool. need to install using yum

* `iotop`: similar to top and htop, used to display real time disk IO and process. needs installation

* `iostat`: show system input and output storage device statistics, used to trace storage device performance issues, including NFS

* `ifconfig`: view active network interfaces

* `arp -a`: display current ARP value

    ```shell
    $ arp -a
    ip-172-31-16-1.ap-southeast-2.compute.internal (172.31.16.1) at 0a:60:37:27:16:54 [ether] on eth0
    ```



# 6. Linux Performance

-----

benchmarking using `sysbench`

## 1. CPU Benchmark

```shell
$ sysbench --test=cpu --cpu-max-prime=20000 run
WARNING: the --test option is deprecated. You can pass a script name or path on the command line without any options.
sysbench 1.0.17 (using system LuaJIT 2.0.4)

Running the test with following options:
Number of threads: 1
Initializing random number generator from current time


Prime numbers limit: 20000

Initializing worker threads...

Threads started!

CPU speed:
    events per second:   402.43

General statistics:
    total time:                          10.0024s
    total number of events:              4026

Latency (ms):
         min:                                    2.34
         avg:                                    2.48
         max:                                    7.35
         95th percentile:                        2.81
         sum:                                 9999.66

Threads fairness:
    events (avg/stddev):           4026.0000/0.00
    execution time (avg/stddev):   9.9997/0.00
```

## 2. File IO Benchmark

prepare the data file:

```shell
sysbench --test=fileio --file-total-size=50G prepare
```

run the benchmarking test:

```shell
sysbench --test=fileio --file-total-size=50G --file-test-mode=rndrw --init-rng=on --max-time=300 --max-requests=0 run

Running the test with following options:
Number of threads: 1
Initializing random number generator from current time


Extra file open flags: (none)
128 files, 400MiB each
50GiB total file size
Block size 16KiB
Number of IO requests: 0
Read/Write ratio for combined random IO test: 1.50
Periodic FSYNC enabled, calling fsync() each 100 requests.
Calling fsync() at the end of test, Enabled.
Using synchronous I/O mode
Doing random r/w test
Initializing worker threads...

Threads started!


File operations:
    reads/s:                      1371.61
    writes/s:                     914.40
    fsyncs/s:                     2926.43

Throughput:
    read, MiB/s:                  21.43
    written, MiB/s:               14.29

General statistics:
    total time:                          300.0057s
    total number of events:              1563644

Latency (ms):
         min:                                    0.00
         avg:                                    0.19
         max:                                   16.52
         95th percentile:                        0.63
         sum:                               298976.80

Threads fairness:
    events (avg/stddev):           1563644.0000/0.00
    execution time (avg/stddev):   298.9768/0.00
```

clean up the test files:

```shell
sysbench --test=fileio --file-total-size=50G cleanup
```



## 3. MySQL Benchmarking

Create test table with 1,000,000 rows of data:

```shell
sysbench --test=oltp --oltp-table-size=1000000 --db-driver=mysql --mysql-db=test --mysql-user=root --mysql-password=yourrootsqlpassword prepare
```













