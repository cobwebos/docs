---
title: "监视 StorSimple 设备 | Microsoft Docs"
description: "介绍了如何使用 StorSimple Manager 服务监视 I/O 性能、容量使用量、网络吞吐量和设备性能。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: bd4f7704-4f6f-47d0-927a-b1c91eabc453
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/16/2016
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d45bb37c8417785db0ea38be4375a998b6d9f109
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016

---
# <a name="use-the-storsimple-manager-service-to-monitor-your-storsimple-device"></a>使用 StorSimple Manager 服务监视 StorSimple 设备
## <a name="overview"></a>概述
可以使用 StorSimple Manager 服务监视 StorSimple 解决方案中的特定设备。 可以基于 I/O 性能、容量使用量、网络吞吐量和设备性能指标创建自定义图表。 

若要查看特定设备的监视信息，请在 Azure 经典门户中选择 StorSimple Manager 服务。 单击“监视”选项卡，然后从设备列表中进行选择。 “监视”页包含以下信息。

## <a name="io-performance"></a>I/O 性能
“I/O 性能”跟踪与在下列对象之间发生的读取和写入操作的数目相关的指标：主机服务器上的 iSCSI 发起程序接口与设备之间，或者设备与云之间。 可以针对特定的卷、特定的卷容器或者所有卷容器测量此性能。

下面的图表针对某个生产设备的所有卷显示了从发起程序到设备的 I/O。 绘制的指标包括每秒读取和写入字节数、每秒读取和写入 IO 操作数，以及读取和写入延迟。

![从发起程序到设备的 I/O 性能](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_InitiatorTODevice_For_AllVolumesM.png)

对于同一设备，针对所有卷容器为从设备到云的数据绘制了 I/O 操作数。 在此设备上，数据仅在线性层中，未扩散到云。 没有发生从设备到云的任何读取写入操作。 因此，图表中的各个峰值之间相隔 5 分钟，这对应于在设备与服务之间检查检测信号的频率。 

![从设备到云的 I/O 性能](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainersM.png)

对于同一设备，从下午 2:00 开始为卷数据创建了云快照。 这导致数据从设备流向云。 在此持续时间内，发生了到云的读取写入。 IO 图表中的各种指标中显示了一个峰值，这对应于创建快照的时间。 

![在创建云快照后从设备到云的 IO 性能](./media/storsimple-monitor-device/StorSimple_IO_Performance_For_DeviceTOCloud_For_AllVolumeContainers2M.png)

## <a name="capacity-utilization"></a>容量使用量
“容量使用量”跟踪与卷、卷容器或设备使用的数据存储空间量相关的指标。 可以基于主存储、云存储或设备存储的容量使用量创建报表。 可以针对特定的卷、特定的卷容器或者所有卷容器测量容量使用量。

主存储、云存储和设备存储容量可以描述如下：

### <a name="primary-storage-capacity-utilization"></a>主存储容量使用量
这些图表显示了在删除重复数据项并压缩数据之前写入到 StorSimple 卷的数据量。 可以按所有卷或者针对单个卷查看主存储使用量。

查看针对所有卷的主存储卷容量使用量图表与查看针对各个个体卷的图表时，将主数据量相加，这两种情况下的两个总和数字可能不匹配。 所有卷上的主数据总量可能不等于各个个体卷的主数据量的总和。 这可能是由以下原因之一造成的：

* **针对所有卷包括了快照数据**：只有当运行的版本低于 Update 3 时才存在此行为。 针对所有卷显示的主数据是每个卷的主数据以及快照数据的和。 针对特定卷显示的主数据仅对应于在卷上分配的数据量（不包括对应的卷快照数据）。
  
    这还可以通过以下公式进行解释：
  
    *主数据（所有卷）=（主数据（卷 i）+ 快照数据的大小（卷 i））的和*
  
    *其中，主数据（卷 i）=分配到卷 i 的主数据的大小*
  
    如果通过该服务删除快照，则删除是在后台异步执行的。 在删除快照后，可能需要过些时间才会更新卷数据大小。 
  
    如果运行的是 Update 3 或更高版本，则卷数据中不包括快照数据。 主使用量的计算方式如下：
  
    *主数据（所有卷）=（主数据（卷 i））的和
  
    *其中，主数据（卷 i）=分配到卷 i 的主数据的大小*
* **所有卷中包括了禁用了监视的卷**：如果你的设备上存在关闭了监视的卷，则这些个体卷的监视数据将不会包括在图表中。 但是，图表中针对所有卷的数据包括关闭了监视的卷。 
* **针对所有卷包括了具有活动的关联备份的已删除卷**：如果删除了包含快照数据的卷但是关联的快照仍然存在，则可能会存在不匹配。
* **针对所有卷包括了已删除的卷**：在某些情况下，即使删除了旧卷，它们也可能仍然存在。 删除没有起作用并且设备可能显示较低的可用容量。 你需要联系 Microsoft 支持部门来删除这些卷。

下面的图表显示了在创建云快照之前和之后，某个 StorSimple 设备的主存储容量使用量。 因为这只是卷数据，所以云快照不应当改变主存储。 如你所见，在创建云快照后，图表在主存储使用量方面没有显示任何差异。 云快照大约在下午 2:00 在该设备上开始。

![创建云快照前的主容量使用量](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes2M.png)

![创建云快照后的主容量使用量](./media/storsimple-monitor-device/StorSimple_PrimaryCapacityUtil_For_AllVolumes1M.png)

如果运行的是 Update 2 或更高版本，则可以按个体卷、所有卷、所有分层卷和所有本地固定卷对主存储容量使用量进行细分，如下所示。 通过按所有本地固定卷进行细分，可以快速确定使用了本地层的多少容量。

![所有本地固定卷的主容量使用量](./media/storsimple-monitor-device/localvolumes.png)

### <a name="cloud-storage-capacity-utilization"></a>云存储容量使用量
这些图表显示使用的云存储量。 此数据已去除了重复数据项并经过压缩。 此数据量包括可能包含未在任何主卷中反映出来并且为旧的或必需的保留用途而保留的数据的云快照。 可以将主存储消耗图表与云存储消耗图表进行比较来获得数据缩减率，虽然该数字不是特别确切。 下面的图表显示了在创建云快照之前和之后，某个 StorSimple 设备的云存储容量使用量。 云快照大约在下午 2:00 在该设备上开始，你可以看到云容量使用量在同一时间迅速上升，从 5.73 MB 增加到了 4.04 GB。

![创建云快照前的云容量使用量](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers2M.png)

![创建云快照后的云容量使用量](./media/storsimple-monitor-device/StorSimple_CloudCapacityUtil_For_AllVolumeContainers1M.png)

### <a name="device-storage-capacity-utilization"></a>设备存储容量使用量
这些图表显示设备的总使用量，这将大于主存储使用量，因为它包括 SSD 线性层。 该层包含也存在于设备的其他层上的许多数据。 SSD 线性层中的容量是循环利用的，以便新数据可以进来，并且旧数据将移动到 HDD 层（彼时将删除重复数据项并对数据进行压缩），随后将移动到云。

随着时间的推移，主容量使用量和设备容量使用量很可能会一起增加，直到数据开始分层存储到云。 那时，设备容量使用量将可能开始达到平稳状态，但主容量使用量将随着更多数据的写入而增加。

下面的图表显示了在创建云快照之前和之后，某个 StorSimple 设备的主存储容量使用量。 云快照在下午 2:00 开始，并且设备容量使用量在那时开始下降。 设备容量使用量从 11.58 GB 下降到了 7.48 GB。 这表明很可能从线性 SSD 层中的未压缩数据中删除了重复数据项，对数据进行了压缩并将其移动到了 HDD 层。 注意，如果设备在 SSD 和 HDD 层中都已经有大量数据，则可能不会看到此下降。 在此示例中，设备具有少量数据。

![创建云快照前的设备容量使用量](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil2M.png)

![创建云快照后的设备容量使用量](./media/storsimple-monitor-device/StorSimple_DeviceCapacityUtil1M.png)

## <a name="network-throughput"></a>网络吞吐量
“网络吞吐量”跟踪从主机服务器和设备上的 iSCSI 发起程序网络接口传输的数据量以及在设备与云之间传输的数据量。 可以针对设备上的每个 iSCSI 网络接口监视此指标。

下面的图表显示了 Data 0 和 Data 4（设备上的两个 1 GbE 网络接口）的网络吞吐量。 在本例中，Data 0 已启用云，而 Data 4 已启用 iSCSI。 还可以查看 StorSimple 设备的入站和出站流量。 图表中从下午 3:24 开始的平坦线条归因于我们仅每隔 5 分钟就收集数据的事实，并且应当忽略。 

![Data4 的网络吞吐量](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data0M.png)

![Data4 的网络吞吐量](./media/storsimple-monitor-device/StorSimple_NetworkThroughput_Data4M.png)

## <a name="device-performance"></a>设备性能
“设备性能”跟踪与设备的性能相关的指标。 下面的图表显示了用于生产的某个设备的 CPU 使用率统计信息。

![设备的 CPU 使用率](./media/storsimple-monitor-device/StorSimple_DeviceMonitor_DevicePerformance1M.png)

## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple Manager 服务设备仪表板](storsimple-device-dashboard.md)。
* 了解如何[使用 StorSimple Manager 服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。


