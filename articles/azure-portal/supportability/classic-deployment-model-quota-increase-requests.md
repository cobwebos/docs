---
title: Azure 经典部署模型
description: 经典部署模型现在被资源管理器模型取代，它为 VM 和虚拟机规模集强制实施全局 vCPU 配额限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835530"
---
# <a name="classic-deployment-model"></a>经典部署模型

经典部署模型是较老一代 Azure 部署模型。 它对虚拟机和虚拟机规模集强制实施全局 vCPU 配额限制。 不再推荐经典部署模型，现在被资源管理器模型取代。

要了解有关这两种部署模型和使用资源管理器的优势的更多信息，请参阅[资源管理器和经典部署](../../azure-resource-manager/management/deployment-models.md)。

创建新订阅时，将为其分配默认的 vCPU 配额。 每当使用经典部署模型部署新虚拟机时，所有区域的新 vCPU 使用情况的总和不得超过为经典部署模型批准的 vCPU 配额。

要了解有关配额的更多，请参阅[Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

您可以请求提高经典部署模型的 vCPU 配额限制。 在 Azure 门户中使用**帮助 + 支持**或使用情况 +**配额**。

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>使用帮助和支持，在订阅级别请求每个 VM 系列 vCPU 配额增加

按照以下说明，在 Azure 门户中使用帮助 **+ 支持**来创建支持请求。

1. 在[Azure 门户](https://portal.azure.com)菜单中，选择 **"帮助 + 支持**"。

   ![在 Azure 门户中选择帮助和支持](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 选择 **"新建支持请求**"。

   ![在 Azure 门户中创建新的支持请求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 在 **"问题"类型**中，选择**服务和订阅限制（配额）。**

   ![选择配额作为问题类型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 选择要增加其配额的订阅。

   ![选择要增加配额的订阅](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 对于**配额类型**，选择**计算 VM（核心-vCPU）订阅限制增加**。

   ![选择要增加的配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 选择 **"下一步"：** 打开**问题详细信息的解决方案**。 选择 **"提供详细信息**以提供其他信息"。

   ![提供详细信息，帮助您获得请求](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在**配额详细信息**中，选择 **"经典"** 并选择 **"位置**"。

   ![添加详细信息，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 对于**SKU 系列**，选择要增加的一个或多个 SKU 族。

   ![指定要增加的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 输入想对订阅设定的新限制。 要删除行，请从**SKU 系列**中取消选择 SKU 或选择丢弃的"X"图标。 为每个 SKU 系列输入配额后，在 **"配额详细信息**中**保存并继续"** 以继续支持请求。

   ![请求新限制](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>使用使用情况和配额，在订阅级别请求每个 VM 系列 vCPU 配额增加

按照以下说明在 Azure 门户中使用 **"使用情况 + 配额**"创建支持请求。

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 **"订阅**"。

   ![转到 Azure 门户中的订阅](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 选择要增加其配额的订阅。

   ![选择要修改的订阅](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 选择 **"用法+配额**"。

   ![选择订阅的使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角，选择 **"请求增加**"。

   ![选择增加配额](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 选择**计算 VM（核心-vCPU）订阅限制随着****配额类型**增加。

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 选择 **"下一步"：** 打开**问题详细信息的解决方案**。 选择 **"提供详细信息**以提供其他信息"。

   ![提供您的请求的详细信息](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在**配额详细信息**中，选择 **"经典"** 和"**位置**"。

   ![选择配额详细信息，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 选择一个或多个 SKU 族进行增加。

   ![选择 SKU 系列以进行增加](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 输入想对订阅设定的新限制。 要删除行，请从**SKU 系列**中取消选择 SKU 或选择丢弃的"X"图标。 为每个 SKU 系列输入配额后，在 **"配额详细信息**中**保存并继续"** 以继续支持请求。

   ![输入新配额](./media/resource-manager-core-quotas-request/new-limits-classic.png)

