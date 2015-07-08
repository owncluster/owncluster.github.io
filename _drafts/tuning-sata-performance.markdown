---
title: Tuning SATA performance
author: Jörn
---
the sunxi community seems to have done some testing http://linux-sunxi.org/SATA#Performance

This section is work in progress
It has been tested to work up to 60MB/s. How, and with what SoC / device combination?
SATA throughput is unbalanced for unknown reasons: With appropriate cpufreq settings it's possible to get sequential read speeds of +200 MB/s while write speeds retain at approx. 45 MB/s. This might be caused by buggy silicone or driver problems.
Unlike other platforms sequential SATA transfer rates on A10/A20 scale somewhat linearly with both cpufreq settings and DRAM clock. In case you use the wrong cpufreq settings it's impossible to achieve maximum SATA performance (eg. using the ondemand governor without io_is_busy setting).
On the dual-core A20 setting both CONFIG_SCHED_MC=y and CONFIG_SCHED_SMT=y at kernel compile time seems to increase SATA throughput (sequential reads +10 MB/s).
Also worth a look are Linux' I/O schedulers. If your SATA disk is available as /dev/sda you can query /sys/block/sda/queue/scheduler to get the list of available I/O schedulers (the active printed in brackets) and change the scheduler either globally by supplying elevator=deadline to bootargs environment or on a per device basis using echo deadline >/sys/block/sdN/queue/scheduler (deadline seems to be the most performant scheduler on A10/A20)
Since irqbalancing isn't working on sunxi/ARM one way to get better SATA throughput on A20 devices is to assign all AHCI/SATA IRQs to the second CPU core using
echo 2 >/proc/irq/$(awk -F":" '/ahci/ {print $1}' </proc/interrupts)/smp_affinity
