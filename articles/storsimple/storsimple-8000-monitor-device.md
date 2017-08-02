---
title: "监视 StorSimple 8000 系列设备 | Microsoft Docs"
description: "介绍了如何使用 StorSimple 设备管理器服务监视使用情况、I/O 性能和容量使用情况。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/15/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 337da13d4448090f320fa53a789d97f9843804d3
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017


---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>使用 StorSimple 设备管理器服务监视 StorSimple 设备
## <a name="overview"></a>概述
可以使用 StorSimple 设备管理器服务监视 StorSimple 解决方案中的特定设备。 可以基于 I/O 性能、容量使用情况、网络吞吐量和设备性能指标创建自定义图表并将它们固定到仪表板。 有关详细信息，请转到[自定义门户仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards.md)。

若要查看特定设备的监视信息，请在 Azure 门户中选择 StorSimple 设备管理器服务。 从设备列表中，选择设备并转到“监视”。 然后，可以看到所选设备的“容量”、“使用情况”和“性能”图表。

## <a name="capacity"></a>容量
“容量”跟踪设备上的预配空间和剩余空间。 剩余容量然后显示为本地固定的或分层的。

预配容量和剩余容量按分层卷和本地固定卷进一步细分。 对于每个卷，都将显示设备上的预配容量和剩余容量。

![IO 容量](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>使用情况
“使用情况”跟踪与卷、卷容器或设备使用的数据存储空间量相关的指标。 可以基于主存储、云存储或设备存储的容量使用量创建报表。 可以针对特定的卷、特定的卷容器或者所有卷容器测量容量使用量。
默认情况下，将报告过去 24 小时的使用情况。 可以编辑图表来更改要报告使用情况的持续时间，可以从以下项中进行选择：
* 过去 24 小时
* 过去 7 天
* 过去 30 天
* 过去 90 天
* 过去一年


使用的主存储、云存储和本地存储可以描述如下：

### <a name="primary-storage-usage"></a>主存储使用情况
这些图表显示了在删除重复数据项并压缩数据之前写入到 StorSimple 卷的数据量。 可以查看某个卷容器内的所有卷或单个卷使用的主存储。 使用的主存储进一步按使用的主分层存储和使用的主本地固定存储进行细分。

下面的图表显示了在创建云快照之前和之后，某个 StorSimple 设备使用的主存储。 因为这只是卷数据，所以云快照不应当改变主存储。 如你所见，在创建云快照后，在使用的主分层存储或本地固定存储方面，图表没有显示任何差异。 云快照大约在晚上 11:50 在该设备上开始。

![创建云快照后的主容量使用量](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

如果现在在连接到 StorSimple 设备的主机上运行 IO，则会看到使用的主分层存储或主本地固定存储增长，具体取决于将数据写入到哪些卷（分层卷还是本地固定卷）。 下面是 StorSimple 设备的主存储使用情况图表。 在此设备上，StorSimple 主机大约在下午 2:30 开始在设备上的分层卷上提供写入服务。 可以看到与在主机上运行的 IO 对应的以字节数/秒为单位的写入峰值。

![在分层卷上运行 IO 时的性能](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

如果查看使用的主分层存储，该数量已经上升，而主本地固定存储使用量则保持不变，因为没有向设备上的本地固定卷进行写入。

![在分层卷上运行 IO 时的主容量使用量](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

如果运行的是 Update 3 或更高版本，则可以按个体卷、所有卷、所有分层卷和所有本地固定卷对主存储容量使用量进行细分，如下所示。 通过按所有本地固定卷进行细分，可以快速确定使用了本地层的多少容量。

![所有分层卷的主容量使用量](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![所有本地固定卷的主容量使用量](./media/storsimple-8000-monitor-device/monitor-usage4.png)

可以进一步单击列表中的每个卷并查看对应的使用量。

![所有本地固定卷的主容量使用量](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>云存储使用情况
这些图表显示使用的云存储量。 此数据已去除了重复数据项并经过压缩。 此数据量包括可能包含未在任何主卷中反映出来并且为旧的或必需的保留用途而保留的数据的云快照。 可以将主存储消耗图表与云存储消耗图表进行比较来获得数据缩减率，虽然该数字不是特别确切。

下面的图表显示了在创建云快照时某个 StorSimple 设备的云存储使用量。

* 云快照大约在上午 11:50 在该设备上开始，可以看到，在创建云快照之前，未使用云存储。 
* 在云快照完成之后，云存储使用量上升了 0.89 GB。 
* 当云快照正在进行时，在从设备到云的 IO 中也有一个对应的峰值。

    ![创建云快照前的云存储使用量](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![创建云快照后的云存储使用量](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![在创建云快照期间从设备到云的 IO](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>本地存储使用情况
这些图表显示设备的总使用量，这将大于主存储使用量，因为它包括 SSD 线性层。 该层包含也存在于设备的其他层上的许多数据。 SSD 线性层中的容量是循环利用的，以便新数据可以进来，并且旧数据将移动到 HDD 层（彼时将删除重复数据项并对数据进行压缩），随后将移动到云。

随着时间的推移，使用的主存储和本地存储很可能会一起增加，直到数据开始分层存储到云。 那时，使用的本地存储将可能开始达到平稳状态，但使用的主存储将随着更多数据的写入而增加。

下面的图表显示了在创建云快照时某个 StorSimple 设备使用的主存储。 云快照在上午 11:50 开始，使用的本地存储在那时开始下降。 使用的本地存储从 1.445 GB 下降到了 1.09 GB。 这表明很可能从线性 SSD 层中的未压缩数据中删除了重复数据项，对数据进行了压缩并将其移动到了 HDD 层。 注意，如果设备在 SSD 和 HDD 层中都已经有大量数据，则可能不会看到此下降。 在此示例中，设备具有少量数据。

![创建云快照后的本地存储使用量](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>性能
“性能”跟踪与在下列对象之间发生的读取和写入操作的数目相关的指标：主机服务器上的 iSCSI 发起程序接口与设备之间，或者设备与云之间。 可以针对特定的卷、特定的卷容器或者所有卷容器测量此性能。 “性能”还包括 CPU 使用率以及设备上各种网络接口的网络吞吐量。

### <a name="io-performance-for-initiator-to-device"></a>从发起程序到设备的 I/O 性能
下面的图表针对某个生产设备的所有卷显示了从发起程序到设备的 I/O。 绘制的指标为每秒的读取和写入字节数。 还可以在图表中绘制读取、写入和未完成的 IO，或者绘制读取和写入延迟。

![从发起程序到设备的 IO 性能](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>从设备到云的 I/O 性能
对于同一设备，针对所有卷容器为从设备到云的数据绘制了 I/O 操作数。 在此设备上，数据仅在线性层中，未扩散到云。 没有发生从设备到云的任何读取写入操作。 因此，图表中的各个峰值之间相隔 5 分钟，这对应于在设备与服务之间检查检测信号的频率。

对于同一设备，从上午 11:50 开始为卷数据创建了一个云快照。 这导致数据从设备流向云。 在此持续时间内，发生了到云的写入。 IO 图表中显示了一个以写入字节数/秒为单位的峰值，这对应于创建快照的时间。

![在创建云快照期间从设备到云的 IO](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>设备网络接口的网络吞吐量
“网络吞吐量”跟踪从主机服务器和设备上的 iSCSI 发起程序网络接口传输的数据量以及在设备与云之间传输的数据量。 可以针对设备上的每个 iSCSI 网络接口监视此指标。

下面的图表显示了 Data 0（设备上的一个 1 GbE 网络）的网络吞吐量，该网络同时启用了云（默认设置）和 iSCSI。 在此设备上，在 6 月 14 日晚上 9 点左右，数据被分层写入到云（那时没有创建云快照，这指示分层成为将数据移动到云中的机制），这导致发生了到云的 IO。 在网络吞吐量图中，针对同一时间没有对应的峰值，并且大部分流量都传出到云。

![Data 0 的网络吞吐量](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

如果查看 Data 1 接口（仅启用了 iSCSI 的另一个 1 GbE 网络接口）的吞吐量图表，则会发现，在此持续时间内实际上没有网络流量。

![Data 1 的网络吞吐量](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>设备的 CPU 使用率
“CPU 使用率”跟踪与设备上使用的 CPU 相关的指标。 下面的图表显示了用于生产的某个设备的 CPU 使用率统计信息。

![设备的 CPU 使用率](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>后续步骤
* 了解如何[使用 StorSimple 设备管理器服务设备仪表板](storsimple-device-dashboard.md)。
* 了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-manager-service-administration.md)。


