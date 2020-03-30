---
title: Azure 数据共享的灾难恢复
description: Azure 数据共享的灾难恢复
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483175"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure 数据共享的灾难恢复

在本文中，我们将介绍如何为 Azure 数据共享配置灾难恢复环境。 Azure 数据中心中断很少发生，但可以持续几分钟到几小时。 数据中心中断可能导致依赖于数据提供商共享的数据的环境中断。 通过按照本文中详述的步骤，数据提供程序可以继续与其数据使用者共享数据，在托管数据共享的主区域的数据中心中断时。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>实现 Azure 数据共享的业务连续性

为了准备数据中心中断，数据提供程序可以在辅助区域中预配数据共享环境。 可以采取一些措施，确保在发生数据中心中断时顺利故障转移。 

数据提供程序可以在其他区域中预配辅助 Azure 数据共享资源。 可以配置这些数据共享资源，以包括主数据共享环境中存在的数据集。 在配置 DR 环境时，数据使用者可以添加到数据共享中，或者在以后的时间点（即 作为手动故障转移步骤的一部分）。

如果数据使用者在为 DR 目的预配的辅助环境中具有活动共享订阅，则可以在故障转移时启用快照计划。 如果数据使用者不想为 DR 目的订阅辅助区域，则可以在稍后时间点邀请他们加入辅助数据共享。 

数据使用者可以具有为 DR 目的处于空闲状态的活动共享订阅，或者数据提供程序可以在以后的时间点将其添加为手动故障转移过程的一部分。 

## <a name="related-information"></a>相关信息

- [业务连续性和灾难恢复](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [将高可用性纳入 BCDR 策略](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。




