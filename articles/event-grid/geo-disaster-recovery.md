---
title: Azure 事件网格中的异地灾难恢复 |Microsoft Docs
description: 说明如何 Azure 事件网格支持异地灾难恢复 (GeoDR) 自动。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66307312"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure 事件网格中的服务器端异地灾难恢复
事件网格现在包含不仅新的、 对元数据，但所有现有的域、 主题和事件订阅的自动异地灾难恢复 (GeoDR)。 如果整个 Azure 区域出现故障，事件网格已将具有所有事件相关的基础结构元数据同步到配对区域。 您的新事件将开始再次使用由你无需干预的流。 

使用两个指标度量，灾难恢复：

- [恢复点目标 (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)： 分钟或小时的数据可能会丢失。
- [恢复时间目标 (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)： 向下的该服务可作为的小时分钟数。

事件网格自动故障转移具有不同的 Rpo 和 Rto 为元数据 （事件订阅等） 和数据 （事件）。 如果你需要从以下的不同命名规范，你仍然可以实现你自己[故障转移使用本主题的客户端的运行状况 api](custom-disaster-recovery.md)。

## <a name="recovery-point-objective-rpo"></a>恢复点目标 (RPO)
- **元数据的 RPO**： 零分钟。 每当事件网格中创建一个资源，它立即将跨区域复制。 故障转移时，不不丢失任何元数据。
- **数据 RPO**:如果系统运行正常并且在区域故障转移时遇上现有流量，则事件的 RPO 大约为 5 分钟。

## <a name="recovery-time-objective-rto"></a>恢复时间目标 (RTO)
- **元数据的 RTO**:尽管通常它发生速度更快，在 60 分钟，事件网格将开始接受为主题和订阅的创建/更新/删除调用。
- **数据 RTO**:如元数据，它通常会出现速度更快，但是在 60 分钟内，事件网格将开始接受新流量区域的故障转移后。

> [!NOTE]
> GeoDR 事件网格上的元数据的成本是： 0 美元。


## <a name="next-steps"></a>后续步骤
如果你想要实现自己的客户端的故障转移逻辑，请参阅[# 构建你自己的事件网格中的自定义主题的灾难恢复](custom-disaster-recovery.md)
