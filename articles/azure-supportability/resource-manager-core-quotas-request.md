---
title: "Azure Resource Manager 核心配额增加请求 | Microsoft Docs"
description: "Azure Resource Manager 核心配额增加请求"
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
translationtype: Human Translation
ms.sourcegitcommit: 2373cf227cbed4f6657e31088b11dca5223ce5fe
ms.openlocfilehash: cb6c5b3e86f126d4110d1cd29d8c9891e356e414


---

# <a name="resource-manager-core-quota-increase-requests"></a>Resource Manager 核心配额增加请求

在区域级别和 SKU 系列级别，需要强制实施 Resource Manager 核心配额。
有关配额的强制实施方法的详细信息，请参阅 [Azure 订阅和服务限制](http://aka.ms/quotalimits)页面。
若要了解 SKU 系列的详细信息，可在[虚拟机定价](http://aka.ms/pricingcompute)页面比较成本和性能。

若要请求增加，请在 Azure 门户 [https://portal.azure.com](https://portal.azure.com)中，创建内核的配额支持案例。

> [!NOTE]
> 了解如何在 Azure 门户中[创建支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)

1. 在新的支持请求页上，将问题类型选择为“配额”，将配额类型选择为“内核”。

    ![“配额基本信息”边栏选项卡](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. 将部署模型选择为“资源管理器”并选择一个位置。

    ![“配额问题”边栏选项卡](./media/resource-manager-core-quotas-request/Problem-step.png)

3. 选择需要增加的 SKU 系列。

    ![选择的 SKU 系列](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. 输入想对订阅设定的新限制。

    ![SKU 新配额请求](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- 若要删除某行，请从 SKU 系列下拉列表中取消选中 SKU，或单击“x”丢弃图标。
为每个 SKU 系列输入所需的配额后，在“问题步骤”页面上单击“下一步”，继续创建支持请求。



<!--HONumber=Jan17_HO3-->


