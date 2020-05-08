---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 850ace7af15ab37ab9a4a124d20ed4588771f4d4
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594371"
---
## <a name="common-scenarios"></a>常见方案
以下方案极大地受益于突发：
- **改善启动时间**–使用突发，实例的启动速度将大大加快。 例如，启用高级版 Vm 的默认 OS 磁盘是 P4 磁盘，其预配性能最高可达 120 IOPS 和 25 MB/秒。 使用突发时，P4 最多可达 3500 IOPS 和 170 MB/秒，允许6倍的启动时间。
- **处理批处理作业**–某些应用程序的工作负荷本质上是循环的，在大多数时间都需要基线性能并且需要较长时间的性能。 这种情况的一个示例是一个会计程序，该程序处理每日需要少量磁盘流量的事务。 然后，在该月结束时，协调需要大量磁盘流量的报表。
- 应对**流量高峰**– Web 服务器及其应用程序随时可以体验流量。 如果你的 web 服务器使用突发方式由 Vm 或磁盘提供支持，则这些服务器可以更好地处理流量高峰。 

## <a name="bursting-flow"></a>突发流
突发信用系统以相同的方式应用到虚拟机级别和磁盘级别。 你的资源（VM 或磁盘）将从 "完全贮存的信用" 开始。 这些额度允许以最大的突发速率传输30分钟。 当资源在其性能磁盘存储限制下运行时，会累计突发信用额度。 对于您的资源使用低于性能限制的所有 IOPS 和 MB/秒，您开始积累信用额度。 如果你的资源具有用于突发的累计信用额度，而你的工作负荷需要额外的性能，则你的资源可以使用这些信用额度超出你的性能限制，为其提供满足要求所需的磁盘 IO 性能。

![突发桶关系图](media/managed-disks-bursting/bucket-diagram.jpg)

有关突发累积的注意事项之一是，每个资源都有不同的原因，因为它基于其性能量下的未使用 IOPS 和 MB/s。 这意味着，基线性能较高的产品可以更快地将其突发量降低到比基线执行更低的产品。 例如，不包含活动的 P1 磁盘置于空闲状态将每秒计为 120 IOPS，而 P20 磁盘将每秒累算 2300 IOPS，同时置于空闲状态无活动。

## <a name="bursting-states"></a>突发状态
有三种启用了突发功能的状态的资源：
- **累积**–资源的 IO 流量小于性能目标。 IOPS 的累积分解信用额度和 MB/秒之间是分开的。 你的资源可能会被计为 IOPS 信用额度和支出，反之亦然。
- **突发**–资源的流量超过性能目标。 突发流量将独立消耗 IOPS 或带宽的积分。
- **常量**–资源的流量与性能目标完全相同。

## <a name="examples-of-bursting"></a>突发的示例
以下示例显示了突发如何处理各种虚拟机和磁盘组合。 为了使示例易于理解，我们将重点放在 MB/秒，但相同的逻辑单独应用于 IOPS。

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>具有可突增磁盘的非可突增虚拟机
**VM 和磁盘组合：** 
- Standard_D8as_v4 
    - 未缓存的 MB/秒：192
- P4 OS 磁盘
    - 预配的 MB/秒：25
    - 最大突发 MB/秒：170 
- 2 P10 数据磁盘 
    - 预配的 MB/秒：25
    - 最大突发 MB/秒：170

 当 VM 启动时，它将从 OS 磁盘中检索数据。 由于 OS 磁盘是开始使用的 VM 的一部分，因此操作系统磁盘将满突发信用额度。 这些信用额度允许 OS 磁盘以 170 MB/秒的速度突然启动，如下所示：

![非突发 vm 突发磁盘启动](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

启动完成后，应用程序将在 VM 上运行，并且具有非关键工作负荷。 此工作负荷需要 15 MB/S，在所有磁盘上均匀分配：

![非突发 vm 暴冲磁盘空闲](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

然后，应用程序需要处理需要 192 MB/s 的批处理作业。 OS 磁盘使用 2 MB/秒，其余部分在数据磁盘之间平均剥离：

![非突发 vm 暴冲磁盘突发](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>可突增包含非可突增磁盘的虚拟机
**VM 和磁盘组合：** 
- Standard_L8s_v2 
    - 未缓存的 MB/秒：160
    - 最大突发 MB/秒：1280
- P50 OS 磁盘
    - 预配的 MB/秒：250 
- 2 P10 数据磁盘 
    - 预配的 MB/秒：250

 首次启动后，应用程序将在 VM 上运行，并且具有非关键工作负荷。 此工作负荷需要 30 MB/s，可在所有磁盘上均匀分配![：突发 vm 非突发磁盘空闲](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

然后，应用程序需要处理需要 600 MB/s 的批处理作业。 Standard_L8s_v2 突发以满足这一需求，然后对磁盘的请求会均匀地分散到 P50 磁盘：

![突发 vm 非突发磁盘突发](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>可突增包含可突增磁盘的虚拟机
**VM 和磁盘组合：** 
- Standard_L8s_v2 
    - 未缓存的 MB/秒：160
    - 最大突发 MB/秒：1280
- P4 OS 磁盘
    - 预配的 MB/秒：25
    - 最大突发 MB/秒：170 
- 2 P4 数据磁盘 
    - 预配的 MB/秒：25
    - 最大突发 MB/秒：170 

VM 启动时，会将其从 OS 磁盘请求突发为 1280 MB/秒的突发限制，操作系统磁盘将响应其突发性能 170 MB/秒：

![突发 vm 突发磁盘启动](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

启动完成后，会在 VM 上运行应用程序。 应用程序具有一个非关键工作负荷，该工作负荷需要 15 MB/s，可在所有磁盘上均匀分配：

![突发 vm 暴冲磁盘空闲](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

然后，应用程序需要处理需要 360 MB/s 的批处理作业。 Standard_L8s_v2 猝发以满足此要求，然后请求。 OS 磁盘只需要 20 MB/s。 剩余 340 MB/秒由突发 P4 数据磁盘处理：  

![突发 vm 暴冲磁盘突发](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)