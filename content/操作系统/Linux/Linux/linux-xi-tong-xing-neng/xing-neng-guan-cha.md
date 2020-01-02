# 性能观察

![](../../.gitbook/assets/linux_observability_tools.png)

| lsof、perf、strace、pidstat、iostat、iotop、blktrace、swapon |
| :--- |


| top、ps、free、netstat |
| :--- |


| ss、netstat、tcpdump、iptraf、ethtool、ip |
| :--- |


| **Applications** |  |  |
| :--- | :--- | :--- |
| **System Libraries** | ltrac | perf |
| ~~**System Call Interface**~~ | ~~strace、sysdig~~ | ~~perf~~ |
| **VFS：**lsof、pcstat | **Sockets：**ss、netstat | _**Scheduler：**_ |
| **File Systems：** | **TCP/UDP：**netstat、iptraf | _**perf、mpstat、top、ps、pidstat**_ |
| **VM：** | **IP：**netstat、iptraf | ~~**Virtual Memory：**~~ |
| **BDI：**iostat、iotop、blktrace | **Ethernet：**netstat、iptraf、tcpdump | ~~**top、ps、pidstat、vmstat、slabtop、free**~~ |
| **Device Drivers** |  |  |
|  | I/O Bridge |  |
| I/O Cintroller：swapon、iostat、iptop、blktrace |  | Network Controller：ethtool、ip、nicstat、netstat、snmpget、lldptool |

