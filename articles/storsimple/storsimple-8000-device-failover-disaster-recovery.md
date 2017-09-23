---
title: "StorSimple 8000 系列设备的故障转移、灾难恢复 | Microsoft Docs"
description: "了解如何将 StorSimple 设备故障转移到其自身、另一个物理设备或云设备。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 5dc4a98bf889d38c62c76364289c2d58c14d771e
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>StorSimple 8000 系列设备的故障转移和灾难恢复

## <a name="overview"></a>概述

本文介绍了 StorSimple 8000 系列设备的设备故障转移功能以及在发生灾难时可以如何使用此功能来恢复 StorSimple 设备。 StorSimple 使用设备故障转移将数据从数据中心内的源设备迁移到另一目标设备。 本文中的指导适用于运行软件版本 Update 3 和更高版本的 StorSimple 8000 系列物理设备与云设备。

当发生灾难时，StorSimple 使用“设备”边栏选项卡启动设备故障转移功能。 此边栏选项卡列出连接到 StorSimple 设备管理器服务的所有 StorSimple 设备。

![“设备”边栏选项卡](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>灾难恢复 (DR) 与设备故障转移

在灾难恢复 (DR) 方案中，主要设备将停止工作。 StorSimple 使用主设备作为“源”并将关联的云数据移动到另一“目标”设备。 此过程称为*故障转移*。 下图演示了故障转移过程。

![设备故障转移过程中发生的操作](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

故障转移的目标设备可以是物理设备，甚至可以是云设备。 目标设备可以位于与源设备相同或不同的地理位置。

在故障转移期间，可以选择要迁移的卷容器。 然后，StorSimple 会将这些卷容器的所有权从源设备更改为目标设备。 在卷容器更改所有权后，StorSimple 会将这些容器从源设备中删除。 在删除完成后，你可以对目标设备进行故障回复。 “故障回复”将所有权移交回原始源设备。

### <a name="cloud-snapshot-used-during-device-failover"></a>设备故障转移期间使用的云快照

在灾难恢复之后，将使用最新的云备份将数据还原到目标设备。 有关云快照的详细信息，请参阅[使用 StorSimple 设备管理器服务执行手动备份](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup)。

在 StorSimple 8000 系列设备上，备份策略与备份相关联。 如果同一个卷有多个备份策略，则 StorSimple 会选择卷数最多的备份策略。 然后，StorSimple 将使用所选备份策略中的最新备份在目标设备上还原数据。

假设有两个备份策略，分别为 *defaultPol* 和 *customPol*：

* *defaultPol*：有一个卷（*vol1*），每天晚上 10:30 开始运行。
* *customPol*：有四个卷（*vol1*、*vol2*、*vol3*、*vol4*），每天晚上 10:00 开始运行。

在此情况下，StorSimple 将针对崩溃一致性确定优先级并将使用 *customPol*，因为具有更多卷。 此策略中的最新备份将用于还原数据。 有关如何创建和管理备份策略的详细信息，请转到[使用 StorSimple 设备管理器服务管理备份策略](storsimple-8000-manage-backup-policies-u2.md)。

## <a name="common-considerations-for-device-failover"></a>设备故障转移常见注意事项

在对设备进行故障转移之前，请查看以下信息：

* 在设备故障转移启动之前，卷容器内的所有卷都必须处于脱机状态。 在计划外故障转移中，StotSimple 卷将自动脱机。 但是，如果执行计划内故障转移（来测试灾难恢复），则你必须使所有卷脱机。
* 只会为灾难恢复列出具有关联的云快照的卷容器。 必须至少存在一个具有关联的云快照的卷容器才能恢复数据。
* 如果存在跨多个卷容器的云快照，则 StorSimple 会将这些卷容器作为一个集合进行故障转移。 在极少情况下，如果本地快照跨多个卷容器，但关联的云快照没有跨多个容器，则 StorSimple 会对本地快照进行故障转移，并且本地数据在灾难恢复后将会丢失。
* 灾难恢复的可用目标设备是有足够空间容纳所选卷容器的设备。 任何没有足够空间的设备都不会作为目标设备列出。
* 在灾难恢复之后（在一段有限的时间内），数据访问性能可能会受到明显影响，因为设备需要从云访问数据，然后将数据存储在本地。

#### <a name="device-failover-across-software-versions"></a>跨软件版本的设备故障转移

部署中的 StorSimple 设备管理器服务可能有多个运行不同软件版本的物理设备和云设备。

可以使用下表来确定在灾难恢复期间是否可以故障转移或故障回复到运行不同软件版本的另一个设备，并确定卷类型的行为。

#### <a name="failover-and-failback-across-software-versions"></a>跨软件版本的故障转移和故障回复

| 故障转移/故障回复 | 物理设备 | 云设备 |
| --- | --- | --- |
| 从 Update 3 到 Update 4 |分层卷将故障转移为分层卷。 <br></br>本地固定卷将故障转移为本地固定卷。 <br></br> 在故障转移后，当在 Update 4 设备上创建快照时，[基于热度地图的跟踪](storsimple-update4-release-notes.md#whats-new-in-update-4)将启动。 |本地固定卷将故障转移为分层卷。 |
| 从 Update 4 到 Update 3 |分层卷将故障转移为分层卷。 <br></br>本地固定卷将故障转移为本地固定卷。 <br></br> 用于还原的备份保留着热度地图元数据。 <br></br>在故障回复后，在 Update 3 中，基于热度地图的跟踪不可用。 |本地固定卷将故障转移为分层卷。 |


## <a name="device-failover-scenarios"></a>设备故障转移方案

如果发生灾难，可以选择将 StorSimple 设备故障转移到：

* [物理设备](storsimple-8000-device-failover-physical-device.md)。
* [其自身](storsimple-8000-device-failover-same-device.md)。
* [云设备](storsimple-8000-device-failover-cloud-appliance.md)。

前面的文章针对上述的每个故障转移案例提供了详细步骤。


## <a name="failback"></a>故障回复

对于 Update 3 和更高版本，StorSimple 还支持故障回复。 故障回复恰好是故障转移的反向过程，目标成为源，且故障转移期间的原始源设备现在成为目标设备。 

在故障回复期间，StorSimple 将数据重新同步回主位置，暂停 I/O 和应用程序活动，并切换回原始位置。

在故障转移完成后，StorSimple 执行以下操作：

* StorSimple 从源设备中清除已故障转移的卷容器。
* StorSimple 针对源设备上每个（已故障转移的）卷容器启动一个后台作业。 如果在作业进行时尝试故障回复，则会收到一条通知，告知这样做会造成何种影响。 需要等到该作业完成后才能开始故障回复。
* 完成卷容器删除花费的时间取决于多个因素，例如数据量、数据期限、备份数目以及操作可用的网络带宽。

如果在规划测试故障转移或测试故障回复，我们建议对数据较少（Gb 量级）的卷容器进行测试。 通常，可以在故障转移完成 24 小时后开始故障回复。

## <a name="frequently-asked-questions"></a>常见问题

问： **如果灾难恢复失败或部分成功，会发生什么情况？**

答： 如果灾难恢复失败，我们建议再试一次。 第二个设备故障转移作业知道第一个作业的进度，并且将从该点向前开始。

问： **正在进行设备故障转移时，是否可以删除设备？**

答： 灾难恢复正在进行时无法删除设备。 只能在灾难恢复完成后删除设备。 可以在“作业”边栏选项卡中监视设备故障转移作业进度。

问： **源设备上的垃圾收集何时开始，以便能够删除源设备上的本地数据？**

答： 只有在完全清理设备之后，才会在源设备上启用垃圾收集。 清理过程包括清理已从源设备故障转移的对象，例如卷、备份对象（不是数据）、卷容器和策略。

问： **如果与源设备中卷容器关联的删除作业失败，会发生什么情况？**

答：  如果删除作业失败，则可以手动删除卷容器。 在“设备”边栏选项卡中，选择源设备并单击“卷容器”。 选择已故障转移的卷容器，并在边栏选项卡底部单击“删除”。 删除源设备上所有已故障转移的卷容器后，便可以开始故障回复。 有关详细信息，请转到[删除卷容器](storsimple-8000-manage-volume-containers.md#delete-a-volume-container)。

## <a name="business-continuity-disaster-recovery-bcdr"></a>业务连续性灾难恢复 (BCDR)

当整个 Azure 数据中心停止运行时，将会启动业务连续性灾难恢复 (BCDR) 方案。 此方案可能会影响 StorSimple 设备管理器服务和关联的 StorSimple 设备。

如果 StorSimple 设备在发生灾难前才刚刚注册，则此 StorSimple 设备可能需要进行出厂重置。 在发生灾难后，StorSimple 设备在 Azure 门户中显示为脱机。 必须从门户中删除此设备。 将设备重置为出厂默认设置并重新将其注册到服务。

## <a name="next-steps"></a>后续步骤

如果已准备好执行设备故障转移，请选择下列方案之一来获取详细说明：

- [故障转移到另一个物理设备](storsimple-8000-device-failover-physical-device.md)
- [故障转移到同一设备](storsimple-8000-device-failover-same-device.md)
- [故障转移到 StorSimple 云设备](storsimple-8000-device-failover-cloud-appliance.md)

如果已对设备进行了故障转移，请选择下列选项之一：

* [停用或删除 StorSimple 设备](storsimple-8000-deactivate-and-delete-device.md)。
* [使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。


