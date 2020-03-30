---
title: 请求提高每个 Azure VM 系列的 vCPU 配额限制
description: 如何在 Azure 门户中请求增加 VM 系列的 vCPU 配额限制，这将区域 vCPU 总限制增加相同的数量。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843699"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>标准配额：按 VM 系列增加限制

Azure 资源管理器支持虚拟机的两种类型的 vCPU 配额：

* *即用即付 VM*和保留*VM 实例*受*标准 vCPU 配额*的约束。
* *现货 VM*受点*vCPU 配额的约束*。

即用即付和保留虚拟机实例的标准 vCPU 配额在每个区域中的每个订阅的两层执行：

* 第一层是所有 VM 系列*的区域 vCPU 限制*。
* 第二层是*每个 VM 系列 vCPU 限制*，例如 Dv3 系列 vCPU。

每当部署新的 spot VM 时，该 VM 系列的新 vCPU 和现有 vCPU 总使用量不得超过该特定 VM 系列的已批准的 vCPU 配额。 此外，在所有 VM 系列中部署的新 vCPU 和现有 vCPU 的总数不应超过订阅的已批准区域 vCPU 配额总数。 如果超过这些配额之一，则不允许 VM 部署。

可以使用 Azure 门户请求提高 VM 系列的 vCPU 配额限制。 VM 系列配额的增加会自动将区域 vCPU 总限制增加相同的数量。

要了解有关标准 vCPU 配额的更多，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)以及 Azure[订阅和服务限制](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)。

要了解如何按区域增加标准配额的 vCPU 限制，请参阅[标准配额：按区域增加限制](regional-quota-requests.md)。

要了解有关增加 spot VM vCPU 限制的更多，请参阅[Spot 配额：增加所有 VM 系列的限制](low-priority-quota.md)。

您可以通过以下两种方式之一请求提高每个 VM 系列的标准 vCPU 配额限制，如以下各节所述。

## <a name="request-a-standard-quota-increase-from-help--support"></a>请求从帮助和支持增加标准配额

要请求标准 vCPU 配额增加每个 VM 系列从**帮助 + 支持**：

> [!NOTE]
> 您还可以通过单个支持案例请求提高多个区域的配额限制。 有关详细信息，请参阅步骤 8。

1. 在[Azure 门户](https://portal.azure.com)菜单上，选择 **"帮助 + 支持**"。

   !["帮助+支持"链接](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 **"帮助+支持"** 中，选择 **"新建支持请求**"。

    ![创建新的支持请求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制(配额)”********。

   ![选择问题类型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 对于**订阅**，选择要增加其配额的订阅。

   ![为增加的配额选择订阅](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 对于**配额类型**，选择**计算 VM（核心-vCPU）订阅限制增加**。

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 选择 **"下一步"：** 打开**问题详细信息的解决方案**。 选择 **"提供详细信息**以输入其他信息"。

   !["提供详细信息"链接](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在**配额详细信息**中，执行以下步骤：

   ![T 提供其他配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 对于**部署模型**，请选择相应的模型。

   1. 对于 **"位置**"，请选择位置。 对于所选位置，在 **"选择类型****"** 下，选择 **"标准**"。

      ![配额详细信息 - 配额类型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 **"类型"** 下，您可以通过多选择支持从单个支持案例请求标准和点配额类型。

      有关增加点配额限制的详细信息，请参阅[虚拟机缩放集的 Azure spot VM。](../../virtual-machine-scale-sets/use-spot.md)

   1. 在 **"标准"** 中，选择 SKU 系列以增加配额。

      ![配额详细信息 - SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 输入此订阅所需的新配额限制。 要从列表中删除 SKU，请清除 SKU 旁边的复选框，或选择丢弃的"X"图标。

      ![选择新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 要请求多个位置的配额增加，请在 **"位置**"中选择其他位置，然后选择适当的 VM 类型。 然后，您可以输入适用于其他位置的限制。

   ![在配额详细信息中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择 **"保存并继续**创建支持请求"。

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>请求从订阅中增加标准配额

要请求从**订阅**中每个 VM 系列增加标准 vCPU 配额：

> [!NOTE]
> 您还可以通过单个支持案例请求提高多个区域的配额限制。 有关详细信息，请参阅步骤 7。

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 **"订阅**"。

   ![Azure 门户搜索中的订阅](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 选择要增加其配额的订阅。

   ![要为更改选择的订阅](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左侧窗格中，选择 **"使用情况 + 配额**"。

   !["使用 + 配额"链接](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角，选择 **"请求增加**"。

   ![选择增加配额](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 对于**配额类型**，选择**计算 VM（核心-vCPU）订阅限制增加**。

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 在**配额详细信息**中，执行以下步骤：

   1. 对于**部署模型**，选择适当的模型，并为**位置**，选择位置。

      ![提供配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 对于所选位置，在 **"类型"** 下选择 **"选择类型**"，然后选择 **"标准**"。

      ![选择标准类型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 **"类型"** 下，您可以通过多选择支持从单个支持案例请求标准和点配额类型。

      有关增加点配额限制的详细信息，请参阅[虚拟机缩放集的 Azure spot VM。](../../virtual-machine-scale-sets/use-spot.md)

   1. 对于**标准**，选择要增加其配额的 SKU 系列。

      ![配额详细信息 - SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 输入此订阅所需的新配额限制。 要从列表中删除 SKU，请取消选中 SKU 旁边的复选框或选择丢弃的"X"图标。

      ![选择新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 要请求多个位置的配额增加，请在 **"位置**"中选择其他位置，然后选择适当的 VM 类型。

   此步骤预加载为早期位置选择的 SKU 系列。 输入要应用于其他系列的配额限制。

   ![在配额详细信息中选择其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择 **"保存并继续**创建支持请求"。
