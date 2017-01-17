# Create an instance

On openstack an instance is a VM.

Before creating a VM, get the following information that will be needed.

## Get image

~~~
$ openstack image list
+--------------------------------------+--------------------------+--------+
| ID                                   | Name                     | Status |
+--------------------------------------+--------------------------+--------+
| 555f8798-d122-47e4-b67f-fc3850b4628d | Ubuntu-14.04-x86_64      | active |
| cbe0a04d-1e12-4cd1-8783-f6a849ebecb5 | cirros-0.3.3-x86_64-disk | active |
+--------------------------------------+--------------------------+--------+
~~~

Image ID: 555f8798-d122-47e4-b67f-fc3850b4628d

## Get Flavor

In Openstack, the flavor defines how big is the VM, to get it:

~~~
$ openstack flavor list
+----+-----------+-------+------+-----------+-------+-----------+
| ID | Name      |   RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+-----------+-------+------+-----------+-------+-----------+
| 1  | m1.tiny   |   512 |    1 |         0 |     1 | True      |
| 2  | m1.small  |  2048 |   20 |         0 |     1 | True      |
| 3  | m1.medium |  4096 |   40 |         0 |     2 | True      |
| 4  | m1.large  |  8192 |   80 |         0 |     4 | True      |
| 5  | m1.xlarge | 16384 |  160 |         0 |     8 | True      |
+----+-----------+-------+------+-----------+-------+-----------+
~~~

Flavor ID: 2

## Creating a keypair

In this case, add a keypair of a previously generated key

~~~
$ openstack keypair create --public-key ~/mykey.pub mykey
+-------------+-------------------------------------------------+
| Field       | Value                                           |
+-------------+-------------------------------------------------+
| fingerprint | bd:35:a4:f3:70:c5:4e:56:31:e5:61:50:8a:6d:7b:bf |
| name        | mykey                                        |
| user_id     | d4e35a07820641278c69617a29b9b92f                |
+-------------+-------------------------------------------------+
~~~

Keypair: mykey


## Getting the private network

~~~ 
$ neutron net-list
+--------------------------------------+--------------------------------------+---------------------------------------+
| id                                   | name                                 | subnets                               |
+--------------------------------------+--------------------------------------+---------------------------------------+
| a985b606-dacb-4561-baf6-d29fb9eb20bb | ext-net                              | ef974aeb-4795-427f-9481-2cfdcfa86dc4  |
|                                      |                                      | 172.16.42.0/24                        |
| 8ebd252c-5488-4600-aba3-2f7b4fcdff48 | paasnet                              | 4d6f2b18-6643-41b2-a71a-c98e204d2b5f  |
|                                      |                                      | 10.0.0.0/24                           |
+--------------------------------------+--------------------------------------+---------------------------------------+
~~~

Private network ID: 8ebd252c-5488-4600-aba3-2f7b4fcdff48


## Creating the VM

Arguments:

Image ID: 555f8798-d122-47e4-b67f-fc3850b4628d

Flavor ID: 2

Keypair: mykey

Private subnet ID: 4d6f2b18-6643-41b2-a71a-c98e204d2b5f

~~~
$ openstack server create --image 555f8798-d122-47e4-b67f-fc3850b4628d --flavor 2 --key-name stackato --nic net-id=8ebd252c-5488-4600-aba3-2f7b4fcdff48 myvm 
+--------------------------------------+------------------------------------------------------------+
| Field                                | Value                                                      |
+--------------------------------------+------------------------------------------------------------+
| OS-DCF:diskConfig                    | MANUAL                                                     |
| OS-EXT-AZ:availability_zone          |                                                            |
| OS-EXT-SRV-ATTR:host                 | None                                                       |
| OS-EXT-SRV-ATTR:hypervisor_hostname  | None                                                       |
| OS-EXT-SRV-ATTR:instance_name        | instance-0000001f                                          |
| OS-EXT-STS:power_state               | NOSTATE                                                    |
| OS-EXT-STS:task_state                | scheduling                                                 |
| OS-EXT-STS:vm_state                  | building                                                   |
| OS-SRV-USG:launched_at               | None                                                       |
| OS-SRV-USG:terminated_at             | None                                                       |
| accessIPv4                           |                                                            |
| accessIPv6                           |                                                            |
| addresses                            |                                                            |
| adminPass                            | 4XP4JUez3EYL                                               |
| config_drive                         |                                                            |
| created                              | 2016-11-28T11:58:38Z                                       |
| flavor                               | m1.small (2)                                               |
| hostId                               |                                                            |
| id                                   | 90a7997e-e0a7-4f94-9c31-307c3d643170                       |
| image                                | Ubuntu-14.04-x86_64 (555f8798-d122-47e4-b67f-fc3850b4628d) |
| key_name                             | stackato                                                   |
| name                                 | myvm                                                       |
| os-extended-volumes:volumes_attached | []                                                         |
| progress                             | 0                                                          |
| project_id                           | 2d423f42d64243e1b6aebb223d10aae9                           |
| properties                           |                                                            |
| security_groups                      | [{u'name': u'default'}]                                    |
| status                               | BUILD                                                      |
| updated                              | 2016-11-28T11:58:39Z                                       |
| user_id                              | d4e35a07820641278c69617a29b9b92f                           |
+--------------------------------------+------------------------------------------------------------+
~~~

## Wait until vm is Active

~~~
$ openstack server list
+--------------------------------------+------+--------+----------+---------------------+
| ID                                   | Name | Status | Networks | Image Name          |
+--------------------------------------+------+--------+----------+---------------------+
| 90a7997e-e0a7-4f94-9c31-307c3d643170 | myvm | BUILD  |          | Ubuntu-14.04-x86_64 |
+--------------------------------------+------+--------+----------+---------------------+
$ openstack server list
+--------------------------------------+------+--------+------------------+---------------------+
| ID                                   | Name | Status | Networks         | Image Name          |
+--------------------------------------+------+--------+------------------+---------------------+
| 90a7997e-e0a7-4f94-9c31-307c3d643170 | myvm | BUILD  | paasnet=10.0.0.6 | Ubuntu-14.04-x86_64 |
+--------------------------------------+------+--------+------------------+---------------------+
$ openstack server list
+--------------------------------------+------+--------+------------------+---------------------+
| ID                                   | Name | Status | Networks         | Image Name          |
+--------------------------------------+------+--------+------------------+---------------------+
| 90a7997e-e0a7-4f94-9c31-307c3d643170 | myvm | ACTIVE | paasnet=10.0.0.6 | Ubuntu-14.04-x86_64 |
+--------------------------------------+------+--------+------------------+---------------------+
~~~

## Check VM console logs

This can help understand problems on VM creation:

~~~
 openstack console log show suse
[    0.000000] Initializing cgroup subsys cpuset
[    0.000000] Initializing cgroup subsys cpu
[    0.000000] Initializing cgroup subsys cpuacct
[    0.000000] Linux version 3.16.7-53-default (geeko@buildhost) (gcc version 4.8.3 20140627 [gcc-4_8-branch revision 212064] (SUSE Linux) ) #1 SMP Fri Dec 2 13:19:28 UTC 2016 (7b4a1f9)
[    0.000000] Command line: BOOT_IMAGE=/boot/linux.vmx console=ttyS0,115200n8 console=tty0 net.ifnames=0 NON_PERSISTENT_DEVICE_NAMES=1 showopts
[    0.000000] e820: BIOS-provided physical RAM map:
[    0.000000] BIOS-e820: [mem 0x0000000000000000-0x000000000009fbff] usable
[    0.000000] BIOS-e820: [mem 0x000000000009fc00-0x000000000009ffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000000f0000-0x00000000000fffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000000100000-0x00000000bffdffff] usable
[    0.000000] BIOS-e820: [mem 0x00000000bffe0000-0x00000000bfffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000feffc000-0x00000000feffffff] reserved
[    0.000000] BIOS-e820: [mem 0x00000000fffc0000-0x00000000ffffffff] reserved
[    0.000000] BIOS-e820: [mem 0x0000000100000000-0x000000013fffffff] usable
[    0.000000] NX (Execute Disable) protection: active
[    0.000000] SMBIOS 2.8 present.
[    0.000000] Hypervisor detected: KVM
[    0.000000] AGP: No AGP bridge found
[    0.000000] e820: last_pfn = 0x140000 max_arch_pfn = 0x400000000
[    0.000000] x86 PAT enabled: cpu 0, old 0x7040600070406, new 0x7010600070106
[    0.000000] e820: last_pfn = 0xbffe0 max_arch_pfn = 0x400000000
[    0.000000] found SMP MP-table at [mem 0x000f0ea0-0x000f0eaf] mapped at [ffff8800000f0ea0]
[    0.000000] Scanning 1 areas for low memory corruption
[    0.000000] Using GB pages for direct mapping
[    0.000000] init_memory_mapping: [mem 0x00000000-0x000fffff]
[    0.000000] init_memory_mapping: [mem 0x13fe00000-0x13fffffff]
[    0.000000] init_memory_mapping: [mem 0x13c000000-0x13fdfffff]
[    0.000000] init_memory_mapping: [mem 0x100000000-0x13bffffff]
[    0.000000] init_memory_mapping: [mem 0x00100000-0xbffdffff]
[    0.000000] RAMDISK: [mem 0x313a6000-0x349cafff]
[    0.000000] ACPI: Early table checksum verification disabled
[    0.000000] ACPI: RSDP 0x00000000000F0C30 000014 (v00 BOCHS )
[    0.000000] ACPI: RSDT 0x00000000BFFE16D9 00002C (v01 BOCHS  BXPCRSDT 00000001 BXPC 00000001)
[    0.000000] ACPI: FACP 0x00000000BFFE15E5 000074 (v01 BOCHS  BXPCFACP 00000001 BXPC 00000001)
[    0.000000] ACPI: DSDT 0x00000000BFFE0040 0015A5 (v01 BOCHS  BXPCDSDT 00000001 BXPC 00000001)
[    0.000000] ACPI: FACS 0x00000000BFFE0000 000040
[    0.000000] ACPI: APIC 0x00000000BFFE1659 000080 (v01 BOCHS  BXPCAPIC 00000001 BXPC 00000001)
[    0.000000] No NUMA configuration found
[    0.000000] Faking a node at [mem 0x0000000000000000-0x000000013fffffff]
[    0.000000] Initmem setup node 0 [mem 0x00000000-0x13fffffff]
[    0.000000]   NODE_DATA [mem 0x13ffea000-0x13fffdfff]
[    0.000000] kvm-clock: Using msrs 4b564d01 and 4b564d00
[    0.000000] kvm-clock: cpu 0, msr 1:3ffe2001, primary cpu clock
[    0.000000] Zone ranges:
[    0.000000]   DMA      [mem 0x00001000-0x00ffffff]
[    0.000000]   DMA32    [mem 0x01000000-0xffffffff]
[    0.000000]   Normal   [mem 0x100000000-0x13fffffff]
[    0.000000] Movable zone start for each node
[    0.000000] Early memory node ranges
[    0.000000]   node   0: [mem 0x00001000-0x0009efff]
[    0.000000]   node   0: [mem 0x00100000-0xbffdffff]
[    0.000000]   node   0: [mem 0x100000000-0x13fffffff]
[    0.000000] ACPI: PM-Timer IO Port: 0x608
[    0.000000] ACPI: LAPIC (acpi_id[0x00] lapic_id[0x00] enabled)
[    0.000000] ACPI: LAPIC (acpi_id[0x01] lapic_id[0x01] enabled)
[    0.000000] ACPI: LAPIC_NMI (acpi_id[0xff] dfl dfl lint[0x1])
[    0.000000] ACPI: IOAPIC (id[0x00] address[0xfec00000] gsi_base[0])
[    0.000000] IOAPIC[0]: apic_id 0, version 17, address 0xfec00000, GSI 0-23
[    0.000000] ACPI: INT_SRC_OVR (bus 0 bus_irq 0 global_irq 2 dfl dfl)
[    0.000000] ACPI: INT_SRC_OVR (bus 0 bus_irq 5 global_irq 5 high level)
[    0.000000] ACPI: INT_SRC_OVR (bus 0 bus_irq 9 global_irq 9 high level)
[    0.000000] ACPI: INT_SRC_OVR (bus 0 bus_irq 10 global_irq 10 high level)
[    0.000000] ACPI: INT_SRC_OVR (bus 0 bus_irq 11 global_irq 11 high level)
[    0.000000] Using ACPI (MADT) for SMP configuration information
[    0.000000] smpboot: Allowing 2 CPUs, 0 hotplug CPUs
[    0.000000] PM: Registered nosave memory: [mem 0x0009f000-0x0009ffff]
[    0.000000] PM: Registered nosave memory: [mem 0x000a0000-0x000effff]
[    0.000000] PM: Registered nosave memory: [mem 0x000f0000-0x000fffff]
[    0.000000] PM: Registered nosave memory: [mem 0xbffe0000-0xbfffffff]
[    0.000000] PM: Registered nosave memory: [mem 0xc0000000-0xfeffbfff]
[    0.000000] PM: Registered nosave memory: [mem 0xfeffc000-0xfeffffff]
[    0.000000] PM: Registered nosave memory: [mem 0xff000000-0xfffbffff]
[    0.000000] PM: Registered nosave memory: [mem 0xfffc0000-0xffffffff]
[    0.000000] e820: [mem 0xc0000000-0xfeffbfff] available for PCI devices
[    0.000000] Booting paravirtualized kernel on KVM
[    0.000000] setup_percpu: NR_CPUS:512 nr_cpumask_bits:512 nr_cpu_ids:2 nr_node_ids:1
[    0.000000] PERCPU: Embedded 27 pages/cpu @ffff88013fc00000 s78656 r8192 d23744 u1048576
[    0.000000] KVM setup async PF for cpu 0
[    0.000000] kvm-stealtime: cpu 0, msr 13fc0c240
[    0.000000] Built 1 zonelists in Node order, mobility grouping on.  Total pages: 1034089
[    0.000000] Policy zone: Normal
[    0.000000] Kernel command line: BOOT_IMAGE=/boot/linux.vmx console=ttyS0,115200n8 console=tty0 net.ifnames=0 NON_PERSISTENT_DEVICE_NAMES=1 showopts
[    0.000000] PID hash table entries: 4096 (order: 3, 32768 bytes)
[    0.000000] xsave: enabled xstate_bv 0x7, cntxt size 0x340
[    0.000000] AGP: Checking aperture...
[    0.000000] AGP: No AGP bridge found
[    0.000000] Memory: 3997876K/4193784K available (5983K kernel code, 966K rwdata, 4548K rodata, 1556K init, 1528K bss, 195908K reserved)
[    0.000000] Hierarchical RCU implementation.
[    0.000000] 	RCU dyntick-idle grace-period acceleration is enabled.
[    0.000000] 	RCU restricting CPUs from NR_CPUS=512 to nr_cpu_ids=2.
[    0.000000] 	Offload RCU callbacks from all CPUs
[    0.000000] 	Offload RCU callbacks from CPUs: 0-1.
[    0.000000] RCU: Adjusting geometry for rcu_fanout_leaf=16, nr_cpu_ids=2
[    0.000000] NR_IRQS:33024 nr_irqs:512 16
[    0.000000] Console: colour dummy device 80x25
[    0.000000] console [tty0] enabled
[    0.000000] console [ttyS0] enabled
[    0.000000] allocated 16777216 bytes of page_cgroup
[    0.000000] please try 'cgroup_disable=memory' option if you don't want memory cgroups
[    0.000000] tsc: Detected 1695.749 MHz processor
[    0.008000] Calibrating delay loop (skipped) preset value.. 3391.49 BogoMIPS (lpj=6782996)
[    0.008000] pid_max: default: 32768 minimum: 301
[    0.008020] ACPI: Core revision 20140424
[    0.012547] ACPI: All ACPI Tables successfully acquired
[    0.014270] Security Framework initialized
[    0.015239] AppArmor: AppArmor initialized
[    0.017083] Dentry cache hash table entries: 524288 (order: 10, 4194304 bytes)
[    0.021557] Inode-cache hash table entries: 262144 (order: 9, 2097152 bytes)
[    0.024648] Mount-cache hash table entries: 8192 (order: 4, 65536 bytes)
[    0.027190] Mountpoint-cache hash table entries: 8192 (order: 4, 65536 bytes)
[    0.028317] Initializing cgroup subsys memory
[    0.029720] Initializing cgroup subsys devices
[    0.030754] Initializing cgroup subsys freezer
[    0.032010] Initializing cgroup subsys net_cls
[    0.033042] Initializing cgroup subsys blkio
[    0.034389] Initializing cgroup subsys perf_event
[    0.036010] Initializing cgroup subsys net_prio
[    0.037070] Initializing cgroup subsys hugetlb
[    0.039711] mce: CPU supports 10 MCE banks
[    0.040066] Last level iTLB entries: 4KB 0, 2MB 0, 4MB 0
[    0.040066] Last level dTLB entries: 4KB 0, 2MB 0, 4MB 0, 1GB 0
[    0.040066] tlb_flushall_shift: 6
[    0.044461] Freeing SMP alternatives memory: 24K (ffffffff81e78000 - ffffffff81e7e000)
[    0.051325] ftrace: allocating 23594 entries in 93 pages
[    0.060324] Enabling x2apic
[    0.061008] Enabled x2apic
[    0.062331] Switched APIC routing to physical x2apic.
[    0.065275] ..TIMER: vector=0x30 apic1=0 pin1=2 apic2=-1 pin2=-1
[    0.066872] smpboot: CPU0: Intel Xeon E312xx (Sandy Bridge) (fam: 06, model: 2a, stepping: 01)
[    0.068936] Performance Events: unsupported p6 CPU model 42 no PMU driver, software events only.
[    0.072938] NMI watchdog: disabled (cpu0): hardware events not enabled
[    0.075773] x86: Booting SMP configuration:
[    0.076008] .... node  #0, CPUs:      #1
[    0.008000] kvm-clock: cpu 1, msr 1:3ffe2041, secondary cpu clock
[    0.092029] x86: Booted up 1 node, 2 CPUs
[    0.092010] KVM setup async PF for cpu 1
[    0.092016] kvm-stealtime: cpu 1, msr 13fd0c240
[    0.095806] smpboot: Total of 2 processors activated (6782.99 BogoMIPS)
[    0.096529] devtmpfs: initialized
[    0.101547] pinctrl core: initialized pinctrl subsystem
[    0.104166] RTC time: 15:35:52, date: 01/13/17
[    0.105305] NET: Registered protocol family 16
[    0.106517] cpuidle: using governor ladder
[    0.107474] cpuidle: using governor menu
[    0.108180] ACPI: bus type PCI registered
[    0.109106] acpiphp: ACPI Hot Plug PCI Controller Driver version: 0.5
[    0.111786] PCI: Using configuration type 1 for base access
[    0.116147] ACPI: Added _OSI(Module Device)
[    0.117098] ACPI: Added _OSI(Processor Device)
[    0.118100] ACPI: Added _OSI(3.0 _SCP Extensions)
[    0.119152] ACPI: Added _OSI(Processor Aggregator Device)
[    0.122408] ACPI: Interpreter enabled
[    0.123264] ACPI Exception: AE_NOT_FOUND, While evaluating Sleep State [\_S1_] (20140424/hwxface-580)
[    0.125223] ACPI Exception: AE_NOT_FOUND, While evaluating Sleep State [\_S2_] (20140424/hwxface-580)
[    0.127338] ACPI: (supports S0 S3 S4 S5)
[    0.128006] ACPI: Using IOAPIC for interrupt routing
[    0.129139] PCI: Using host bridge windows from ACPI; if necessary, use "pci=nocrs" and report a bug
[    0.136576] ACPI: PCI Root Bridge [PCI0] (domain 0000 [bus 00-ff])
[    0.137994] acpi PNP0A03:00: _OSC: OS supports [ASPM ClockPM Segments MSI]
[    0.139569] acpi PNP0A03:00: _OSC failed (AE_NOT_FOUND); disabling ASPM
[    0.140036] acpi PNP0A03:00: fail to add MMCONFIG information, can't access extended PCI configuration space under this bridge.
[    0.143739] acpiphp: Slot [3] registered
[    0.144046] acpiphp: Slot [4] registered
[    0.144974] acpiphp: Slot [5] registered
[    0.145918] acpiphp: Slot [6] registered
[    0.146837] acpiphp: Slot [7] registered
[    0.147753] acpiphp: Slot [8] registered
[    0.148039] acpiphp: Slot [9] registered
[    0.148961] acpiphp: Slot [10] registered
[    0.150547] acpiphp: Slot [11] registered
[    0.151490] acpiphp: Slot [12] registered
[    0.152040] acpiphp: Slot [13] registered
[    0.155486] acpiphp: Slot [14] registered
[    0.156040] acpiphp: Slot [15] registered
[    0.157619] acpiphp: Slot [16] registered
[    0.158553] acpiphp: Slot [17] registered
[    0.159491] acpiphp: Slot [18] registered
[    0.160043] acpiphp: Slot [19] registered
[    0.160980] acpiphp: Slot [20] registered
[    0.161919] acpiphp: Slot [21] registered
[    0.162851] acpiphp: Slot [22] registered
[    0.164045] acpiphp: Slot [23] registered
[    0.164980] acpiphp: Slot [24] registered
[    0.165917] acpiphp: Slot [25] registered
[    0.168043] acpiphp: Slot [26] registered
[    0.168982] acpiphp: Slot [27] registered
[    0.169920] acpiphp: Slot [28] registered
[    0.171199] acpiphp: Slot [29] registered
[    0.172041] acpiphp: Slot [30] registered
[    0.172975] acpiphp: Slot [31] registered
[    0.173892] PCI host bridge to bus 0000:00
[    0.176010] pci_bus 0000:00: root bus resource [bus 00-ff]
[    0.177239] pci_bus 0000:00: root bus resource [io  0x0000-0x0cf7]
[    0.178944] pci_bus 0000:00: root bus resource [io  0x0d00-0xffff]
[    0.180007] pci_bus 0000:00: root bus resource [mem 0x000a0000-0x000bffff]
[    0.181549] pci_bus 0000:00: root bus resource [mem 0xc0000000-0xfebfffff]
[    0.193084] pci 0000:00:01.1: legacy IDE quirk: reg 0x10: [io  0x01f0-0x01f7]
[    0.196008] pci 0000:00:01.1: legacy IDE quirk: reg 0x14: [io  0x03f6]
[    0.199033] pci 0000:00:01.1: legacy IDE quirk: reg 0x18: [io  0x0170-0x0177]
[    0.200299] pci 0000:00:01.1: legacy IDE quirk: reg 0x1c: [io  0x0376]
[    0.211476] pci 0000:00:01.3: quirk: [io  0x0600-0x063f] claimed by PIIX4 ACPI
[    0.212023] pci 0000:00:01.3: quirk: [io  0x0700-0x070f] claimed by PIIX4 SMB
[    0.273890] ACPI: PCI Interrupt Link [LNKA] (IRQs 5 *10 11)
[    0.276087] ACPI: PCI Interrupt Link [LNKB] (IRQs 5 *10 11)
[    0.277679] ACPI: PCI Interrupt Link [LNKC] (IRQs 5 10 *11)
[    0.279925] ACPI: PCI Interrupt Link [LNKD] (IRQs 5 10 *11)
[    0.280467] ACPI: PCI Interrupt Link [LNKS] (IRQs *9)
[    0.282987] ACPI: Enabled 16 GPEs in block 00 to 0F
[    0.284164] vgaarb: setting as boot device: PCI:0000:00:02.0
[    0.286010] vgaarb: device added: PCI:0000:00:02.0,decodes=io+mem,owns=io+mem,locks=none
[    0.288009] vgaarb: loaded
[    0.289296] vgaarb: bridge control possible 0000:00:02.0
[    0.290895] SCSI subsystem initialized
[    0.292182] PCI: Using ACPI for IRQ routing
[    0.293716] NetLabel: Initializing
[    0.295060] NetLabel:  domain hash size = 128
[    0.296008] NetLabel:  protocols = UNLABELED CIPSOv4
[    0.297715] NetLabel:  unlabeled traffic allowed by default
[    0.299135] Switched to clocksource kvm-clock
[    0.311363] AppArmor: AppArmor Filesystem Enabled
[    0.312651] pnp: PnP ACPI init
[    0.313380] ACPI: bus type PNP registered
[    0.315923] pnp: PnP ACPI: found 6 devices
[    0.316864] ACPI: bus type PNP unregistered
[    0.325955] NET: Registered protocol family 2
[    0.327321] TCP established hash table entries: 32768 (order: 6, 262144 bytes)
[    0.329056] TCP bind hash table entries: 32768 (order: 7, 524288 bytes)
[    0.331229] TCP: Hash tables configured (established 32768 bind 32768)
[    0.332740] TCP: reno registered
[    0.333492] UDP hash table entries: 2048 (order: 4, 65536 bytes)
[    0.335256] UDP-Lite hash table entries: 2048 (order: 4, 65536 bytes)
[    0.336842] NET: Registered protocol family 1
[    0.337862] pci 0000:00:00.0: Limiting direct PCI/PCI transfers
[    0.339520] pci 0000:00:01.0: PIIX3: Enabling Passive Release
[    0.340851] pci 0000:00:01.0: Activating ISA DMA hang workarounds
[    0.398982] ACPI: PCI Interrupt Link [LNKD] enabled at IRQ 11
[    0.456533] Unpacking initramfs...
[    2.085451] Freeing initrd memory: 55444K (ffff8800313a6000 - ffff8800349cb000)
[    2.087961] PCI-DMA: Using software bounce buffering for IO (SWIOTLB)
[    2.089403] software IO TLB [mem 0xbbfe0000-0xbffe0000] (64MB) mapped at [ffff8800bbfe0000-ffff8800bffdffff]
[    2.093465] microcode: CPU0 sig=0x206a1, pf=0x1, revision=0x1
[    2.095501] microcode: CPU1 sig=0x206a1, pf=0x1, revision=0x1
[    2.096973] microcode: Microcode Update Driver: v2.00 <tigran@aivazian.fsnet.co.uk>, Peter Oruba
[    2.100249] Scanning for low memory corruption every 60 seconds
[    2.102656] futex hash table entries: 512 (order: 3, 32768 bytes)
[    2.104121] audit: initializing netlink subsys (disabled)
[    2.105372] audit: type=2000 audit(1484321754.393:1): initialized
[    2.107621] HugeTLB registered 2 MB page size, pre-allocated 0 pages
[    2.109150] zbud: loaded
[    2.111727] VFS: Disk quotas dquot_6.5.2
[    2.112726] Dquot-cache hash table entries: 512 (order 0, 4096 bytes)
[    2.114881] msgmni has been set to 7916
[    2.115885] Key type big_key registered
[    2.117274] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    2.119086] io scheduler noop registered
[    2.119984] io scheduler deadline registered
[    2.121055] io scheduler cfq registered (default)
[    2.122989] pci_hotplug: PCI Hot Plug PCI Core version: 0.5
[    2.124277] pciehp: PCI Express Hot Plug Controller Driver version: 0.4
[    2.125827] vesafb: mode is 640x480x24, linelength=1920, pages=0
[    2.127192] vesafb: scrolling: redraw
[    2.128031] vesafb: Truecolor: size=0:8:8:8, shift=0:16:8:0
[    2.129284] vesafb: framebuffer at 0xfc000000, mapped to 0xffffc90000780000, using 960k, total 960k
[    2.133401] Console: switching to colour frame buffer device 80x30
[    2.136685] fb0: VESA VGA frame buffer device
[    2.137808] GHES: HEST is not enabled!
[    2.139192] Serial: 8250/16550 driver, 32 ports, IRQ sharing disabled
[    2.168397] 00:04: ttyS0 at I/O 0x3f8 (irq = 4, base_baud = 115200) is a 16550A
[    2.200025] 00:05: ttyS1 at I/O 0x2f8 (irq = 3, base_baud = 115200) is a 16550A
[    2.207136] Non-volatile memory driver v1.3
[    2.209127] Linux agpgart interface v0.103
[    2.211363] i8042: PNP: PS/2 Controller [PNP0303:KBD,PNP0f13:MOU] at 0x60,0x64 irq 1,12
[    2.216202] serio: i8042 KBD port at 0x60,0x64 irq 1
[    2.219108] serio: i8042 AUX port at 0x60,0x64 irq 12
[    2.221760] mousedev: PS/2 mouse device common for all mice
[    2.225146] input: AT Translated Set 2 keyboard as /devices/platform/i8042/serio0/input/input0
[    2.231174] rtc_cmos 00:00: RTC can wake from S4
[    2.234528] rtc_cmos 00:00: rtc core: registered rtc_cmos as rtc0
[    2.237065] rtc_cmos 00:00: alarms up to one day, y3k, 114 bytes nvram
[    2.239949] ledtrig-cpu: registered to indicate activity on CPUs
[    2.242420] hidraw: raw HID events driver (C) Jiri Kosina
[    2.244780] TCP: cubic registered
[    2.246698] NET: Registered protocol family 10
[    2.249598] registered taskstats version 1
[    2.252607] AppArmor: AppArmor sha1 policy hashing enabled
[    2.256044]   Magic number: 1:565:588
[    2.258158] rtc_cmos 00:00: setting system clock to 2017-01-13 15:35:54 UTC (1484321754)
[    2.263510] Freeing unused kernel memory: 1556K (ffffffff81cf3000 - ffffffff81e78000)
[    2.268235] Write protecting the kernel read-only data: 12288k
[    2.272248] Freeing unused kernel memory: 148K (ffff8800015db000 - ffff880001600000)
[    2.282055] Freeing unused kernel memory: 1596K (ffff880001a71000 - ffff880001c00000)
[    2.605393] systemd-udevd[197]: starting version 210
[    2.609494] systemd-udevd[197]: Network interface NamePolicy= disabled on kernel commandline, ignoring.
[    2.615461] random: systemd-udevd urandom read with 3 bits of entropy available
[    2.651087] input: Power Button as /devices/LNXSYSTM:00/LNXPWRBN:00/input/input3
[    2.687941] ACPI: PCI Interrupt Link [LNKC] enabled at IRQ 10
[    2.756086] ACPI: Power Button [PWRF]
[    2.801713] ACPI: PCI Interrupt Link [LNKA] enabled at IRQ 10
[    2.816238] [drm] Initialized drm 1.1.0 20060810
[    2.826291]  vda: vda1
[    2.828877] scsi0 : ata_piix
[    2.833068] scsi1 : ata_piix
[    2.835504] ata1: PATA max MWDMA2 cmd 0x1f0 ctl 0x3f6 bmdma 0xc0a0 irq 14
[    2.838495] ata2: PATA max MWDMA2 cmd 0x170 ctl 0x376 bmdma 0xc0a8 irq 15
[    2.842211] fb: switching to cirrusdrmfb from VESA VGA
[    2.842513] systemd-udevd[271]: failed to execute '/usr/bin/systemd-detect-virt' '/usr/bin/systemd-detect-virt': No such file or directory
[    2.850175] Console: switching to colour dummy device 80x25
[    2.852993] [TTM] Zone  kernel: Available graphics memory: 2028322 kiB
[    2.855162] [TTM] Initializing pool allocator
[    2.856278] [TTM] Initializing DMA pool allocator
[    2.858108] [drm] fb mappable at 0xFC000000
[    2.859462] [drm] vram aper at 0xFC000000
[    2.860485] [drm] size 4194304
[    2.861255] [drm] fb depth is 24
[    2.862094] [drm]    pitch is 3072
[    2.863062] fbcon: cirrusdrmfb (fb0) is primary device
[    2.864895] Console: switching to colour frame buffer device 128x48
[    2.889556] cirrus 0000:00:02.0: fb0: cirrusdrmfb frame buffer device
[    2.892616] cirrus 0000:00:02.0: registered panic notifier
[    2.904075] [drm] Initialized cirrus 1.0.0 20110418 for 0000:00:02.0 on minor 0
[    3.000988] systemd-udevd[197]: Network interface NamePolicy= disabled on kernel commandline, ignoring.
[    3.096108] tsc: Refined TSC clocksource calibration: 1695.696 MHz
%G[    3.222069] device-mapper: uevent: version 1.0.3
[    3.223605] device-mapper: ioctl: 4.27.0-ioctl (2013-10-30) initialised: dm-devel@redhat.com
[    3.237692] loop: module loaded
[    3.242707] squashfs: version 4.0 (2009/01/31) Phillip Lougher
[    3.277672] input: ImExPS/2 Generic Explorer Mouse as /devices/platform/i8042/serio1/input/input2
[    0.895585] Searching for boot device...
[    3.491132] st: Version 20101219, fixed bufsize 32768, s/g segs 256
[    1.084083] Found boot device: /dev/vda
[    1.142100] Filesystem of VMX system is: ext4 -> /dev/vda1
[    4.713505] EXT4-fs (vda1): mounted filesystem with ordered data mode. Opts: (null)
/dev/vda1: UUID="c02a05a1-c257-4c9c-97b6-df2e17af4c66" TYPE="ext4" PARTUUID="2a2b0bc9-01" 
[    2.798545] Activating Image: [/dev/vda1]
[    2.892058] Preparing preinit phase...

[    2.940618] Booting System: 
[    0.061196] Calling pre-init stage in system image
[    6.396660] systemd-udevd[902]: starting version 210
[    6.490913] systemd-udevd[902]: Network interface NamePolicy= disabled on kernel commandline, ignoring.
[   22.651839] Creating dracut based initrd
[   31.778538] random: nonblocking pool is initialized
[   47.441678] device-mapper: core: cleaned up
[   47.449973] systemd-udevd[902]: Network interface NamePolicy= disabled on kernel commandline, ignoring.
[   47.502129] device-mapper: uevent: version 1.0.3
[   47.503462] device-mapper: ioctl: 4.27.0-ioctl (2013-10-30) initialised: dm-devel@redhat.com
[   48.150995] fuse init (API version 7.23)
[   62.107460] writing new MBR ID to master boot record: 0x20110571
[   68.687373] systemd[1]: systemd 210 running in system mode. (+PAM -LIBWRAP +AUDIT +SELINUX -IMA +SYSVINIT +LIBCRYPTSETUP +GCRYPT +ACL +XZ +SECCOMP +APPARMOR)
[   68.692853] systemd[1]: Detected virtualization 'kvm'.
[   68.694118] systemd[1]: Detected architecture 'x86-64'.
[   68.695662] systemd[1]: Set hostname to <linux>.
[   68.991969] systemd[1]: Starting Remote File Systems (Pre).
[   68.994057] systemd[1]: Reached target Remote File Systems (Pre).
[   68.995593] systemd[1]: Starting Remote File Systems.
[   68.998870] systemd[1]: Reached target Remote File Systems.
[   69.001866] systemd[1]: Starting Arbitrary Executable File Formats File System Automount Point.
[   69.005387] systemd[1]: Set up automount Arbitrary Executable File Formats File System Automount Point.
[   69.013004] systemd[1]: Starting Dispatch Password Requests to Console Directory Watch.
[   69.017397] systemd[1]: Started Dispatch Password Requests to Console Directory Watch.
[   69.023141] systemd[1]: Starting Paths.
[   69.030706] systemd[1]: Reached target Paths.
[   69.035556] systemd[1]: Starting Swap.
[   69.041832] systemd[1]: Reached target Swap.
[   69.049016] systemd[1]: Starting Root Slice.
[   69.065878] systemd[1]: Created slice Root Slice.
[   69.070026] systemd[1]: Starting Journal Socket.
[   69.077245] systemd[1]: Listening on Journal Socket.
[   69.086818] systemd[1]: Starting LVM2 metadata daemon socket.
[   69.097319] systemd[1]: Listening on LVM2 metadata daemon socket.
[   69.100848] systemd[1]: Starting Delayed Shutdown Socket.
[   69.107672] systemd[1]: Listening on Delayed Shutdown Socket.
[   69.111694] systemd[1]: Starting /dev/initctl Compatibility Named Pipe.
[   69.120098] systemd[1]: Listening on /dev/initctl Compatibility Named Pipe.
[   69.123567] systemd[1]: Starting udev Kernel Socket.
[   69.130346] systemd[1]: Listening on udev Kernel Socket.
[   69.133461] systemd[1]: Starting udev Control Socket.
[   69.139641] systemd[1]: Listening on udev Control Socket.
[   69.151718] systemd[1]: Starting Syslog Socket.
[   69.162355] systemd[1]: Listening on Syslog Socket.
[   69.171485] systemd[1]: Starting User and Session Slice.
[   69.186966] systemd[1]: Created slice User and Session Slice.
[   69.193077] systemd[1]: Starting System Slice.
[   69.198985] systemd[1]: Created slice System Slice.
[   69.204805] systemd[1]: Starting system-serial\x2dgetty.slice.
[   69.210746] systemd[1]: Created slice system-serial\x2dgetty.slice.
[   69.220707] systemd[1]: Starting system-getty.slice.
[   69.230383] systemd[1]: Created slice system-getty.slice.
[   69.233681] systemd[1]: Mounting POSIX Message Queue File System...
[   69.244846] systemd[1]: Starting Journal Service...
[   69.252684] systemd[1]: Starting Create list of required static device nodes for the current kernel...
[   69.261641] systemd[1]: Mounting Huge Pages File System...
[   69.268565] systemd[1]: Starting Rule generator for /dev/root symlink...
[   69.279475] systemd[1]: Started Collect Read-Ahead Data.
[   69.290178] systemd[1]: Started Replay Read-Ahead Data.
[   69.293950] systemd[1]: Starting Setup Virtual Console...
[   69.305910] systemd[1]: Starting Load Kernel Modules...
[   69.319426] systemd[1]: Started Set Up Additional Binary Formats.
[   69.325668] systemd[1]: Started File System Check on Root Device.
[   69.329569] systemd[1]: Starting Remount Root and Kernel File Systems...
[   69.387017] systemd[1]: Starting Slices.
[   69.402608] systemd[1]: Reached target Slices.
[   69.426063] EXT4-fs (vda1): re-mounted. Opts: (null)
[   69.434693] systemd[1]: Mounted Huge Pages File System.
[   69.445955] systemd[1]: Mounted POSIX Message Queue File System.
[   69.462163] systemd[1]: Started Journal Service.
[   69.666901] input: PC Speaker as /devices/platform/pcspkr/input/input4
[   69.682610] piix4_smbus 0000:00:01.3: SMBus Host Controller at 0x700, revision 0
[   69.686524] ACPI: bus type USB registered
[   69.689334] usbcore: registered new interface driver usbfs
[   69.693384] usbcore: registered new interface driver hub
[   69.706905] AVX version of gcm_enc/dec engaged.
[   69.707808] usbcore: registered new device driver usb
[   69.713764] ehci_hcd: USB 2.0 'Enhanced' Host Controller (EHCI) Driver
[   69.718668] uhci_hcd: USB Universal Host Controller Interface driver
[   69.798284] uhci_hcd 0000:00:01.2: UHCI Host Controller
[   69.806379] uhci_hcd 0000:00:01.2: new USB bus registered, assigned bus number 1
[   69.819314] uhci_hcd 0000:00:01.2: detected 2 ports
[   69.822543] uhci_hcd 0000:00:01.2: irq 11, io base 0x0000c040
[   69.831229] usb usb1: New USB device found, idVendor=1d6b, idProduct=0001
[   69.839995] usb usb1: New USB device strings: Mfr=3, Product=2, SerialNumber=1
[   69.840023] usb usb1: Product: UHCI Host Controller
[   69.840036] usb usb1: Manufacturer: Linux 3.16.7-53-default uhci_hcd
[   69.840323] usb usb1: SerialNumber: 0000:00:01.2
[   69.842624] FDC 0 is a S82078B
[   69.868633] hub 1-0:1.0: USB hub found
[   69.868644] hub 1-0:1.0: 2 ports detected
[   69.966073] ppdev: user-space parallel port driver
[   70.102552] systemd-journald[6468]: Received request to flush runtime journal from PID 1
[   70.224029] usb 1-1: new full-speed USB device number 2 using uhci_hcd
[   70.398939] usb 1-1: New USB device found, idVendor=0627, idProduct=0001
[   70.404571] usb 1-1: New USB device strings: Mfr=1, Product=3, SerialNumber=5
[   70.407776] usb 1-1: Product: QEMU USB Tablet
[   70.411320] usb 1-1: Manufacturer: QEMU
[   70.417871] usb 1-1: SerialNumber: 42
[   70.450193] usbcore: registered new interface driver usbhid
[   70.454087] usbhid: USB HID core driver
[   70.459150] input: QEMU QEMU USB Tablet as /devices/pci0000:00/0000:00:01.2/usb1/1-1/1-1:1.0/0003:0627:0001.0001/input/input5
[   70.467363] hid-generic 0003:0627:0001.0001: input,hidraw0: USB HID v0.01 Pointer [QEMU QEMU USB Tablet] on usb-0000:00:01.2-1/input0

Welcome to openSUSE 13.2 "Harlequin" - Kernel 3.16.7-53-default (ttyS0).

~~~
