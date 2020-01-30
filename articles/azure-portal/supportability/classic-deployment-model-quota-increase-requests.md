---
title: Azure 经典部署模型
description: 经典部署模型现在被资源管理器模型取代，对 Vm 和虚拟机规模集强制实施全局 vCPU 配额限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76835530"
---
# <a name="classic-deployment-model"></a>经典部署模型

经典部署模型是较旧的代 Azure 部署模型。 它对虚拟机和虚拟机规模集强制实施全局 vCPU 配额限制。 不再建议使用经典部署模型，它现在被资源管理器模型取代。

若要详细了解这两种部署模型以及使用资源管理器的优点，请参阅[资源管理器和经典部署](../../azure-resource-manager/management/deployment-models.md)。

创建新订阅时，将为其分配默认的个 vcpu 配额。 无论何时使用经典部署模型部署新的虚拟机，所有区域中新的和现有 vCPU 使用的总和不得超过针对经典部署模型批准的 vCPU 配额。

若要了解有关配额的详细信息，请参阅[Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

可以为经典部署模型请求增加 vCPU 配额限制。 在 Azure 门户中使用 "**帮助 + 支持**" 或 "使用**情况 + 配额**"。

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>使用 "帮助 + 支持" 在订阅级别增加每个 VM 序列 vCPU 配额

按照以下说明使用 Azure 门户中的 "**帮助 + 支持**" 创建支持请求。

1. 从 " [Azure 门户](https://portal.azure.com)" 菜单中，选择 "**帮助 + 支持**"。

   ![在 Azure 门户中选择 "帮助 + 支持"](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 选择“新建支持请求”。

   ![在 Azure 门户中创建新的支持请求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 在 "**问题类型**" 中，选择 "**服务和订阅限制（配额）** "。

   ![选择 "配额" 作为问题类型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 选择要增加其配额的订阅。

   ![选择要增加配额的订阅](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 对于 "**配额类型**"，选择 "**计算-VM （个 vcpu）订阅限制增加**"。

   ![选择要增加的配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 选择 "**下一步：** 用于打开**问题详细信息**的解决方案"。 选择 "**提供详细**信息" 以提供其他信息。

   ![提供详细信息来帮助你的请求](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在 "**配额详细信息**" 中，选择 "**经典**" 并选择一个**位置**。

   ![添加详细信息，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 对于 " **sku 系列**"，选择一个或多个要增加的 SKU 系列。

   ![指定要增加的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 输入想对订阅设定的新限制。 若要删除线条，请取消选择**sku 系列**中的 sku，或选择 "丢弃" X 图标。 为每个 SKU 系列输入配额后，请在 "**配额详细信息**" 中选择 "**保存并继续**" 以继续支持请求。

   ![请求新限制](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>使用使用情况 + 配额在订阅级别增加每个 VM 序列的请求 vCPU 配额

按照以下说明使用 Azure 门户中的**使用情况 + 配额**创建支持请求。

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 "**订阅**"。

   ![在 Azure 门户中，请参阅订阅](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 选择要增加其配额的订阅。

   ![选择要修改的订阅](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 选择“使用情况 + 配额”。

   ![选择订阅的使用情况和配额](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角选择“请求增加”。

   ![选择以增加配额](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 选择 "**计算-VM （个 vcpu）订阅限制" 会增加** **配额类型**。

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 选择 "**下一步：** 用于打开**问题详细信息**的解决方案"。 选择 "**提供详细**信息" 以提供其他信息。

   ![提供请求的详细信息](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在 "**配额详细信息**" 中，选择 "**经典**" 和 "**位置**"。

   ![选择配额详细信息，包括部署模型和位置](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. 选择一个或多个 SKU 系列以增加。

   ![选择要增加的 SKU 系列](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. 输入想对订阅设定的新限制。 若要删除线条，请取消选择**sku 系列**中的 sku，或选择 "丢弃" X 图标。 为每个 SKU 系列输入配额后，请在 "**配额详细信息**" 中选择 "**保存并继续**" 以继续支持请求。

   ![输入新配额](./media/resource-manager-core-quotas-request/new-limits-classic.png)

