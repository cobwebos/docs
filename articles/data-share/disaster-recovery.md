---
title: Azure 数据共享的灾难恢复
description: Azure 数据共享的灾难恢复
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 61804aaf65a97485e2b2b5bb4869c335a14ce812
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513552"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure 数据共享的灾难恢复

本文介绍如何为 Azure 数据共享配置灾难恢复环境。 Azure 数据中心的故障很少见，但可以在几分钟到几小时内持续。 数据中心中断会导致依赖于数据访问接口所共享的数据的环境中断。 按照本文中所述的步骤，数据访问接口可以在承载数据共享的主要区域发生数据中心服务中断时继续与数据使用者共享数据。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>为 Azure 数据共享实现业务连续性

若要为数据中心服务中断做好准备，数据访问接口可以在次要区域中预配数据共享环境。 在发生数据中心服务中断的情况下，可以采取措施来确保平滑故障转移。 

数据访问接口可以将辅助 Azure 数据共享资源预配到其他区域。 这些数据共享资源可配置为包括主 Azure 数据共享资源中存在的共享和数据集。 它们可以在配置 DR 环境时或稍后将数据使用者邀请到辅助共享（即 作为手动故障转移步骤的一部分。

如果数据使用者在为 DR 目的而预配的辅助环境中具有活动共享订阅，则他们可以在故障转移过程中启用快照计划。 如果数据使用者出于灾难恢复目的不想订阅次要区域，则可以在以后邀请他们加入辅助共享。 

数据使用者可以使用处于空闲状态的活动共享订阅进行 DR，也可以在手动故障转移过程中将数据访问接口稍后邀请到这些订阅。 

## <a name="related-information"></a>相关信息

- [业务连续性和灾难恢复](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [将高可用性纳入 BCDR 策略](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。




