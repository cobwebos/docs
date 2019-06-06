---
title: Azure 资源管理器 vCPU 配额增加请求 | Microsoft Docs
description: Azure 资源管理器 vCPU 配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 05/09/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9d19d1a993d574777aa630b9c58f2472b94716cd
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479391"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>资源管理器 vCPU 配额增加请求

为虚拟机和虚拟机规模集资源管理器 vCPU 配额是在为每个订阅，每个区域中的两个层强制实施。 

第一层的总区域的 Vcpu 限制 （跨所有 VM 系列中），第二个层 （例如 D 系列 Vcpu) VM 系列的 Vcpu 数限制。 每当新的 VM 时要部署的 VM 系列的新的和现有 Vcpu 使用率总和不能超过为该特定 VM 系列进一步批准的 vCPU 配额、 跨所有 VM 系列部署的总 vCPU 计数不应超过总区域的 Vcpu 配额 批准的订阅。 如果超过了上述任一配额，将不允许部署 VM。
你可以从 Azure 门户来请求增加的 VM 系列的 Vcpu 配额限制。 中的 VM 系列配额增加自动增加总区域的 Vcpu 限制相同的量。 

创建新订阅时，默认总区域 Vcpu 的单个 VM 系列此线程的全部可能会导致订阅具有足够的配额为每个你想要部署的单个 VM 序列可能不等于默认 vCPU 配额的总和但订阅没有足够的所有部署的总区域 vcpu 的配额。 在这种情况下，需要提交请求以显式增加总配额增加的区域限制。 区域的 Vcpu 总数限制不能超过已批准的配额的和区域的所有 VM 系列上。

了解有关配额的详细信息[虚拟机 vCPU 配额页](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)并[Azure 订阅和服务限制](https://aka.ms/quotalimits)页。 

你现在可以请求通过增加**帮助 + 支持**边栏选项卡或**使用情况 + 配额**门户边栏选项卡。 

## <a name="request-quota-increase-using-the-help--support-blade"></a>使用请求配额增加**帮助 + 支持**边栏选项卡

请按照下面的说明来创建支持请求通过 Azure 的帮助 + 支持边栏选项卡在 Azure 门户中可用。 

1. 从 https://portal.azure.com，选择**帮助 + 支持**。

![帮助 + 支持](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  选择“新建支持请求”。  

![新建支持请求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在问题类型下拉列表中选择**服务和订阅限制 （配额）** 。

![问题类型下拉列表](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 选择需要增加配额的订阅。

![选择订阅 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 选择**计算的 VM (内核 Vcpu) 订阅限制会增加**中**配额类型**下拉列表。 

![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. 在中**问题详细信息**，通过单击你的请求提供其他信息以帮助进程**提供的详细信息**。

![提供详细信息](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在中**配额的详细信息**面板，选择部署模型并选择一个位置。

![配额详细信息数据挖掘](./media/resource-manager-core-quotas-request/quota-details.png)

8. 选择**SKU 系列**需要增加。 

![SKU 系列](./media/resource-manager-core-quotas-request/sku-family.png)

9. 输入想对订阅设定的新限制。 若要删除某行，请从 SKU 系列下拉列表中取消选中 SKU，或单击“x”丢弃图标。 对于每个 SKU 系列输入所需的配额后，单击**保存并继续**继续创建支持请求配额的详细信息面板上。

![新的限制](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-quota-increase-at-subscription-level-using-usages--quota"></a>请求在订阅级别使用使用情况 + 配额增加配额

按照下面使用来创建支持请求通过 Azure 的使用情况 + 配额的说明可以在 Azure 门户边栏选项卡。 

1. 从 https://portal.azure.com 中选择“订阅”  。

![订阅](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 选择需要增加配额的订阅。

![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 选择“使用情况 + 配额” 

![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角选择“请求增加”  。

![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5. 选择**计算 VM (内核 Vcpu) 订阅限制会增加**作为配额类型。 

![填写表单](./media/resource-manager-core-quotas-request/forms.png)
   
6. 在中**配额的详细信息**面板，选择部署模型并选择一个位置。

![“配额问题”边栏选项卡](./media/resource-manager-core-quotas-request/problemstep.png)

7. 选择**SKU 系列**需要增加。

![选择的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family.png)

8. 输入想对订阅设定的新限制。 若要删除某行，请从 SKU 系列下拉列表中取消选中 SKU，或单击“x”丢弃图标。 对于每个 SKU 系列输入所需的配额后，单击**保存并继续**在问题步骤页上，若要继续创建支持请求。

![SKU 新配额请求](./media/resource-manager-core-quotas-request/new-limits.png)


