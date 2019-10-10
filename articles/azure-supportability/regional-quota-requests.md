---
title: Azure 区域配额增加请求 |Microsoft Docs
description: 区域配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e73f22b0e617ad8f20b98c3bb0fb1647bf5fe61d
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249079"
---
# <a name="total-regional-vcpu-limit-increase"></a>区域性总 vCPU 限制提高 

每个区域的每个订阅在两个级别上为虚拟机和虚拟机规模集资源管理器 vCPU 配额。 

第一层是**区域个 vcpu 限制**（跨所有 VM 系列），第二层是**每个 vm 系列个 vcpu 限制**（如 D 系列个 vcpu）。 每当部署新 VM 时，该 VM 系列的新的和现有个 vcpu 使用的总和不得超过为该特定 VM 系列批准的 vCPU 配额。 此外，在所有 VM 系列上部署的新的和现有的 vCPU 计数不应超过为订阅批准的总区域个 vcpu 配额。 如果超过了上述任一配额，将不允许部署 VM。
可以从 Azure 门户中为 VM 序列请求增加个 vcpu 配额限制。 VM 序列配额增加会自动增加相同数量的区域个 vcpu 限制。 

创建新订阅时，默认的区域个 vcpu 不能等于所有单个 VM 序列的默认 vCPU 配额之和。 这可能会导致订阅中每个要部署的单个 VM 序列具有足够的配额，但没有足够的配额来容纳所有部署的区域个 vcpu。 在这种情况下，你将需要提交请求，以显式增加区域个 vcpu 限制。 区域个 vcpu 限制的总大小不能超过区域的所有 VM 系列上批准的配额的总和。

在[虚拟机 vCPU 配额页](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 订阅和服务限制](https://aka.ms/quotalimits)页上了解有关配额的详细信息。 

你现在可以通过 "**帮助 + 支持**" 边栏选项卡或门户中的 "**使用情况 + 配额**" 边栏选项卡请求增加。 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>使用 "**帮助 + 支持**" 边栏选项卡在订阅级别请求总区域个 vcpu 配额增加

按照下面的说明通过 Azure 的 "帮助 + 支持" 边栏选项卡，在 Azure 门户中创建支持请求。 

1. 从 https://portal.azure.com 中，选择 "**帮助 + 支持**"。

![帮助 + 支持](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  选择“新建支持请求”。 

![新建支持请求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 "问题类型" 下拉列表中，选择 "**服务和订阅限制（配额）** "。

![问题类型下拉列表](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 选择需要增加配额的订阅。

![选择订阅 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 选择 "**配额类型**" 下拉列表中的 "**其他请求**"。

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. 在**详细**信息窗格中，根据下面的示例提供其他信息，以帮助处理请求并继续创建案例。 
    1.  **部署模型**–指定 "资源管理器"
    2.  **请求的区域**-指定所需的区域，例如美国东部2
    3.  **新限制值**–指定新区域限制。 此订阅不应超过单独 SKU 系列的批准配额总和

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>使用 "使用情况 **+ 配额**" 边栏选项卡在订阅级别请求总区域个 vcpu 配额增加

按照下面的说明操作，通过 Azure 的 "使用情况 + 配额" 边栏选项卡，在 Azure 门户中创建支持请求。 

1. 从 https://portal.azure.com 中选择“订阅”。

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 选择需要增加配额的订阅。

![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 选择“使用情况 + 配额”

![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角选择“请求增加”。

![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5. 选择 "**配额类型**" 下拉列表中的 "**其他请求**"。

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. 在**详细**信息窗格中，根据下面的示例提供其他信息，以帮助处理请求并继续创建案例。 
    1.  **部署模型**–指定 "资源管理器"
    2.  **请求的区域**-指定所需的区域，例如美国东部2
    3.  **新限制值**–指定新区域限制。 此订阅不应超过单独 SKU 系列的批准配额总和

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



