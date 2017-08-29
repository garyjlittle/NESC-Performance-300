# NESC-Performance-300

This directory contains the fio configuration files that I use in the POC Demo.

## slowops.fio
This script shows the "poor performance" on my example cluster node, the achieved performance is around 2400 IOPS.  

## single-vdisk-max.fio
The single-vdisk-max is the same as the "slowops" script, except that is has a queue depth of 128  rather than 1.  You can see that these are otherwise exactly the same by running "diff" on the two scripts.


```
garylittle$ diff slowops.fio single-vdisk-max.fio
6c6
< iodepth=1
---
> iodepth=128
```
With the additional queue depth, the cluster node now has a steady stream of work to do, rather than spending a lot of time waiting for new work to arrive.  With a queue depth of 128, the node achieves around 22,000 IOPS.  Almost 10x of the original example.

## fastops-1ctr.fio & fastops-1ctr-cached.fio
These two scripts generate around 80,000 IOPS.  There only difference is the "working set size".  In the "cached" case the workingset size is 4x1GB and so is easily cached in the DRAM of the CVM.  The non-cached script is 4x32G and is not cacheable, so the bits are pulled all the way from SSSD.

```
garylittle$ diff fastops-1ctr.fio fastops-1ctr-cached.fio
11c11
< size=32g
---
> size=1g
```

## fastops-1ctr-fio Vs fastops-2ctr
In this comparison, we simply chose to spread 4 vdisks across two containers to alleviate the EPOLL thread bottleneck.  In this example the two container example almost doubles the single container case to generate 150,000 IOPS from a single node.
```
garylittle$ diff fastops-1ctr-cached.fio fastops-2ctr-cached.fio
17c17
< filename=/dev/sdg1
---
> filename=/dev/sdj1
19c19
< filename=/dev/sdh1
---
> filename=/dev/sdk1
```
