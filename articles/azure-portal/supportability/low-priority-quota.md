---
title: 点虚拟机配额 - Azure
description: 增加现场 VM 的配额限制，该限制提供了 Azure 使用模型，允许您承担更低的成本，以换取 Azure 根据需要删除 VM。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842750"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>点配额：提高所有 VM 系列的限制

Spot 虚拟机 （VM） 提供了不同的 Azure 使用模型。 它们允许您承担更低的成本，以换取 Azure 根据需要删除虚拟机，以便即用即付或保留 VM 实例部署。 有关现场 VM 的详细信息，请参阅[虚拟机缩放集的 Azure spot VM。](../../virtual-machine-scale-sets/use-spot.md)

Azure 资源管理器支持虚拟机的两种类型的 vCPU 配额：

* *即用即付 VM*和保留*VM 实例*受*标准 vCPU 配额*的约束。
* *现货 VM*受点*vCPU 配额的约束*。

对于现场 vCPU 配额类型，资源管理器 vCPU 配额作为单个区域限制在所有可用的虚拟机系列中强制执行。

每当部署新的点 VM 时，所有点 VM 实例的新和现有 vCPU 使用率不得超过批准的点 vCPU 配额限制。 如果超过点配额，则不允许部署现场 VM。

本文讨论如何通过使用 Azure 门户请求提高当前 vCPU 配额限制。

要了解有关标准 vCPU 配额的更多，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)以及 Azure[订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

要了解如何按区域增加 vCPU 限制，请参阅[标准配额：按区域增加限制](regional-quota-requests.md)。

## <a name="request-a-quota-limit-increase-from-help--support"></a>请求从帮助和支持增加配额限制

请求使用**帮助和支持**的所有虚拟机系列增加配额限制：

> [!NOTE]
> 您还可以通过单个支持案例请求提高多个区域的配额限制。 有关详细信息，请参阅步骤 8。

1. 在[Azure 门户](https://portal.azure.com)菜单中，选择 **"帮助 + 支持**"。

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

   1. 对于**部署模型**，选择适当的模型，并为**位置**，选择位置。

      ![提供其他配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 对于所选位置，在 **"选择类型****"** 下键入 "**点**"。

      ![选择点类型](./media/resource-manager-core-quotas-request/select-spot-type.png)

       在 **"类型"** 下，您可以通过多选择支持从单个支持案例请求标准和点配额类型。

       有关详细信息，请参阅[标准配额：按 VM 系列增加限制](per-vm-quota-requests.md)。

   1. 输入此订阅所需的新配额限制。

      ![为现场 VM 选择新配额](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 要请求多个位置的配额增加，请在 **"位置**"中选择其他位置，然后选择适当的 VM 类型。 然后，您可以输入适用于其他位置的限制。

   ![在配额详细信息中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择 **"保存并继续**创建支持请求"。

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>从"订阅"窗格请求提高配额限制

要从 **"订阅"** 窗格请求增加所有 VM 系列的即期配额限制，请从"订阅"窗格中：

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

1. 选择 **"下一步"：** 打开**问题详细信息的解决方案**。 选择 **"提供详细信息**以输入其他信息"。 在**配额详细信息**中，输入以下信息：

   1. 对于**部署模型**，选择适当的模型，并为**位置**，选择位置。

      ![提供配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 对于所选位置，在 **"选择类型****"** 下键入 "**点**"。

      ![选择点类型](./media/resource-manager-core-quotas-request/select-spot-type.png)

      有关详细信息，请参阅[标准配额：按 VM 系列增加限制](per-vm-quota-requests.md)。

   1. 输入此订阅所需的新配额限制。

      ![输入 vCPU 限制的新值](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 要请求多个位置的配额增加，请在 **"位置**"中选择其他位置，然后选择适当的 VM 类型。 然后，您可以输入适用于其他位置的限制。

   ![在配额详细信息中选择其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择 **"保存并继续**创建支持请求"。
