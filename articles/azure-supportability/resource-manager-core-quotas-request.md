---
title: Azure 资源管理器 vCPU 配额增加请求 | Microsoft Docs
description: Azure 资源管理器 vCPU 配额增加请求
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7456785815dbefb2436713814965d90ba0e789ee
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037231"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>资源管理器 vCPU 配额增加请求

在区域级别和 SKU 系列级别，需要强制实施资源管理器 vCPU 配额。
有关配额的强制实施方法的详细信息，请参阅 [Azure 订阅和服务限制](http://aka.ms/quotalimits)页面。
若要了解 SKU 系列的详细信息，可在[虚拟机定价](http://aka.ms/pricingcompute)页面比较成本和性能。

若要请求增加，请按照以下说明使用 Azure 门户中提供的 Azure“使用情况 + 配额”边栏选项卡创建支持请求。 

## <a name="request-quota-increase-at-subscription-level"></a>在订阅级别请求增加配额

1. 从 https://portal.azure.com 中选择“订阅”。

   ![订阅](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 选择需要增加配额的订阅。

   ![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 选择“使用情况 + 配额”

   ![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角选择“请求增加”。

   ![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5. 步骤：1 - 选择“核心”作为配额类型。 

   ![填写表单](./media/resource-manager-core-quotas-request/forms.png)
   
6. 步骤：2 - 将部署模型选择为“资源管理器”并选择一个位置。

    ![“配额问题”边栏选项卡](./media/resource-manager-core-quotas-request/Problem-step.png)

3. 选择需要增加的 SKU 系列。

    ![选择的 SKU 系列](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. 输入想对订阅设定的新限制。

    ![SKU 新配额请求](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- 若要删除某行，请从 SKU 系列下拉列表中取消选中 SKU，或单击“x”丢弃图标。
为每个 SKU 系列输入所需的配额后，在“问题步骤”页面上单击“下一步”，继续创建支持请求。

