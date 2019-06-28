---
title: Azure 区域的配额增加请求 |Microsoft Docs
description: 区域配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7ac6dce9aec1c363fa9772caabad9ce542d43888
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310648"
---
# <a name="total-regional-vcpu-limit-increase"></a>区域性总 vCPU 限制提高 

为虚拟机和虚拟机规模集资源管理器 vCPU 配额是在为每个订阅，每个区域中的两个层强制实施。 

第一层是**总区域 Vcpu 限制**（跨所有 VM 系列中） 和第二层是**Vcpu 限制每个 VM 系列**（例如 D 系列 Vcpu)。 每当新的 VM 时要部署的 VM 系列的新的和现有 Vcpu 使用率总和不能超过批准该特定的 VM 系列的 vCPU 配额。 此外，在所有 VM 系列中部署的总的新的和现有 vCPU 计数不应超过订阅的已批准的总区域的 Vcpu 配额。 如果超过了上述任一配额，将不允许部署 VM。
你可以从 Azure 门户来请求增加的 VM 系列的 Vcpu 配额限制。 中的 VM 系列配额增加自动增加总区域的 Vcpu 限制相同的量。 

创建新订阅时，默认区域 Vcpu 总数可能不是等于默认对所有单个 VM 系列的 vCPU 配额的总和。 这可能导致具有足够的配额为你想要部署，每个单个 VM 系列，但没有足够的配额的区域 Vcpu 总数的所有部署的订阅。 在这种情况下，需要提交请求以显式增大总区域的 Vcpu 数限制。 区域的 Vcpu 总数限制不能超过已批准的配额的和区域的所有 VM 系列上。

了解有关配额的详细信息[虚拟机 vCPU 配额页](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)并[Azure 订阅和服务限制](https://aka.ms/quotalimits)页。 

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



