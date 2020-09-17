---
title: Azure NetApp 文件卷的跨区域复制 |Microsoft Docs
description: 介绍 Azure NetApp 文件跨区域复制的功能、支持的区域对、服务级别目标、数据持续性和成本模型。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: e7d424435c29eb3e7a6779a7036816a7bedd2085
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708551"
---
# <a name="cross-region-replication-of-azure-netapp-files-volumes"></a>Azure NetApp 文件卷的跨区域复制

Azure NetApp 文件复制功能通过跨区域卷复制提供数据保护。 可将数据从一个区域中的 Azure NetApp 文件卷 (源) 异步复制到另一个区域中的另一个 Azure NetApp 文件卷 (目标) 。  此功能使你能够在发生区域范围的服务中断或灾难时故障转移关键应用程序。

> [!IMPORTANT]
> 跨区域复制功能目前为公共预览版。 需要提交候补请求，以便通过 [Azure NetApp 文件跨区域复制候补提交页](https://aka.ms/anfcrrpreviewsignup)访问该功能。 使用跨区域复制功能之前，请先从 Azure NetApp 文件团队等待官方确认电子邮件。

## <a name="supported-region-pairs"></a>支持的区域对

Azure NetApp 文件卷复制当前提供以下固定区域对：  

* 美国西部和美国东部
* 美国西部2和美国东部 
* 美国中南部和美国中部 
* 美国中南部和美国东部
* 美国中南部和美国东部2 
* 美国东部2和美国中部 
* 欧洲北部和欧洲西部
* 英国南部和英国西部
* 澳大利亚东部和澳大利亚东南部
* 加拿大中部和加拿大东部
* 日本东部和日本西部
* 英国南部和德国中西部
* 东南亚和澳大利亚东部

## <a name="service-level-objectives"></a>服务级别目标

恢复点目标 (RPO) 或可承受的最大数据丢失数定义为复制计划的两倍。  观察到的实际 RPO 可能因多种因素而异，例如，数据集总大小以及更改率、数据覆盖百分比以及可传输的复制带宽。   

* 对于10分钟的复制计划，最大 RPO 为20分钟。  
* 对于每小时复制计划，最大 RPO 为两小时。  
* 对于每日复制计划，最大 RPO 为两天。  

恢复时间目标 (RTO) ，或可容忍的最大业务应用程序停机时间取决于引入应用程序的因素，并提供对第二个站点中的数据的访问。 用于中断对等关系以激活目标卷并在第二个站点中提供读取和写入数据访问的 RTO 存储部分应在一分钟内完成。

## <a name="cost-model-for-cross-region-replication"></a>跨区域复制的成本模型  

通过 Azure NetApp 文件跨区域复制，只需为复制的数据量付费。 不收取任何费用，也没有最低的使用费用。 复制价格基于在初始复制配置期间选择的 *目标* 卷的复制频率和区域。 有关详细信息，请参阅 [Azure NetApp 文件定价](https://azure.microsoft.com/pricing/details/netapp/) 页。  

常规 Azure NetApp 文件存储容量费用适用于复制目标卷 (也称为 *数据保护* 卷) 。 

### <a name="pricing-examples"></a>定价示例

在一个月中，按跨区域复制功能复制的数据量是按月计费的跨区域复制量。 复制的数据量以 GiB 度量。 它表示从源卷到目标卷的所有常规复制期间，以及从目标卷到源卷的所有重新同步复制期间跨两个区域复制的数据的总和。

#### <a name="example-1-month-1-baseline-replication-and-incremental-replications"></a>示例1：第1个月的基线复制和增量复制

假设出现以下情况：

* *源*卷来自 Azure NetApp 文件*高级*服务级别。 在一个月的第一天开始时，它的卷配额大小为 1000 GiB，卷使用大小为 500 GiB。 该卷位于 *美国中南部* 区域。
* *目标*卷来自 Azure NetApp 文件*标准*服务级别。 它位于 *美国东部 2* 区域。
* 在上述两个卷之间配置了基于 *小时* 的跨区域复制。 因此，复制价格为每个 GiB $0.12。
* 为简单起见，假设源卷每隔0.5 一小时会更改一个 GiB 的数据，但总使用的卷大小不会增长 (保持为 500 GiB) 。 

初始设置完成后，基线复制会立即进行。  

* 基线复制期间复制的数据量： `500 GiB`
* 基线复制费用： `500 GiB * $0.12 = $60`

完成基线复制后，只会复制更改的块。 因此，在后续的增量复制中，每小时只能复制 0.5 GiB 的数据。

* 每月30天跨增量复制复制的数据量总计： `0.5 GiB * 24 hours * 30 days = 360 GiB`
* 增量复制费用： `360 GiB * $0.12 = $43.2`

在第1个月结束时，总的跨区域复制费用如下所示：  

*  每月1： `$60 + $43.2 = $103.2`

常规 Azure NetApp 文件存储容量费用适用于目标卷。 但是，目标卷可以使用不同于源卷层)  (和更便宜的存储层。

#### <a name="example-2-month-2-incremental-replications-and-resync-replications"></a>示例2：第2个月2个增量复制和重新同步复制  

假设你有一个源卷、一个目标卷和两个设置之间的复制关系，如示例1中所述。 在第二个月的29天 (每月30天) ，按预期方式发生了每小时副本。

* 在29天内增量复制间复制的数据量的总和： `0.5 GiB * 24 hours * 29 days = 348 GiB`

假设在该月的最后一天，在源区域中发生计划外故障，并故障转移到目标卷。 2小时后，源区域恢复，并执行从目标卷到源卷的重新同步复制。 在2小时内，在目标卷上发生了 0.8 GiB 的数据更改，需要 resynced 到源中。

* 过去一天在22小时内通过常规复制复制的数据量总计： `0.5 GiB * 22 hours = 11 GiB`
* 在一个重新同步复制期间复制的数据量： `0.8 GiB`

因此，在第2月结束时，跨区域复制费用总计如下所示：  

* 每月2： `(348 GiB + 11 GiB + 0.8 GiB) * $0.12 = $43.18`

第2个月的常规 Azure NetApp 文件存储容量费用适用于目标卷。

## <a name="next-steps"></a>后续步骤
* [使用跨区域复制的要求和注意事项](cross-region-replication-requirements-considerations.md)
* [创建复制对等](cross-region-replication-create-peering.md)
* [显示复制关系的运行状况状态](cross-region-replication-display-health-status.md)
* [管理灾难恢复](cross-region-replication-manage-disaster-recovery.md)
* [卷复制指标](azure-netapp-files-metrics.md#replication)
* [跨区域复制故障排除](troubleshoot-cross-region-replication.md)


