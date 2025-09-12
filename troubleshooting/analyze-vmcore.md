How to set up a vmcore analysis environment?

https://access.redhat.com/solutions/767753



Vmcore analysis techniques

https://access.redhat.com/articles/1406253



可使用这个工具在线分析

Kernel Oops Analyzer

https://access.redhat.com/labs/kerneloopsanalyzer/



reboot analyz

https://access.redhat.com/support/cases/#/case/04031971

https://access.redhat.com/support/cases/#/case/04148072

memory issue

```
crash> sys
        CPUS: 2
        DATE: Tue May 20 02:59:34 UTC 2025
      UPTIME: 11:59:05
LOAD AVERAGE: 74.62, 71.06, 50.37 <<——[High Load Average]
       TASKS: 373
    NODENAME: INMUDA0837LPS01
     RELEASE: 4.18.0-553.47.1.el8_10.x86_64
     VERSION: #1 SMP Thu Mar 20 10:01:53 EDT 2025
     MACHINE: x86_64  (2593 Mhz)
      MEMORY: 4 GB
       PANIC: "Kernel panic - not syncing: Out of memory: system-wide panic_on_oom is enabled"
crash> 
crash> sys -i
        DMI_BIOS_VENDOR: American Megatrends Inc.
       DMI_BIOS_VERSION: 090008 
          DMI_BIOS_DATE: 12/07/2018
         DMI_SYS_VENDOR: Microsoft Corporation
       DMI_PRODUCT_NAME: Virtual Machine



=================
System Paremeter:
=================

crash> sysctl_panic_on_oom
sysctl_panic_on_oom = $1 = 1
crash> 


===================
Kernel Ring Buffer:
===================

crash> log
[43145.386847] [  12609]     0 12609     7001      222    94208        0             0 crond
[43145.391185] [  12610]     0 12610     7001      222    94208        0             0 crond
[43145.395327] [  12611]     0 12611     6484      220    86016        0             0 crond
[43145.399689] Kernel panic - not syncing: Out of memory: system-wide panic_on_oom is enabled
               
[43145.405103] CPU: 1 PID: 2444 Comm: smpolicysrv Kdump: loaded Not tainted 4.18.0-553.47.1.el8_10.x86_64 #1
[43145.411186] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090008  12/07/2018
[43145.416808] Call Trace:
[43145.418144]  dump_stack+0x41/0x60
[43145.419867]  panic+0xe7/0x2ac
[43145.421410]  out_of_memory.cold.36+0x2f/0x7e
[43145.423795]  __alloc_pages_slowpath+0xbf0/0xcd0
[43145.426106]  __alloc_pages_nodemask+0x2e2/0x330
[43145.428634]  pagecache_get_page+0xce/0x310
[43145.430753]  filemap_fault+0x6c8/0xa30
[43145.432886]  ? __mod_lruvec_page_state+0x5e/0x80
[43145.435329]  ? page_add_file_rmap+0x99/0x150
[43145.437544]  ? alloc_set_pte+0xb6/0x420
[43145.440048]  ? xas_load+0x8/0x80
[43145.441745]  ? xas_find+0x183/0x1c0
[43145.443635]  ? filemap_map_pages+0x271/0x410
[43145.445945]  ext4_filemap_fault+0x2c/0x40 [ext4]
[43145.448471]  __do_fault+0x38/0xc0
[43145.450271]  handle_pte_fault+0x55d/0x880
[43145.452382]  __handle_mm_fault+0x552/0x6d0
[43145.454696]  handle_mm_fault+0xca/0x2a0
[43145.456886]  __do_page_fault+0x1e4/0x440
[43145.459041]  do_page_fault+0x37/0x12d
[43145.460983]  ? page_fault+0x8/0x30
[43145.462740]  page_fault+0x1e/0x30
[43145.464508] RIP: 0033:0x7f75015df630
[43145.466358] Code: 3b 2d a4 6f 2b 00 88 45 18 74 dd c7 45 10 00 00 00 00 48 89 5d 00 c6 44 1d 18 00 eb cb 66 2e 0f 1f 84 00 00 00 00 00 0f 1f 00 <f3> 0f 1e fa e9 b7 3d fb ff 0f 1f 80 00 00 00 00 48 83 3d d8 74 2b
[43145.476572] RSP: 002b:00007f74e0f2a7a8 EFLAGS: 00010246
[43145.479727] RAX: 0000000000000000 RBX: 0000000000000000 RCX: 00007f75036698ea
[43145.483741] RDX: 0000000001ab62a8 RSI: 00007f74e0f2a7cf RDI: 00007f74bc080f50
[43145.487617] RBP: 00007f74bc080f50 R08: 0000000000000000 R09: 0000000000000000
[43145.491631] R10: 00007f74e0f2aeb0 R11: 0000000000000206 R12: 00007f74e0f2a830
[43145.495824] R13: 00007f74e0f2a820 R14: 00007f74e0f2a89c R15: 00007f74e0f3569f
[43145.501190] Kernel Offset: 0x27000000 from 0xffffffff81000000 (relocation range: 0xffffffff80000000-0xffffffffbfffffff)




===================
Memory Utilization:
===================

crash> kmem -i
                 PAGES        TOTAL      PERCENTAGE
    TOTAL MEM   938433       3.6 GB         ----
         FREE    23839      93.1 MB    2% of TOTAL MEM
         USED   914594       3.5 GB   97% of TOTAL MEM <<----[Fully utilized]
      BUFFERS       56       224 KB    0% of TOTAL MEM
       CACHED     9726        38 MB    1% of TOTAL MEM
         SLAB    21204      82.8 MB    2% of TOTAL MEM

   TOTAL HUGE        0            0         ----
    HUGE FREE        0            0    0% of TOTAL HUGE

   TOTAL SWAP        0            0         ----
    SWAP USED        0            0    0% of TOTAL SWAP
    SWAP FREE        0            0    0% of TOTAL SWAP

 COMMIT LIMIT   469216       1.8 GB         ----
    COMMITTED  1084994       4.1 GB  231% of TOTAL LIMIT
crash> 


==========================================
Processes utilizing High Memory on server:
==========================================

1. List of user-space tasks and their corresponding memory usage (RSS):

crash> ps -G | sed 's/^>//' | awk '{ m[$9]+=$8 } END { for (item in m) { printf "%20s %10s KiB\n", item, m[item] } }' | sort -k 2 -r -n | head
                java    1392944 KiB <<----[Highest memory consumer]
         smpolicysrv    1270764 KiB <<----[Highest memory consumer]
                 dnf     421064 KiB
     falcon-sensor-b     175436 KiB
     platform-python      28044 KiB
               crond      24276 KiB
     systemd-journal      20608 KiB
             waagent      19624 KiB
               tuned      16036 KiB
            rsyslogd      13836 KiB
crash>


2. The total memory allocated to 'user-space' process(es) .

crash> ps -u -G | sed 's/>//g' | awk '{ total += $8 } END { printf "Total RSS of user-mode: %.02f GiB\n", total/2^20 }'
Total RSS of user-mode: 3.29 GiB <<————
crash>



=============
Observations:
=============

1. The system was panicked with the string "Kernel panic - not syncing: Out of memory: system-wide panic_on_oom is enabled".

   The out-of-memory (OOM) is a state of computer operation where no additional memory can be allocated for use by programs or the operating system. Such a system will be unable to load any additional programs and since many programs may load additional data into memory during execution, these will cease to function correctly. This occurs because all available memory, including disk swap space, has been allocated. 
          
    - Please refer following article for more information.      
      -------- 
      Server reboot due to "Kernel panic - not syncing: Out of memory: system-wide panic_on_oom is enabled" 
      ==>> https://access.redhat.com/solutions/1756533
      --------

2. The parameter 'sysctl_panic_on_oom' is enabled on the server, so it got rebooted when the out-of-memory event is reported by the server.
   We do not recommend to enable this parameter ('sysctl_panic_on_oom') as it will cause unnecessary reboot in case of an OOM situation.
   So if this parameter is not enabled intentionally or for debugging any issue then kindly disable it. 

3. I can server is configured with around 3.6 GB of RAM & it has been completely utilized.

4. Further analyzing, we found the top memory-consuming processes were “java”  & “smpolicysrv” on OOM events. 

```





https://access.redhat.com/support/cases/#/case/03915352

io issue

```
  KERNEL: /shared/kernel-debuginfo-files/4.18.0-147.5.1.el8_1.x86_64/usr/lib/debug/lib/modules/4.18.0-147.5.1.el8_1.x86_64/vmlinux  [TAINTED]
    DUMPFILE: /cores/03915352/CRM2PRPRDDCRS13-29187376.vmss
        CPUS: 12
        DATE: Tue Aug 27 10:13:12 UTC 2024
      UPTIME: 158 days, 04:04:26
LOAD AVERAGE: 102.15, 101.31, 99.26
       TASKS: 1125
    NODENAME: CRM2PRPRDDCRS13
     RELEASE: 4.18.0-147.5.1.el8_1.x86_64
     VERSION: #1 SMP Tue Jan 14 15:50:19 UTC 2020
     MACHINE: x86_64  (2297 Mhz)
      MEMORY: 56 GB
       PANIC: ""

[I.1]  Increased load due to blocked tasks:

crash> ps -S
  RU: 18
  UN: 96
  ID: 236
  IN: 763
  ZO: 12

[I.2]  Most of the blocked tasks are waiting in IO activity:

PID: 14322    TASK: ffffa0c95ac6af80  CPU: 0    COMMAND: "oracle_14322_pn"
 #0 [ffffbf59d6373d80] __schedule at ffffffff9b2629e3
 #1 [ffffbf59d6373de8] schedule at ffffffff9b262fe8
 #2 [ffffbf59d6373df0] schedule_timeout at ffffffff9b2671ed
 #3 [ffffbf59d6373e90] wait_for_completion at ffffffff9b2639cf
 #4 [ffffbf59d6373ef0] __x64_sys_io_destroy at ffffffff9ad104e2
 #5 [ffffbf59d6373f38] do_syscall_64 at ffffffff9aa041cb
 #6 [ffffbf59d6373f50] entry_SYSCALL_64_after_hwframe at ffffffff9b4000ad
    RIP: 00007f833f92299d  RSP: 00007fff86ce18d8  RFLAGS: 00000246
    RAX: ffffffffffffffda  RBX: 00007f8343ec06a0  RCX: 00007f833f92299d
    RDX: 00007fff86ce1b90  RSI: 00007f8340033da0  RDI: 00007f8343f2a000
    RBP: 00007f8343f2a000   R8: 0000000000000001   R9: 00007fff86ce1b28
    R10: 0000000000000030  R11: 0000000000000246  R12: 0000000000000002
    R13: 0000000000000000  R14: 0000000000000000  R15: 00007fff86ce1b90
    ORIG_RAX: 00000000000000cf  CS: 0033  SS: 002b

[I.3]  In flight IO to several devices:

crash> dev -D
MAJOR GENDISK            NAME       REQUEST_QUEUE      TOTAL ASYNC  SYNC 
  253 ffffa0d6fdcb8800   dm-0       ffffa0cb2cb8b098       3     1     2 
  253 ffffa0d6fe0fd800   dm-1       ffffa0d6f7a6eae8      62     9    53 
  253 ffffa0d701bcb000   dm-3       ffffa0d6ffaaf4a0      53     0    53 
  253 ffffa0d6fe3ba000   dm-6       ffffa0d6ffae89b8       7     0     7 
  253 ffffa0d6fe20c800   dm-7       ffffa0d700844408       2     2     0 
  253 ffffa0d6fe20e800   dm-8       ffffa0d700845778       1     0     1 
  253 ffffa0d6fe20f800   dm-9       ffffa0d7008409b8       1     0     1 

[I.4]  The dm-3 device that shows active IO appears to be related to the /orabkp mount:

crash> mount | grep -e MOUNT -e orabkp
     MOUNT           SUPERBLK     TYPE   DEVNAME   DIRNAME
ffffa0d6fd39a880 ffffa0d6fe22e800 xfs    /dev/mapper/vg_bkp-lv_bkp /orabkp 

crash> struct super_block.s_bdev ffffa0d6fe22e800
  s_bdev = 0xffffa0d6faa47bc0,

crash> struct block_device.bd_disk 0xffffa0d6faa47bc0
  bd_disk = 0xffffa0d701bcb000,

crash> struct gendisk.disk_name ffffa0d701bcb000
  disk_name = "dm-3 

[I.5]  Repeated soft lockups and scheduling stalls are reported:

[13656894.813623] watchdog: BUG: soft lockup - CPU#11 stuck for 22s! [s1-agent:46632]
[13656894.814303] Modules linked in: overlay nf_tables nfnetlink vsock_diag tcp_diag udp_diag raw_diag inet_diag unix_diag af_packet_diag netlink_diag sha512_ssse3 sha512_generic vmw_vsock_vmci_transport vsock sunrpc vmw_balloon sb_edac crct10dif_pclmul crc32_pclmul ghash_clmulni_intel intel_rapl_perf joydev pcspkr sg vmw_vmci i2c_piix4 binfmt_misc ip_tables xfs libcrc32c sr_mod cdrom sd_mod ata_generic vmwgfx drm_kms_helper syscopyarea sysfillrect sysimgblt fb_sys_fops ttm drm ahci libahci crc32c_intel ata_piix libata serio_raw vmxnet3 vmw_pvscsi dm_mirror dm_region_hash dm_log dm_mod [last unloaded: dsa_filter_hook]
[13656894.814332] Features: eBPF/rawtrace eBPF/event
[13656894.814336] CPU: 11 PID: 46632 Comm: s1-agent Kdump: loaded Tainted: P           OEL   --------- -  - 4.18.0-147.5.1.el8_1.x86_64 #1
[13656894.814337] Hardware name: VMware, Inc. VMware Virtual Platform/440BX Desktop Reference Platform, BIOS 6.00 11/12/2020
[13656894.814346] RIP: 0010:0xffffffffc08d2f3c
[13656894.814348] Code: 7f 0a eb fe eb fe eb fe eb fe eb fe 48 8b 55 80 49 81 c6 08 20 00 00 4c 89 f7 be 40 00 00 00 e8 ba 0e 2f da 48 83 f8 ff 7f 0a <eb> fe eb fe eb fe eb fe eb fe 48 8b 7d 90 48 8b 8d 60 ff ff ff 48
[13656894.814349] RSP: 0018:ffffbf59c739fbb8 EFLAGS: 00000297 ORIG_RAX: ffffffffffffff13
[13656894.814350] RAX: fffffffffffffff2 RBX: ffffbf59c739fdc0 RCX: 0000000000000000
[13656894.814351] RDX: 0000000000000000 RSI: 0000000000000000 RDI: ffffdf59ba0c6060
[13656894.814351] RBP: ffffbf59c739fc70 R08: 0000000000000000 R09: 0000000000000000
[13656894.814352] R10: ffffdf59ba0c6020 R11: ffffa0ca07c04540 R12: 000000000001ee30
[13656894.814353] R13: 00000000000007e5 R14: ffffdf59ba0c6020 R15: ffffdf59ba0c401c
[13656894.814354] FS:  00007fa6bd10c700(0000) GS:ffffa0d705ec0000(0000) knlGS:0000000000000000
[13656894.814355] CS:  0010 DS: 0000 ES: 0000 CR0: 0000000080050033
[13656894.814355] CR2: 0000000000000000 CR3: 00000001563d4003 CR4: 00000000001606e0
[13656894.814361] Call Trace:
[13656894.814370]  ? security_sb_mount+0x5/0x60
[13656894.814374]  ? trace_call_bpf+0x73/0xa0
[13656894.814375]  ? security_sb_mount+0x1/0x60
[13656894.814377]  ? kprobe_perf_func+0x21d/0x260
[13656894.814378]  ? security_sb_mount+0x5/0x60
[13656894.814381]  ? aggr_pre_handler+0x40/0x70
[13656894.814383]  ? security_sb_mount+0x1/0x60
[13656894.814386]  ? kprobe_ftrace_handler+0x90/0xf0
[13656894.814390]  ? ftrace_ops_assist_func+0x81/0xf0
[13656894.814391]  ? 0xffffffffc084d0bf
[13656894.814394]  ? filename_lookup.part.64+0xe0/0x170
[13656894.814395]  ? security_sb_mount+0x1/0x60
[13656894.814397]  ? security_sb_mount+0x5/0x60
[13656894.814400]  ? do_mount+0xbf/0xc80
[13656894.814402]  ? _copy_from_user+0x37/0x60
[13656894.814406]  ? memdup_user+0x4b/0x70
[13656894.814408]  ? ksys_mount+0xb6/0xd0
[13656894.814409]  ? __x64_sys_mount+0x21/0x30
[13656894.814413]  ? do_syscall_64+0x5b/0x1b0
[13656894.814416]  ? entry_SYSCALL_64_after_hwframe+0x65/0xca

crash> log | grep -c BUG
298

crash> log | grep 'stall on CPU' | uniq -cf1
     50 [13656938.701422] rcu: INFO: rcu_sched self-detected stall on CPU

[I.6]  The RIP of the lockups is within the module address range and the last reported module unloaded is the dsa_filter_hook module:

crash> log | grep RIP | awk '{print $3}' | uniq -c
    348 0010:0xffffffffc08d2f3c

crash> help -m | grep modules
            modules_vaddr: ffffffffc0000000
              modules_end: ffffffffff000000

crash> log | grep last | awk '{print $NF}' | uniq -c
    298 dsa_filter_hook]

==============================================================================

[ANALYSIS]

The vmcore shows elevated load due to an increased number of blocked tasks [I.1].  Most of those blocked tasks are waiting on IO activity to complete [I.2] with inflight IO to several devices [I.3].  One of the devices showing inflight IO is related to the /orabkp mount [I.4].  If that mount is related to backup operations, did the issue start when a backup procedure was initialized?  Additionally, I recommend engaging with your storage team to confirm the performance of the underlying storage devices.

Outside of the of the IO activity, there are repeated soft lockups and scheduling stalls reported in the logs [I.5].  All of the stalls are reported at the RIP function ffffffffc08d2f3c that appears to be related to an unloaded module [I.6].  The last reported unloaded module is the dsa_filter_hook.  I recommend engaging with the vendor of that module for further investigation into these soft lockups after the module was unloaded.
```



https://access.redhat.com/solutions/37128



How to find out memory usage of a particular process from vmcore?

 Solution Unverified - Updated August 7 2024 at 2:18 PM - 

[English ](https://access.redhat.com/solutions/37128)

## Environment

- Red Hat Enterprise Linux 3,4,5 and 6
- Crash (all versions)

## Issue

- Server was unresponsive and manually crashed to generate vmcore.
- How to find out memory usage of a particular process from the vmcore?
- Host was hung because of OOM, rebooted the host and fixed the issue, let us know the RCA.

## Resolution

- Once the crash command is successfully invoked to analyze vmcore, kmem output should show the overall memory usage (including swap). e.g.:

  [Raw](https://access.redhat.com/solutions/37128#)

  ```
  crash> kmem -i
                PAGES        TOTAL      PERCENTAGE
   TOTAL MEM  4063420      15.5 GB         ----
        FREE     4280      16.7 MB    0% of TOTAL MEM
        USED  4059140      15.5 GB   99% of TOTAL MEM
      SHARED     8810      34.4 MB    0% of TOTAL MEM
     BUFFERS       98       392 KB    0% of TOTAL MEM
      CACHED     3148      12.3 MB    0% of TOTAL MEM
        SLAB    55340     216.2 MB    1% of TOTAL MEM
  TOTAL SWAP  4194971        16 GB         ----
   SWAP USED  4194971        16 GB  100% of TOTAL SWAP
   SWAP FREE        0            0    0% of TOTAL SWAP
  ```

- Output of "ps" command should print the percentage memory utilization by each process. e.g. the following output suggests that the "example" was using most of the memory:

  [Raw](https://access.redhat.com/solutions/37128#)

  ```
   PID    PPID  CPU       TASK        ST  %MEM     VSZ    RSS  COMM
   1393   1364   1     10222872030    RU  15.0 3830040 2520472  example
   1394   1364   0     101d3ddf030    RU  15.6 3830040 2611748  example
   1423   1395   1     102add7f030    RU  11.4 3240216 1906440  example
   1424   1395   4     102ab18e030    RU  14.2 3764504 2384748  example
  29606  29294   7     103f15af7f0    IN   7.5 2795104 1253440  example
  ```

- "ps -p" can be used to find the parent hierarchy. e.g.

  [Raw](https://access.redhat.com/solutions/37128#)

  ```
  crash> ps -p 1393
  PID: 0      TASK: ffffffff803dc180  CPU: 0   COMMAND: "swapper"
   PID: 1      TASK: 10037ea37f0       CPU: 6   COMMAND: "init"
    PID: 21916  TASK: 101fae03030       CPU: 4   COMMAND: "xinetd"
     PID: 1350   TASK: 102472df030       CPU: 0   COMMAND: "auto_example"
      PID: 1364   TASK: 1001f103030       CPU: 0   COMMAND: "sh"
       PID: 1393   TASK: 10222872030       CPU: 1   COMMAND: "example"
  ```

- Following script can also be used to list 10 processes with highest memory consumptions:



iSCSI storage and network objects identification from vmcore

https://access.redhat.com/solutions/3132881
