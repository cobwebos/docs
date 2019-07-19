---
title: Azure 每个 VM vCPU 配额增加请求 |Microsoft Docs
description: 按 VM vCPU 配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9caf82d2fa4683e845d23b5496f9e7b8f9682ec7
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234822"
---
# <a name="vm-series-vcpu-limit-increase"></a>VM 系列 vCPU 限制提高

每个区域的每个订阅在两个级别上为虚拟机和虚拟机规模集资源管理器 vCPU 配额。 

第一层是**区域个 vcpu 限制**(跨所有 VM 系列), 第二层是**每个 vm 系列个 vcpu 限制**(如 D 系列个 vcpu)。 每当部署新 VM 时, 该 VM 系列的新的和现有个 vcpu 使用的总和不得超过为该特定 VM 系列批准的 vCPU 配额。 此外, 在所有 VM 系列上部署的新的和现有的 vCPU 计数不应超过为订阅批准的总区域个 vcpu 配额。 如果超过了上述任一配额，将不允许部署 VM。
可以从 Azure 门户中为 VM 序列请求增加个 vcpu 配额限制。 VM 序列配额增加会自动增加相同数量的区域个 vcpu 限制。 

在[虚拟机 vCPU 配额页](https://docs.microsoft.com/azure/virtual-machines/windows/quotas)和[Azure 订阅和服务限制](https://aka.ms/quotalimits)页上了解有关配额的详细信息。 

你现在可以通过 "**帮助 + 支持**" 边栏选项卡或门户中的 "**使用情况 + 配额**" 边栏选项卡请求增加。 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>使用 "**帮助 + 支持**" 边栏选项卡在订阅级别增加按 VM 序列 vCPU 配额增加的请求

按照下面的说明通过 Azure 的 "帮助 + 支持" 边栏选项卡, 在 Azure 门户中创建支持请求。 

1. 从 https://portal.azure.com 中, 选择 "**帮助 + 支持**"。

   ![帮助 + 支持](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  选择“新建支持请求”。  

     ![新建支持请求](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. 在 "问题类型" 下拉列表中, 选择 "**服务和订阅限制 (配额)** "。

   ![问题类型下拉列表](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. 选择需要增加配额的订阅。

   ![选择订阅 newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. 在 "**配额类型**" 下拉列表中选择 "**计算-VM (个 vcpu) 订阅限制增加**"。 

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. 在 "**问题详细信息**" 中, 通过单击 "**提供详细信息**" 提供附加信息以帮助处理您的请求。

   ![提供详细信息](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 "**配额详细信息**" 面板中, 选择 "部署模型" 并选择一个位置。

   ![配额详细信息 DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. 选择需要增加的**SKU 系列**。 

   ![SKU 系列](./media/resource-manager-core-quotas-request/sku-family.png)

9. 输入想对订阅设定的新限制。 若要删除某行，请从 SKU 系列下拉列表中取消选中 SKU，或单击“x”丢弃图标。 为每个 SKU 系列输入所需的配额后, 在 "配额详细信息" 面板上单击 "**保存并继续**", 以继续创建支持请求。

   ![新限制](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>使用 "使用情况 **+ 配额**" 边栏选项卡在订阅级别增加每个 VM 序列 vCPU 配额

按照下面的说明操作, 通过 Azure 的 "使用情况 + 配额" 边栏选项卡, 在 Azure 门户中创建支持请求。 

1. 从 https://portal.azure.com 中选择“订阅”  。

   ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 选择需要增加配额的订阅。

   ![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 选择“使用情况 + 配额” 

   ![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角选择“请求增加”  。

   ![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5. 选择 "**计算-VM (个 vcpu) 订阅限制**" 将增加为引号类型。 

   ![填写表单](./media/resource-manager-core-quotas-request/select-quota-type.png)
   
6. 在 "**配额详细信息**" 面板中, 选择 "部署模型" 并选择一个位置。

   ![“配额问题”边栏选项卡](./media/resource-manager-core-quotas-request/quota-details.png)

7. 选择需要增加的**SKU 系列**。

   ![选择的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family.png)

8. 输入想对订阅设定的新限制。 若要删除某行，请从 SKU 系列下拉列表中取消选中 SKU，或单击“x”丢弃图标。 为每个 SKU 系列输入所需的配额后, 单击 "问题步骤" 页上的 "**保存并继续**" 以继续创建支持请求。

   ![SKU 新配额请求](./media/resource-manager-core-quotas-request/new-limits.png)
 
