---
title: Azure 事件网格中的异地灾难恢复 | Microsoft Docs
description: 介绍 Azure 事件网格如何自动支持异地灾难恢复 (GeoDR)。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307312"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure 事件网格中的服务器端异地灾难恢复
现在，事件网格不仅可以针对新的，而且还能针对现有的所有域、主题和事件订阅提供元数据的自动异地灾难恢复 (GeoDR)。 在整个 Azure 区域出现故障时，事件网格已将所有与事件相关的基础结构元数据同步到配对的区域。 无需你的干预，新事件就能再次开始流动。 

根据两项指标衡量灾难恢复：

- [恢复点目标 (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)：可以丢失数据的分钟数或小时数。
- [恢复时间目标 (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)：可以关闭服务的分钟数。

事件网格的自动故障转移针对元数据（事件订阅等）和数据（事件）提供不同的 RPO 和 RTO。 如果所需的规范不同于下述规范，仍可以[使用主题运行状况 API 实现你自己的客户端故障转移](custom-disaster-recovery.md)。

## <a name="recovery-point-objective-rpo"></a>恢复点目标 (RPO)
- **元数据 RPO**：0 分钟。 每当在事件网格中创建某个资源时，该资源会立即跨区域复制。 发生故障转移时，不会丢失任何元数据。
- **数据 RPO：** 如果您的系统状况良好，并在区域故障转移时赶上现有流量，则事件的 RPO 约为 5 分钟。

## <a name="recovery-time-objective-rto"></a>恢复时间目标 (RTO)
- **元数据 RTO**：虽然通常发生得更快，但在 60 分钟内，事件网格将开始接受主题和订阅的创建/更新/删除调用。
- **数据 RTO**： 与元数据一样，它通常发生得更快，但在 60 分钟内，事件网格将在区域故障转移后开始接受新的流量。

> [!NOTE]
> 事件网格中元数据 GeoDR 的成本为：$0。


## <a name="next-steps"></a>后续步骤
若要实现自己的客户端故障转移逻辑，请参阅 [# 在事件网格为自定义主题构建自己的灾难恢复方案](custom-disaster-recovery.md)
