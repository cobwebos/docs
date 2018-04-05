---
title: Azure 资源管理器 vCPU 配额增加请求 | Microsoft Docs
description: Azure 资源管理器 vCPU 配额增加请求
author: ganganarayanan
ms.author: gangan
ms.date: 3/15/2018
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a42fa8e4e8dae140db4fcc8977bda335455b97a1
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>资源管理器 vCPU 配额增加请求

在区域级别和 SKU 系列级别，需要强制实施资源管理器 vCPU 配额。
有关配额的强制实施方法的详细信息，请参阅 [Azure 订阅和服务限制](http://aka.ms/quotalimits)页面。
若要了解 SKU 系列的详细信息，可在[虚拟机定价](http://aka.ms/pricingcompute)页面比较成本和性能。

若要请求增加，请在 Azure 门户 ([https://portal.azure.com](https://portal.azure.com)) 中创建 vCPU 的配额支持案例。

> [!NOTE]
> 了解如何在 Azure 门户中[创建支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)

1. 选择“订阅”。

   ![订阅](./media/resource-manager-core-quotas-request/subscriptions.png)

2. 选择需要增加配额的订阅。

   ![选择订阅](./media/resource-manager-core-quotas-request/select-subscription.png)

3. 选择“使用情况 + 配额”

   ![选择使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. 在右上角选择“请求增加”。

   ![请求增加](./media/resource-manager-core-quotas-request/request-increase.png)

5. 选择“内核”作为配额类型。 

   ![填写表单](./media/resource-manager-core-quotas-request/forms.png)