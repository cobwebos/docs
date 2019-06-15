---
title: Azure 区域的配额增加请求 |Microsoft Docs
description: 区域配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 4c5524cf450959db7055ca5d032c81f79ebac077
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083075"
---
# <a name="total-regional-vcpu-limit-increase"></a>区域 vCPU 总数限制增长 

你现在可以请求通过增加**帮助 + 支持**边栏选项卡或**使用情况 + 配额**门户边栏选项卡。 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>请求在订阅级别使用的总区域的 Vcpu 配额增加**帮助 + 支持**边栏选项卡

请按照下面的说明来创建支持请求通过 Azure 的帮助 + 支持边栏选项卡在 Azure 门户中可用。 

1. 从 https://portal.azure.com ，选择**帮助 + 支持**。

![帮助 + 支持](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  选择“新建支持请求”。  

![新建支持请求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在问题类型下拉列表中选择**服务和订阅限制 （配额）** 。

![问题类型下拉列表](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 选择需要增加配额的订阅。

![选择订阅 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 选择**其他请求**中**配额类型**下拉列表。

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. 在中**详细信息**窗格中，提供你的请求的其他信息，根据以下示例中，以帮助进程，然后继续进行案例创建。 
    1.  **部署模型**– 指定资源管理器
    2.  **请求的区域**– 指定您所需的区域例如美国东部 2
    3.  **新的限制值**– 指定新的区域限制。 这不应超过此订阅的各个 SKU 系列的已批准配额的总和

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>请求在订阅级别使用的总区域的 Vcpu 配额增加**使用情况 + 配额**边栏选项卡

按照下面使用来创建支持请求通过 Azure 的使用情况 + 配额的说明可以在 Azure 门户边栏选项卡。 

1. 从 https://portal.azure.com 中选择“订阅”  。

![订阅](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 选择需要增加配额的订阅。

![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 选择“使用情况 + 配额” 

![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角选择“请求增加”  。

![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5. 选择**其他请求**中**配额类型**下拉列表。

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. 在中**详细信息**窗格中，提供你的请求的其他信息，根据以下示例中，以帮助进程，然后继续进行案例创建。 
    1.  **部署模型**– 指定资源管理器
    2.  **请求的区域**– 指定您所需的区域例如美国东部 2
    3.  **新的限制值**– 指定新的区域限制。 这不应超过此订阅的各个 SKU 系列的已批准配额的总和

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



