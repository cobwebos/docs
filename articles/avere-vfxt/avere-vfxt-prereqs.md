---
title: Avere vFXT 先决条件 - Azure
description: Avere vFXT for Azure 的先决条件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 7f89ea553bc7198c1faee5ba3549f88da5ec2b2c
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152980"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>准备创建 Avere vFXT

本文介绍创建 Avere vFXT 群集的先决条件任务。

## <a name="create-a-new-subscription"></a>创建新订阅

首先创建一个新的 Azure 订阅。 为每个 Avere vFXT 项目使用单独的订阅，以简化支出跟踪和清理，并确保在预配群集工作流时，其他项目不受配额或资源限制的影响。

在 Azure 门户中创建新的 Azure 订阅：

* 导航到[订阅边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* 单击顶部的“+ 添加”按钮
* 如果提示，请登录
* 选择套餐，逐步完成创建新订阅的各个步骤

## <a name="configure-subscription-owner-permissions"></a>配置订阅所有者权限

具有订阅所有者权限的用户应创建 vFXT 群集。 群集创建需要所有者接受软件条款，并授权对网络和存储资源进行更改。

有一些解决方法可允许非所有者创建 Azure 群集的 Avere vFXT。 这些方案涉及限制资源并为创建者分配其他基于角色的访问控制（RBAC）角色。 在所有这些情况下，订阅所有者还必须提前[接受 Avere vFXT 软件条款](#accept-software-terms)。

| 方案 | 限制 | 创建 Avere vFXT 群集所需的访问角色 |
|----------|--------|-------|
| 资源组管理员创建 vFXT | 必须在资源组中创建虚拟网络、群集控制器和群集节点。 | "[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)" 和 "[参与者](../role-based-access-control/built-in-roles.md#contributor)" 角色，作用域为目标资源组。 |
| 使用现有的外部虚拟网络 | 群集控制器和群集节点是在 vFXT 的资源组中创建的，但使用其他资源组中的现有虚拟网络。 | （1） "[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)" 和 "[参与者](../role-based-access-control/built-in-roles.md#contributor)" 角色的作用域为 vFXT 资源组;和（2）[虚拟机参与者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)、[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)和[Avere 参与者](../role-based-access-control/built-in-roles.md#avere-contributor)角色，作用域为虚拟网络的资源组。 |
| 群集创建者的自定义角色 | 无资源放置限制。 此方法提供非所有者重要权限。 | 订阅所有者创建自定义 RBAC 角色，如[本文](avere-vfxt-non-owner.md)所述。 |

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 群集的配额

检查以下 Azure 组件是否有足够的配额。 根据需要[请求增加配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

> [!NOTE]
> 此处列出的虚拟机和 SSD 组件适用于 vFXT 群集本身。 请记住，你还需要将用于计算场的 Vm 和 Ssd 的配额。
>
> 确保为要运行工作流的区域启用了配额。

|Azure 组件|配额|
|----------|-----------|
|虚拟机|3个或更多 E32s_v3 （每个群集节点一个） |
|高级 SSD 存储|200 GB OS 空间加上每个节点的 1 TB 到 4 TB 缓存空间 |
|存储帐户（可选） |v2|
|数据后端存储（可选） |一个新的 LRS Blob 容器 |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>接受软件条款

> [!TIP]
> 如果订阅所有者将创建 Avere vFXT 群集，请跳过此步骤。

在创建群集期间，必须接受 Avere vFXT 软件的服务条款。 如果你不是订阅所有者，请让订阅所有者提前接受条款。

每个订阅只需执行一次此步骤。

提前接受软件条款：

1. 在 Azure 门户中打开 Cloud Shell 或浏览到 <https://shell.azure.com>。 使用订阅 ID 登录。

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. 发出此命令以接受服务条款并启用 Avere vFXT for Azure 软件映像的编程访问：

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>在虚拟网络中创建存储服务终结点（如果需要）

[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)将 Azure Blob 通信保留为本地，而不是将其路由到虚拟网络之外。 建议为 Azure 群集使用 Azure Blob 进行后端数据存储的任何 Avere vFXT。

如果在创建群集时创建新的虚拟网络，则会自动创建一个终结点。 如果你提供现有虚拟网络，则在创建群集期间，如果要创建新的 Blob 存储容器，则它必须具有 Microsoft 存储服务终结点。<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* 如果要在创建群集的过程中创建新的虚拟网络，请跳过此步骤。
>* 如果在群集创建过程中未创建 Blob 存储，则终结点是可选的。 在这种情况下，如果决定使用 Azure Blob，则可以在以后创建服务终结点。

从 Azure 门户创建存储服务终结点。

1. 在门户中，打开虚拟网络列表。
1. 选择群集的虚拟网络。
1. 在左侧菜单中单击 "**服务终结点**"。
1. 单击顶部的“添加”。
1. 选择服务 ``Microsoft.Storage``。
1. 选择群集的子网。
1. 在底部单击“添加”。

   ![带注释的 Azure 门户屏幕截图，注释标明创建服务终结点的步骤](media/avere-vfxt-service-endpoint.png)

## <a name="next-step-create-the-vfxt-cluster"></a>下一步：创建 vFXT 群集

完成这些先决条件后，即可创建群集。 阅读[部署 vFXT 群集](avere-vfxt-deploy.md)，查看说明。
