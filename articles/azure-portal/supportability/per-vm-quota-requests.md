---
title: 请求增加每个 Azure VM 系列的 vCPU 配额限制
description: 如何请求 Azure 门户中 VM 系列的 vCPU 配额限制增加，这会增加相同数量的区域 vCPU 限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843699"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>标准配额：按 VM 序列增加限制

Azure 资源管理器为虚拟机支持两种类型的 vCPU 配额：

* 即*用即付 vm*和*保留 vm 实例*受*标准 vCPU 配额*的限制。
* *污点 vm*服从*点 vCPU 配额*。

对于每个区域中的每个订阅，在两个级别上强制实施即用即付和保留虚拟机实例的标准 vCPU 配额：

* 第一层是所有 VM 系列的*区域个 vcpu 限制总计*。
* 第二层是*每个 VM 系列个 vcpu 限制*，如 Dv3 系列个 vcpu。

每当部署新的点 VM 时，该 VM 系列的新的和现有 vCPU 使用情况不得超过该特定 VM 系列的已批准 vCPU 配额。 此外，在所有 VM 系列上部署的新的和现有的个 vcpu 的总数不应超过订阅的总批准区域 vCPU 配额。 如果超过了其中任一配额，则不允许进行 VM 部署。

可以通过使用 Azure 门户来请求提高 VM 系列的 vCPU 配额限制。 VM 序列配额增加会自动增加相同数量的区域 vCPU 限制。

若要了解有关标准 vCPU 配额的详细信息，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)和[Azure 订阅和服务限制](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)。

若要了解有关为标准配额增加区域的 vCPU 限制的信息，请参阅[标准配额：按区域增加限制](regional-quota-requests.md)。

若要了解有关增加位置 VM vCPU 限制的详细信息，请参阅[污点配额：增加所有 VM 序列的限制](low-priority-quota.md)。

可以通过两种方式中的任一方式请求提高每个 VM 系列的标准 vCPU 配额限制，如以下部分中所述。

## <a name="request-a-standard-quota-increase-from-help--support"></a>从 "帮助 + 支持" 请求标准配额增加

若要从 "**帮助 + 支持**" 中请求每个 VM 系列的标准 vCPU 配额增加：

> [!NOTE]
> 你还可以通过单个支持案例为多个区域请求增加配额限制。 有关详细信息，请参阅步骤8。

1. 在 " [Azure 门户](https://portal.azure.com)" 菜单上，选择 "**帮助 + 支持**"。

   !["帮助 + 支持" 链接](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 "**帮助 + 支持**" 中，选择 "**新建支持请求**"。

    ![创建新的支持请求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制(配额)”。

   ![选择问题类型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 对于 "**订阅**"，请选择要增加其配额的订阅。

   ![选择已增加配额的订阅](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 对于 "**配额类型**"，选择 "**计算-VM （个 vcpu）订阅限制增加**"。

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 选择 "**下一步：** 用于打开**问题详细信息**的解决方案"。 选择 "**提供详细**信息" 以输入其他信息。

   !["提供详细信息" 链接](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. 在 "**配额详细信息**" 中，执行以下步骤：

   ![TProvide 其他配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 对于 "**部署模型**"，选择相应的模型。

   1. 对于 "**位置**"，请选择一个位置。 对于所选位置，请在 "**类型**" 下的 "**选择类型**" 中选择 "**标准**"。

      ![配额详细信息-配额类型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 "**类型**" 下，你可以通过多选支持从单个支持案例请求标准和点配额类型。

      有关增加点配额限制的详细信息，请参阅[用于虚拟机规模集的 Azure 点 vm](../../virtual-machine-scale-sets/use-spot.md)。

   1. 在 "**标准**" 中，选择 SKU 系列以增加配额。

      ![配额详细信息-SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 输入要用于此订阅的新配额限制。 若要从列表中删除 SKU，请清除 SKU 旁边的复选框，或选择 "丢弃 ' X" 图标。

      ![选择新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 若要为多个位置请求增加配额，请在 "**位置**" 中选择其他位置，然后选择适当的 VM 类型。 然后，可以输入应用于其他位置的限制。

   ![在配额详细信息中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择 "**保存并继续**" 继续创建支持请求。

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>请求从订阅增加标准配额

请求**订阅**中每个 VM 系列的标准 vCPU 配额增加：

> [!NOTE]
> 你还可以通过单个支持案例为多个区域请求增加配额限制。 有关详细信息，请参阅步骤7。

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 "**订阅**"。

   ![Azure 门户搜索中的订阅](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 选择要增加其配额的订阅。

   ![要为更改选择的订阅](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左窗格中，选择 "**使用情况 + 配额**"。

   !["使用情况 + 配额" 链接](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角，选择 "**请求增加**"。

   ![选择以增加配额](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 对于 "**配额类型**"，选择 "**计算-VM （个 vcpu）订阅限制增加**"。

   ![选择配额类型](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. 在 "**配额详细信息**" 中，执行以下步骤：

   1. 对于 "**部署模型**"，选择相应的模型，并选择 "**位置"。**

      ![提供配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. 对于所选位置，请在 "**类型**" 下选择 "**选择类型**"，然后选择 "**标准**"。

      ![选择标准类型](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      在 "**类型**" 下，你可以通过多选支持从单个支持案例请求标准和点配额类型。

      有关增加点配额限制的详细信息，请参阅[用于虚拟机规模集的 Azure 点 vm](../../virtual-machine-scale-sets/use-spot.md)。

   1. 对于 "**标准**"，请选择要增加其配额的 SKU 系列。

      ![配额详细信息-SKU 系列](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. 输入要用于此订阅的新配额限制。 若要从列表中删除 SKU，请取消选中 SKU 旁边的复选框，或选择 "丢弃 ' X" 图标。

      ![选择新的 vCPU 限制](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. 若要为多个位置请求增加配额，请在 "**位置**" 中选择其他位置，然后选择适当的 VM 类型。

   此步骤预加载您为之前的位置选择的 SKU 系列。 输入要应用于其他序列的配额限制。

   ![在配额详细信息中选择其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择 "**保存并继续**" 继续创建支持请求。
