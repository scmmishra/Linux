## Check Hardware Info
`lscpu`

lscpu  gathers  CPU  architecture information from sysfs and /proc/cpuinfo.  The command output can be optimized for parsing or for easy readability by humans.  The information includes, for example,  the  number  of  CPUs,  threads, cores, sockets, and Non-Uniform Memory Access (NUMA) nodes.  There is also information about the  CPU  caches  and  cache  sharing,  family,  model, bogoMIPS, byte order, and stepping.
```
$ lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                4
On-line CPU(s) list:   0-3
Thread(s) per core:    1
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1
Vendor ID:             GenuineIntel
CPU family:            6
Model:                 23
Stepping:              10
CPU MHz:               1998.000
BogoMIPS:              5302.48
Virtualization:        VT-x
L1d cache:             32K
L1i cache:             32K
L2 cache:              2048K
NUMA node0 CPU(s):     0-3
```

