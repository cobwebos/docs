---
title: 点虚拟机配额-Azure
description: 增加对污点 Vm 的配额限制，提供 Azure 使用情况模型，使你可以在 exchange 中承担更低的成本，让 Azure 根据需要删除 Vm。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842750"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>污点配额：增加所有 VM 序列的限制

点虚拟机（Vm）提供了不同的 Azure 使用模型。 它们可让你在 exchange 中承担更低的成本，让 Azure 根据即用即付或保留 VM 实例部署需要删除虚拟机。 有关污点 Vm 的详细信息，请参阅[用于虚拟机规模集的 Azure 点 vm](../../virtual-machine-scale-sets/use-spot.md)。

Azure 资源管理器为虚拟机支持两种类型的 vCPU 配额：

* 即*用即付 vm*和*保留 vm 实例*受*标准 vCPU 配额*的限制。
* *污点 vm*服从*点 vCPU 配额*。

对于污点 vCPU 配额类型，将在所有可用的虚拟机系列上强制实施资源管理器 vCPU 配额，作为单个区域限制。

每当部署新的点 VM 时，所有查找点 VM 实例的新的和现有 vCPU 使用情况不得超过已批准的点 vCPU 配额限制。 如果超出了污点配额，则不允许使用点 VM 部署。

本文介绍如何使用 Azure 门户请求增加位置 vCPU 配额限制。

若要了解有关标准 vCPU 配额的详细信息，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)、 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

若要了解有关按区域增加 vCPU 限制的信息，请参阅[标准配额：按区域增加限制](regional-quota-requests.md)。

## <a name="request-a-quota-limit-increase-from-help--support"></a>请求从 "帮助 + 支持" 增加配额限制

使用 "**帮助 + 支持**" 为所有虚拟机系列请求一个点配额限制增加：

> [!NOTE]
> 你还可以通过单个支持案例为多个区域请求增加配额限制。 有关详细信息，请参阅步骤8。

1. 从 " [Azure 门户](https://portal.azure.com)" 菜单中，选择 "**帮助 + 支持**"。

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

   1. 对于 "**部署模型**"，选择相应的模型，并选择 "**位置"。**

      ![提供额外的配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 对于所选位置，请在 "**类型**" 下的 "**选择类型**" 中选择 "**点**"。

      ![选择点类型](./media/resource-manager-core-quotas-request/select-spot-type.png)

       在 "**类型**" 下，你可以通过多选支持从单个支持案例请求标准和点配额类型。

       有关详细信息，请参阅[标准配额：按 VM 序列增加限制](per-vm-quota-requests.md)。

   1. 输入要用于此订阅的新配额限制。

      ![为污点 VM 选择新配额](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 若要为多个位置请求增加配额，请在 "**位置**" 中选择其他位置，然后选择适当的 VM 类型。 然后，可以输入应用于其他位置的限制。

   ![在配额详细信息中指定其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择 "**保存并继续**" 继续创建支持请求。

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>从 "订阅" 窗格中请求增加配额限制

若要从 "**订阅**" 窗格中为所有 VM 序列请求一个点配额限制：

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

1. 选择 "**下一步：** 用于打开**问题详细信息**的解决方案"。 选择 "**提供详细**信息" 以输入其他信息。 在 "**配额详细信息**" 中，输入以下信息：

   1. 对于 "**部署模型**"，选择相应的模型，并选择 "**位置"。**

      ![提供配额详细信息](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. 对于所选位置，请在 "**类型**" 下的 "**选择类型**" 中选择 "**点**"。

      ![选择点类型](./media/resource-manager-core-quotas-request/select-spot-type.png)

      有关详细信息，请参阅[标准配额：按 VM 序列增加限制](per-vm-quota-requests.md)。

   1. 输入要用于此订阅的新配额限制。

      ![为 vCPU 限制输入新值](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. 若要为多个位置请求增加配额，请在 "**位置**" 中选择其他位置，然后选择适当的 VM 类型。 然后，可以输入应用于其他位置的限制。

   ![在配额详细信息中选择其他位置](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. 选择 "**保存并继续**" 继续创建支持请求。
