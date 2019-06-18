---
title: 每个 VM 的 azure vCPU 配额增加请求 |Microsoft Docs
description: 每个 VM vCPU 配额增加请求
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: f921b4a95c1b0cfb29d84c0bacc17d268af6e6c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082815"
---
# <a name="vm-series-vcpu-limit-increase"></a>VM 系列 vCPU 限制提高

你现在可以请求通过增加**帮助 + 支持**边栏选项卡或**使用情况 + 配额**门户边栏选项卡。 

## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>每个 VM 在订阅级别使用的 vCPU 配额增加请求**帮助 + 支持**边栏选项卡

请按照下面的说明来创建支持请求通过 Azure 的帮助 + 支持边栏选项卡在 Azure 门户中可用。 

1. 从 https://portal.azure.com ，选择**帮助 + 支持**。

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


## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>每个 VM 在订阅级别使用的 vCPU 配额增加请求**使用情况 + 配额**边栏选项卡

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

