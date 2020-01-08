---
title: Azure 数据共享的灾难恢复
description: Azure 数据共享的灾难恢复
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483175"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure 数据共享的灾难恢复

本文介绍如何为 Azure 数据共享配置灾难恢复环境。 Azure 数据中心的故障很少见，但可以在几分钟到几小时内持续。 数据中心中断会导致依赖于数据访问接口所共享的数据的环境中断。 按照本文中所述的步骤，数据访问接口可以在承载数据共享的主要区域发生数据中心服务中断时继续与数据使用者共享数据。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>为 Azure 数据共享实现业务连续性

若要为数据中心服务中断做好准备，数据访问接口可以在次要区域中预配数据共享环境。 可以采取一些措施，以确保在发生数据中心服务中断的情况下进行平滑故障转移。 

数据访问接口可以将辅助 Azure 数据共享资源预配到其他区域。 这些数据共享资源可配置为包括主数据共享环境中存在的数据集。 在配置 DR 环境时，可以将数据使用者添加到数据共享，也可以在以后的某个时间点添加数据使用者（即 作为手动故障转移步骤的一部分。

如果数据使用者在为 DR 目的而设置的辅助环境中具有活动共享订阅，则可以在故障转移过程中启用快照计划。 如果数据使用者出于灾难恢复目的不想订阅次要区域，则可以在以后的某个时间点邀请他们加入辅助数据共享。 

数据使用者可以使用处于空闲状态的活动共享订阅进行灾难恢复，在手动故障转移过程中，数据访问接口可以将其添加到以后的某个时间点。 

## <a name="related-information"></a>相关信息

- [业务连续性和灾难恢复](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [在 BCDR 策略中构建高可用性](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。




