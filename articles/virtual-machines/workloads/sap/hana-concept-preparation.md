---
title: Azure 上的 SAP HANA（大型实例）的灾难恢复原则和准备工作 | Microsoft Docs
description: Azure 上的 SAP HANA（大型实例）的灾难恢复原则和准备工作
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb1ed063cb11a82d786badd3f63b2d4b6932ce13
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709730"
---
# <a name="disaster-recovery-principles"></a>灾难恢复原则

HANA 大型实例提供不同 Azure 区域中 HANA 大型实例戳之间的灾难恢复功能。 例如，如果在 Azure 美国西部区域部署 HANA 大型实例单元，则可将美国东部区域中的 HANA 大型实例单元用作灾难恢复单元。 如前所述，不会自动配置灾难恢复，因为用户需要为 DR 区域中的另一个 HANA 大型实例单元付费。 灾难恢复设置适用于纵向扩展和横向扩展设置。 

在目前已部署的方案中，客户使用 DR 区域中的单元运行非生产系统，这些系统使用已安装的 HANA 实例。 HANA 大型实例单元的 SKU 应与用于生产目的的 SKU 相同。 下图显示 Azure 生产区域与灾难恢复区域中的服务器单元之间的磁盘配置：

![从磁盘角度的 DR 设置配置](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

如此概览图中所示，需要订购另一组磁盘卷。 目标磁盘卷的大小与灾难恢复单元中用于生产实例的生产卷大小相同。 这些磁盘卷与灾难恢复站点中的 HANA 大型实例服务器单元相关联。 以下卷从生产区域复制到 DR 站点：

- /hana/data
- /hana/logbackups 
- /hana/shared（包括 /usr/sap）

由于从 /hana/log 卷还原的方式不需要 SAP HANA 事务日志，因此不会复制这些卷。 

灾难恢复功能的提供基于 HANA 大型实例基础结构提供的存储复制功能。 存储卷发生更改时，在存储端使用的功能并不是以异步方式复制的恒定更改流。 它是依赖于以下事实的机制：定期创建这些卷的快照。 然后，已复制的快照和尚未复制的新快照之间的差量会转移到灾难恢复站点中的目标磁盘卷。  这些快照存储在卷上；如果发生了灾难恢复故障转移，需要在这些卷上还原这些快照。  

卷中完整数据的首次传输应发生在数据量小于快照之间的差量之前。 因此，DR 站点中的卷包含在生产站点中执行的每个卷的快照。 最终可以使用该 DR 系统回到较早的状态，从而复原丢失的数据而无需回滚生产系统。

如果在一个 HANA 大型实例单元上有多个独立的 SAP HANA 实例时采用 MCOD 部署，应将所有的 SAP HANA 实例存储复制到 DR 端。

使用 HANA 系统复制作为生产站点的高可用性功能，并对 DR 站点使用基于存储的复制时，会将两个节点的卷都从主站点复制到 DR 实例。 必须在 DR 站点购买额外存储（大小与主节点相同）以容纳从主站点和辅助站点到 DR 的复制。 



>[!NOTE]
>HANA 大型实例存储复制功能是镜像和复制存储快照。 如果不按照本文的“备份和还原”部分中介绍的方式执行存储快照，则无法复制到灾难恢复站点。 执行存储快照是将存储复制到灾难恢复站点的先决条件。



## <a name="preparation-of-the-disaster-recovery-scenario"></a>灾难恢复场景的准备
此场景假设在生产 Azure 区域中的 HANA 大型实例上运行了一个生产系统。 对于下面的步骤，我们假设该 HANA 系统的 SID 为“PRD”，另外，假设在 DR Azure 区域中运行的 HANA 大型实例上运行了一个非生产系统。 对于后一种情况，假设该系统的 SID 为“TST”。 下图显示了此配置：

![DR 设置开始](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

如果尚未订购包含附加存储卷集的服务器实例，Azure 上的 SAP HANA 服务管理部门会将附加卷集作为生产副本的目标附加到运行 TST HANA 实例的 HANA 大型实例单元。 为此，需要提供生产 HANA 实例的 SID。 在 Azure 上的 SAP HANA 服务管理部门确认已附加这些卷后，需要将这些卷装载到 HANA 大型实例单元。

![DR 设置后续步骤](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

下一步是在 DR Azure 区域中运行 TST HANA 实例的 HANA 大型实例单元上安装第二个 SAP HANA 实例。 新安装的 SAP HANA 实例需有相同的 SID。 创建的用户需要有与生产实例相同的 UID 和组 ID。 有关详细信息，请阅读[备份和还原](hana-backup-restore.md)。 如果安装成功，需要：

- 执行[备份和还原](hana-backup-restore.md)中介绍的存储快照准备工作的步骤 2。
- 如果之前未执行该步骤，请为 HANA 大型实例单元的 DR 单元创建公钥。 请参阅[备份和还原](hana-backup-restore.md)中介绍的存储快照准备工作的步骤 3。
- 使用新的 HANA 实例维护 HANABackupCustomerDetails.txt  并测试是否可正确连接到存储。  
- 在 DR Azure 区域中的 HANA 大型实例单元上停止新安装的 SAP HANA 实例。
- 卸载这些 PRD 卷并联系 Azure 上的 SAP HANA 服务管理部门。 卷无法一直保持在单元上，因为它们在作为存储复制目标运行时无法对其进行访问。  

![建立复制之前的 DR 设置步骤](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

运营团队在生产 Azure 区域中的 PRD 卷与 DR Azure 区域中的 PRD 卷之间建立复制关系。

>[!IMPORTANT]
>不会复制 /hana/log 卷，因为在灾难恢复站点中将复制的 SAP HANA 数据库还原到一致状态时不需要该卷。

接下来，设置或调整存储快照备份计划，以便在发生灾难时实现 RTO 和 RPO。 若要将恢复点目标最小化，请在 HANA 大型实例服务中设置以下复制间隔：
- 组合快照（快照类型为 **hana**）涵盖的卷每隔 15 分钟复制到灾难恢复站点中的等效存储卷目标。
- 对于事务日志备份卷（快照类型为 **logs**），请设置为每隔 3 分钟复制到灾难恢复站点中的等效存储卷目标。

若要将恢复点目标最小化，请使用以下设置：
- 执行 **hana** 类型存储快照（请参阅“步骤 7：执行快照”），频率为每 30 分钟到 1 小时。
- 每隔 5 分钟执行一次 SAP HANA 事务日志备份。
- 每隔 5-15 分钟执行一次 **logs** 类型存储快照。 使用此间隔周期，RPO 应能达到大约 15-25 分钟。

如果使用此设置，事务日志备份序列、存储快照以及 HANA 事务日志备份卷、/hana/data 和 /hana/shared（包括 /usr/sap）的复制可能如下图中的数据所示：

 ![事务日志备份快照和时间轴上的快照镜像之间的关系](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

若要在灾难恢复方案中实现更好的 RPO，可将 Azure 上的 SAP HANA（大型实例）中的 HANA 事务日志备份复制到其他 Azure 区域。 若要进一步降低 RPO，请执行以下步骤：

1. 以尽可能高的频率将 HANA 事务日志备份到 /hana/logbackups。
1. 使用 rsync 将事务日志备份复制到 NFS 共享托管的 Azure 虚拟机上。 这些 VM 位于 Azure 生产区域和 DR 区域中的 Azure 虚拟网络中。 需要将这两个 Azure 虚拟网络都连接到线路，该线路将 HANA 生产大型实例连接到 Azure。 请参阅[使用 HANA 大型实例实现灾难恢复的网络注意事项](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances)部分中的插图。 
1. 在该区域中附加到 NFS 导出存储的 VM 上保留事务日志备份。
1. 在灾难故障转移方案中，使用在灾难恢复站点中 NFS 共享上较新创建的事务日志备份去补充在 /hana/logbackups 卷上找到的事务日志备份。 
1. 对可保存到 DR 区域的最新备份执行事务日志备份还原。

当 HANA 大型实例操作确认正在设置复制关系并启动执行存储快照备份时，即表示已开始复制数据。

![建立复制之前的 DR 设置步骤](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

在复制过程中，不会还原 DR Azure 区域中的 PRD 卷上的快照。 只会存储这些快照。 如果以这种状态装载卷，则呈现在 DR Azure 区域中的服务器单元上安装 PRD SAP HANA 实例后卸载这些卷时的状态。 此外，这些卷还会呈现尚未还原的存储备份。

如果发生故障转移，还可以选择还原到旧的存储快照而不是最新的存储快照。

## <a name="next-steps"></a>后续步骤

- 参阅[灾难恢复故障转移过程](hana-failover-procedure.md)。
