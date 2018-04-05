## Basic Configuration
### Set the Hostname

Please follow our instructions for setting your hostname. You can use the following commands to make sure it is set properly:

    hostname
    hostname -f

The first command should show your short hostname, and the second should show your fully qualified domain name (FQDN).

### Set the Time Zone

When setting the time zone of your server, it may be best to use the time zone of the majority of your users. If you're not sure which time zone would be best, consider using Universal Coordinated Time or UTC (i.e., Greenwich Mean Time).

By default, Linodes are set to UTC. Many operating systems provide built-in, interactive methods for changing time zones:

#### Set the Time Zone in Debian or Ubuntu

Issue the following command and answer the questions as prompted on the screen:

    dpkg-reconfigure tzdata

#### Set the Time Zone in CentOS 7 or Arch Linux

1. View the list of available time zones:

        timedatectl list-timezones

    Use the `Up`, `Down`, `Page Up` and `Page Down` keys to navigate to the correct zone. Remember it, write it down or copy it as a mouse selection. Then press **q** to exit the list.

2. Set the time zone (change `America/New_York` to the correct zone):

        timedatectl set-timezone 'America/New_York'

#### Set the Time Zone Manually on a Linux System

Find the appropriate zone file in `/usr/share/zoneinfo/` and link that file to `/etc/localtime`. See the examples below for possibilities:

Universal Coordinated Time:

    ln -sf /usr/share/zoneinfo/UTC /etc/localtime

Eastern Standard Time:

    ln -sf /usr/share/zoneinfo/EST /etc/localtime

American Central Time (including Daylight Savings Time):

    ln -sf /usr/share/zoneinfo/US/Central /etc/localtime

American Eastern Time (including Daylight Savings Time):

    ln -sf /usr/share/zoneinfo/US/Eastern /etc/localtime

### Configure the /etc/hosts File

The `/etc/hosts` file provides a list of IP addresses with corresponding hostnames. This allows you to specify hostnames for an IP address in one place on the local machine, and then have multiple applications connect to external resources via their hostnames. The system of host files precedes [DNS](https://linode.com/docs/networking/dns/dns-records-an-introduction/), and hosts files are *always* checked before DNS is queried. As a result, `/etc/hosts` can be useful for maintaining small "internal" networks, for development purposes, and for managing clusters.

Some applications require that the machine properly identify itself in the `/etc/hosts` file. As a result, we recommend configuring the `/etc/hosts` file shortly after deployment. Here is an example file:

{{< file "/etc/hosts" py >}}
127.0.0.1   localhost.localdomain   localhost
103.0.113.12    username.example.com   username
{{< /file >}}


You can specify a number of hostnames on each line separated by spaces. Every line must begin with one and only one IP address. In the above example, replace `103.0.113.12` with your machine's IP address. Consider a few additional `/etc/hosts` entries:

{{< file "/etc/hosts" py >}}
198.51.100.30   example.com
192.168.1.1     stick.example.com

{{< /file >}}


In this example, all requests for the `example.com` hostname or domain will resolve to the IP address `198.51.100.30`, which bypasses the DNS records for `example.com` and returns an alternate website.

The second entry tells the system to look to `192.168.1.1` for the domain `stick.example.com`. These kinds of host entries are useful for using "private" or "back channel" networks to access other servers in a cluster without needing to send traffic on the public network.

## Network Diagnostics

In this section, we'll review some basic Linux commands that will help you assess and diagnose network problems.

### The ping Command

The `ping` command tests the connection between the local machine and a remote address or machine. The following commands "ping" `google.com` and `216.58.217.110`:

    ping google.com
    ping 216.58.217.110

These commands send a small amount of data (an ICMP packet) to the remote host and wait for a response. If the system is able to make a connection, it will report on the "round trip time" for every packet. Here is the sample output of four pings to google.com:

    PING google.com (216.58.217.110): 56 data bytes
    64 bytes from 216.58.217.110: icmp_seq=0 ttl=54 time=14.852 ms
    64 bytes from 216.58.217.110: icmp_seq=1 ttl=54 time=16.574 ms
    64 bytes from 216.58.217.110: icmp_seq=2 ttl=54 time=16.558 ms
    64 bytes from 216.58.217.110: icmp_seq=3 ttl=54 time=18.695 ms
    64 bytes from 216.58.217.110: icmp_seq=4 ttl=54 time=25.885 ms

The `time` field specifies in milliseconds the duration of the round trip for an individual packet. When you've gathered the amount of information you need, use **Control+C** to interrupt the process. You'll be presented with some statistics once the process is stopped. This will resemble:

    --- google.com ping statistics ---
    4 packets transmitted, 4 received, 0% packet loss, time 3007ms
    rtt min/avg/max/mdev = 33.890/40.175/53.280/7.679 ms

There are several important data points to notice:

-   *Packet Loss*, or the discrepancy between the number of packets sent and the number of packets that return successfully. This number shows the percentage of packets that are dropped.
-   *Round Trip Time* (rtt) statistics on the final line report information about all the ping responses. For this ping we see that the fastest packet round trip (min) took 33.89 milliseconds. The average round trip (avg) took 40.175 milliseconds. The longest packet (max) took 53.28 milliseconds. A single standard deviation unit (mdev) for these four packets is 7.67 milliseconds.

The ping command is useful as an informal diagnostic tool to measure point-to-point network latency, and as a tool to simply ensure you are able to make a connection to a remote server.

### The traceroute Command

The `traceroute` command expands on the functionality of the [ping](#the-ping-command) command. It provides a report on the path that the packets take to get from the local machine to the remote machine. Each step (intermediate server) in the path is called a *hop*. Route information is useful when troubleshooting a networking issue: if there is packet loss in one of the first few hops the problem is often related to the user's local area network (LAN) or Internet service provider (ISP). By contrast, if there is packet loss near the end of the route, the problem may be caused by an issue with the server's connection.

Here is an example of output from a `traceroute` command:

    traceroute to google.com (74.125.53.100), 30 hops max, 40 byte packets
    1 207.192.75.2 (207.192.75.2) 0.414 ms 0.428 ms 0.509 ms
    2 vlan804.tbr2.mmu.nac.net (209.123.10.13) 0.287 ms 0.324 ms 0.397 ms
    3 0.e1-1.tbr2.tl9.nac.net (209.123.10.78) 1.331 ms 1.402 ms 1.477 ms
    4 core1-0-2-0.lga.net.google.com (198.32.160.130) 1.514 ms 1.497 ms 1.519 ms
    5 209.85.255.68 (209.85.255.68) 1.702 ms 72.14.238.232 (72.14.238.232) 1.731 ms 21.031 ms
    6 209.85.251.233 (209.85.251.233) 26.111 ms 216.239.46.14 (216.239.46.14) 23.582 ms 23.468 ms
    7 216.239.43.80 (216.239.43.80) 123.668 ms 209.85.249.19 (209.85.249.19) 47.228 ms 47.250 ms
    8 209.85.241.211 (209.85.241.211) 76.733 ms 216.239.43.80 (216.239.43.80) 73.582 ms 73.570 ms
    9 209.85.250.144 (209.85.250.144) 86.025 ms 86.151 ms 86.136 ms
    10 64.233.174.131 (64.233.174.131) 80.877 ms 216.239.48.34 (216.239.48.34) 76.212 ms 64.233.174.131 (64.233.174.131) 80.884 ms
    11 216.239.48.32 (216.239.48.32) 81.267 ms 81.198 ms 81.186 ms
    12 216.239.48.137 (216.239.48.137) 77.478 ms pw-in-f100.1e100.net (74.125.53.100) 79.009 ms 216.239.48.137 (216.239.48.137) 77.437 ms

Often the hostnames and IP addresses on either side of a failed jump are useful in determining who operates the machine where the routing error occurs. Failed jumps are designated by lines with three asterisks (`* * *`).

### The mtr Command

The `mtr` command, like the [traceroute](#the-traceroute-command) tool, provides information about the route that internet traffic takes between the local system and a remote host. However, `mtr` provides additional information about the round trip time for the packet. In a way, you can think of `mtr` as a combination of traceroute and ping.

Here is an example of output from an `mtr` command:

    HOST: username.example.com              Loss%   Snt     Last    Avg     Best    Wrst    StDev
        1.  256.129.75.4                    0.0%    10      0.4     0.4     0.3     0.6     0.1
        2.  vlan804.tbr2.mmu.nac.net        0.0%    10      0.3     0.4     0.3     0.7     0.1
        3.  0.e1-1.tbr2.tl9.nac.net         0.0%    10      4.3     4.4     1.3     11.4    4.1
        4.  core1-0-2-0.lga.net.google.com  0.0%    10      64.9    11.7    1.5     64.9    21.2
        5.  209.85.255.68                   0.0%    10      1.7     4.5     1.7     29.3    8.7
        6.  209.85.251.9                    0.0%    10      23.1    35.9    22.6    95.2    27.6
        7.  72.14.239.127                   0.0%    10      24.2    24.8    23.7    26.1    1.0
        8.  209.85.255.190                  0.0%    10      27.0    27.3    23.9    37.9    4.2
        9.  gw-in-f100.1e100.net            0.0%    10      24.1    24.4    24.0    26.5    0.7

Like the `ping` command, `mtr` tracks the speed of the connection in real time until you exit the program with **CONTROL+C**. To have `mtr` stop automatically and generate a report after ten packets, use the `--report` flag:

    mtr --report

Be aware that `mtr` will pause for a few moments while generating output. For more information regarding `mtr` consider our [diagnosing network issues with mtr](https://linode.com/docs/networking/diagnostics/diagnosing-network-issues-with-mtr/) guide.

## System Diagnostics

If you're having an issue with your Linode that is neither related to [networking](#network-diagnostics) nor another application issue, it may help to rule out "hardware" and operating system level issues. Use the following tools to better diagnose and resolve these.

If you determine that you have a problem with memory usage, refer to our guide on [resolving memory usage issues](https://linode.com/docs/troubleshooting/troubleshooting-memory-and-networking-issues/). Use the following tools and approaches to determine the specific cause of your troubles.

### Check Current Memory Usage

To see how much memory your system is currently using:

    free -m

On a Linode 2GB under moderate use, the output should resemble the following:

                 total       used       free     shared    buffers     cached
    Mem:          1999        954       1044        105         34        703
    -/+ buffers/cache:        216       1782
    Swap:          255          0        255

This output takes a bit of careful reading to interpret. Out of a total 1999 megabytes of memory (RAM), the system is using 954 megabytes and has 1044 megabytes free. *However*, the system also has 703 megabytes of "stale" data buffered and stored in cache. The operating system will "drop" the caches if it needs the space, but retains the cache if there is no other need for the space. It is normal for a Linux system to leave old data in RAM until the space is needed, so don't be alarmed if only a small amount of memory is "free."

In the above example, there are 1782 megabytes of memory that are actually *free*. This means 1782 megabytes are available to your system when you start an additional process or a running application needs more memory.

### Monitor I/O Usage with vmstat

The `vmstat` tool provides information about memory, swap utilization, I/O wait, and system activity. It is particularly useful for diagnosing I/O-related issues.

If you think you're having an I/O issue then run the following command:

    vmstat 1 20

This runs a vmstat every second, twenty times, giving a sample of the current state of the system. The output generated resembles the following:

    procs -----------memory---------- ---swap-- -----io---- -system-- ----cpu----
     r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa
     0  0      4  32652  47888 110824    0    0 0     2   15   15  0  0 100  0
     0  0      4  32644  47888 110896    0    0 0     4  106  123  0  0 100  0
     0  0      4  32644  47888 110912    0    0 0     0   70  112  0  0 100  0
     0  0      4  32644  47888 110912    0    0 0     0   92  121  0  0 100  0
     0  0      4  32644  47888 110912    0    0 0    36   97  136  0  0 100  0
     0  0      4  32644  47888 110912    0    0 0     0   96  119  0  0 100  0
     0  0      4  32892  47888 110912    0    0 0     4   96  125  0  0 100  0
     0  0      4  32892  47888 110912    0    0 0     0   70  105  0  0 100  0
     0  0      4  32892  47888 110912    0    0 0     0   97  119  0  0 100  0
     0  0      4  32892  47888 110912    0    0 0    32   95  135  0  0 100  0
     0  0      4  33016  47888 110912    0    0 0     0   75  107  0  0 100  0
     0  0      4  33512  47888 110912    0    0 0    24  113  134  0  0 100  0
     0  0      4  33512  47888 110912    0    0 0     0  175  244  0  0 100  0
     0  0      4  33512  47888 110912    0    0 0     0   92  148  0  0 100  0
     0  0      4  33512  47888 110912    0    0 0     0  114  162  0  0 100  0
     0  0      4  33512  47888 110912    0    0 0    36  100  157  0  0 100  0
     0  0      4  33388  47888 110912    0    0 0     0  116  166  0  0 100  0
     0  0      4  33388  47888 110912    0    0 0     0   97  157  0  0 100  0
     0  0      4  33388  47888 110912    0    0 0     0   89  144  0  0 100  0
     0  0      4  33380  47888 110912    0    0 0     0  181  185  0  0 99  0

The memory and swap columns provide the same kind of information provided by the "[free -m](#check-current-memory-usage)" command, albeit in a slightly harder to understand format. The most relevant information produced by this command is the `wa` column, which is the final column in most implementations. This field displays the amount of time the CPU spends waiting for I/O operations to complete.

If this number is consistently and considerably higher than 0, you might consider taking measures to address your IO usage. However, if the `vmstat` output resembles the above, you can be sure in the knowledge that you're not experiencing an IO-related issues.

### Monitor Processes, Memory, and CPU Usage with htop

If you want a more organized, real-time view of the current state of your system, we recommend a tool called `htop`. This is not installed by default on most systems. To install `htop`, issue one of the following commands, depending on which distribution you use:

    apt-get install htop
    yum install htop
    pacman -S htop
    emerge sys-process/htop

To start the program:

    htop

You can quit at any time by pressing the `F10` or `Q` keys. There are a couple of `htop` behaviors that may not be initially intuitive. Take note of the following:

-   The memory utilization graph displays used memory, buffered memory, and cached memory. The numbers displayed at the end of this graph reflect the total amount of memory available and the total amount memory on the system as reported by the kernel.
-   The default configuration of `htop` presents all application threads as independent processes, which may not be clear if you're not aware of it. You can disable this by selecting the "setup" option with `F2`, then "Display Options," and then toggling the "Hide userland threads" option.
-   You can toggle a "Tree" view with the `F5` key that displays the processes in a hierarchy and shows which processes were spawned by other processes in an organized format. This is helpful in diagnosing a problem when you're having trouble distinguishing among processes.

## File System Management

Web developers and editors often use the FTP protocol to transfer and manage files on a remote system. FTP, however, is very insecure and inefficient for managing the files on a system when you have SSH access.

### Protect Files on a Remote Server

Because Linode servers are network accessible and often have a number of distinct users, maintaining the security of files is often an important concern.

We suggest the following best practices for maintaining security:

-   Only give users the permission to do what they need to. This includes application-specific users.
-   Only run services on public interfaces that you are actively using. One common source of security vulnerabilities is in unused daemons that are left running. This includes database servers, HTTP development servers, and FTP servers.
-   Use SSH connections whenever possible to secure and encrypt the transfer of sensitive information.

### Symbolic Links

*Symbolic linking*, colloquially "symlinking", allows you to create an object in your filesystem that points to another object on your filesystem. This is useful when you need to provide users and applications access to specific files and directories without reorganizing your folders. This way you can provide restricted users access to your web-accessible directories without moving your `DocumentRoot` into their home directories.

To create a symbolic link, issue a command in the following format:

    ln -s /home/username/config-git/etc-hosts /etc/hosts

This creates a link of the file `etc-hosts` at the location of the system's `/etc/hosts` file. More generically:

    ln -s [/path/to/target/file] [/path/to/location/of/sym/link]

Note the following features of the link command:

-   The final term, the location of the link, is optional. If you omit the link destination, a link will be created in the current directory with the same name as the file you're linking to.
-   When specifying the location of the link, ensure that path does not have a final trailing slash. You can create a sym link that *targets* a directory, but sym links cannot terminate with slashes.
-   You may remove a symbolic link without affecting the target file.
-   You can use relative or absolute paths when creating a link.

### Manage Files on a Linux System
To **copy** files:

    cp /home/username/todo.txt /home/username/archive/todo.01.txt

This copies `todo.txt` to an archive folder, and adds a number to the file name. If you want to recursively copy all of the files and subdirectories in a directory to another directory, use the `-R` option. This command looks like:

    cp -R /home/username/archive/ /srv/backup/username.01/

To **move** a file or directory:

    mv /home/username/archive/ /srv/backup/username.02/

You can also use the `mv` command to rename a file.

To **delete** a file:

    rm scratch.txt

This will delete the `scratch.txt` file from the current directory.

For more information about file system navigation and manipulation, please consider our documentation of [file system navigation](/docs/tools-reference/ssh/using-the-terminal/#file-system-navigation).


## Text Manipulation

Among Linux and UNIX-like systems, nearly all system configuration information is stored and manipulated in plain text form. These following sections show a list of basic Linux commands and tools to manipulate text files.

### Search for a String in Files with grep

The `grep` tool allows you to search a stream of text, such as a file or the output of a command, for a term or regex pattern.

To use the `grep` tool, let's review an example:

    grep "^Subject:.*HELP.*" /home/username/mbox

This will search your mail spool for subject lines (i.e. begins with the word "Subject:"), beginning with any number of characters, containing the word "help" in upper case, and followed by any number of additional characters. It would then print these results in the terminal.

The `grep` tool provides additional options that, if specified, force the program to output the context for each match (e.g., with `-C 2` for two lines of context). With `-n`, `grep` outputs the line number of the match. With `-H`, `grep` prints the file name for each match, which is useful when you "grep" a group of files or "grep" recursively through a file system (using `-r`). Use `grep --help` for more options.

To grep a group of files, you can specify the file with a wildcard:

    grep -i "morris" ~/org/*.txt

This will find and match against every occurrence of the word "morris," while ignoring case (because of the option for `-i`). The `grep` tool will search all files in the `~/org/` directory with a .txt extension.

You can use `grep` to filter the results of another command that sends output to standard out (`stdout`). This is done by "piping" the output of one command into `grep`. For instance:

    ls /home/username/data | grep "1257"

In this example, we assume that the `/home/username/data` directory contains a large number of files that have a UNIX time stamp in their file names. The above command will filter the output to only display those tiles that have the four digits "1257" in their file names. In these cases, `grep` only filters the output of `ls` and does not look into file contents. For more information regarding `grep`, refer to our full documentation of the [grep command](https://linode.com/docs/tools-reference/search-and-filter-text-with-grep).

### Search and Replace Across a Group of Files

While the [grep](#search-for-a-string-in-files-with-grep) tool is quite powerful for filtering text on the basis of regular expressions, if you need to edit a file or otherwise manipulate the text, you can use `sed`. The `sed` tool, or the Stream EDitor, allows you search for a regex pattern and replace it with another string.

`sed` is extremely powerful, and we recommend that you back up your files and test your `sed` commands thoroughly before running them. Here is a very simple `sed` one-liner, intended to illustrate its syntax:

    sed -i `s/^good/BAD/` morning-star.txt

This replaces occurrences of the word "good" at the beginning of a line (noted by the `^`) with the string "BAD" in the file `morning-star.txt`. The option `-i` tells `sed` to perform the replacements "in place." The `sed` command can make backups of the files it edits if you specify a suffix after the `-i` option, as in `-iBAK`. In the above command this option would save the original file as `morning-star.txt.BAK` before making changes.

The general format of a `sed` statement is:

    's/[regex]/[replacement]/'

To match literal slashes (`/`), you must escape them with a backslash (`\`). As a result, to match a `/` character you would use `\/` in the `sed` expression. If you are searching for a string that has multiple slashes, you can replace the slashes which another character. For instance:

    's|r/e/g/e/x|regex|'

This would strip the slashes from the string `r/e/g/e/x` so that this string would be `regex` after running the `sed` command on the file that contains the string.

The following example searches and replaces one IP address with another. In this case `98.76.54.32` is replaced with `12.34.56.78`:

    sed -i 's/98\.76\.54\.32/12\.34\.56\.78/'

In the above example, period characters are escaped as `\.`. In regular expressions the full-stop (period) character matches to any character if it is not escaped.

For more information about `sed` refer to our full documentation of [text manipulation with sed](https://linode.com/docs/tools-reference/tools/manipulate-text-from-the-command-line-with-sed/).

### Edit Text

In many Linode documents, you may be instructed to edit the contents of a file. To do this, you need to use a text editor. Most of the distribution templates that Linode provides come with an implementation of the vi/vim text editor and the nano text editor. These are small, lightweight and powerful text editors that allow you manipulate the text of a file from the terminal environment.

There are other options for text editors, notably emacs and "zile." Feel free to install these programs using your operating system's package manager. Make sure you [search your package database](#find-package-names-and-information) so you can install a version compiled without GUI components (i.e. X11).

To open a file, issue a command beginning with the name of the editor you wish to run followed by the name of the file you wish to edit. Here are a number of example commands that open the `/etc/hosts` file:

    nano /etc/hosts
    vi /etc/hosts
    emacs /etc/hosts
    zile /etc/hosts

When you've edited a file, you can save and exit the editor to return to the prompt. This procedure varies between different editors. In emacs and zile, the key sequence is the same: press control and type x and s to save. This operation is typically notated "C-x C-s" and then "C-x C-c" to close the editor. In nano, press Control-O (notated \^O) and confirm the file name to write the file, and use Control-X to exit from the program.

Since vi and vim are *modal* editors, their operation is a bit more complex. After opening a file in vi, you can enter "insert" mode by pressing the "i" key; this will let you edit text in the conventional way. To save the file, you must exit into "normal" mode by pressing the escape key (`Control-[` also sends escape), and then type `:wq` to write the file and quit the program.

This provides only the most basic outline of how to use these text editors, and there are numerous external resources which will provide instructions for more advanced use of this software.