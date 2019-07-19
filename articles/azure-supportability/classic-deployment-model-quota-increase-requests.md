---
title: Azure 经典部署模型 |Microsoft Docs
description: Azure 经典部署模型
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/20/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 70ad44eade871d52591014ee24e645b95c52f1e5
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234870"
---
# <a name="classic-deployment-model"></a>经典部署模型

经典部署模型是早期版本的 Azure 部署模式, 它对虚拟机和虚拟机规模集强制实施全局 vCPU 配额限制。 建议不要再使用经典部署模型, 资源管理器模型将其取代。 若要详细了解这两种部署模型和资源管理器, 请参阅资源管理器部署模型页。 创建新订阅时, 将为其分配默认的个 vcpu 配额。 每当使用经典部署模型部署新的 VM 时, 所有区域中新的和现有个 vcpu 使用的总和不得超过针对经典部署模型批准的 vCPU 配额。 详细了解[Azure 订阅和服务限制页](https://aka.ms/quotalimits)上的配额

通过门户中的 "帮助 + 支持" 边栏选项卡或 "使用情况 + 配额" 边栏选项卡, 可请求提高经典部署模型的个 vcpu 限制。

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

7. 在 "**配额详细信息**" 面板中, 选择 "经典" 并选择一个位置。

   ![配额详细信息 DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. 选择需要增加的**SKU 系列**。 

   ![SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. 输入想对订阅设定的新限制。 若要删除某行，请从 SKU 系列下拉列表中取消选中 SKU，或单击“x”丢弃图标。 为每个 SKU 系列输入所需的配额后, 在 "配额详细信息" 面板上单击 "**保存并继续**", 以继续创建支持请求。

   ![新限制](./media/resource-manager-core-quotas-request/new-limits-classic.png)

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
   
6. 在 "**问题详细信息**" 中, 通过单击 "**提供详细信息**" 提供附加信息以帮助处理您的请求。

   ![提供详细信息](./media/resource-manager-core-quotas-request/provide-details.png)

7. 在 "**配额详细信息**" 面板中, 选择 "经典" 并选择一个位置。

   ![配额详细信息 DM](./media/resource-manager-core-quotas-request/quota-details-classic.png)

8. 选择需要增加的**SKU 系列**。 

   ![SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

9. 输入想对订阅设定的新限制。 若要删除某行，请从 SKU 系列下拉列表中取消选中 SKU，或单击“x”丢弃图标。 为每个 SKU 系列输入所需的配额后, 在 "配额详细信息" 面板上单击 "**保存并继续**", 以继续创建支持请求。

   ![新限制](./media/resource-manager-core-quotas-request/new-limits-classic.png)

