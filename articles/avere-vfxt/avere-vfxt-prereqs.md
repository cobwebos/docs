---
title: Avere vFXT 先决条件 - Azure
description: Avere vFXT for Azure 的先决条件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 0dafef7cf262153ccdb3b490aa0c7bd039b4a89b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889171"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>准备创建 Avere vFXT

本文介绍在创建 Avere vFXT 群集前必须完成的任务。

## <a name="create-a-new-subscription"></a>创建新订阅

首先创建一个新的 Azure 订阅。 为每个 Avere vFXT 项目使用单独的订阅，可以轻松跟踪所有的项目资源和费用，使其他项目在预配期间不受可能的资源限制，并简化清理过程。

在 Azure 门户中创建新的 Azure 订阅：

* 导航到[订阅边栏选项卡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* 单击顶部的“+ 添加”按钮
* 如果提示，请登录
* 选择套餐，逐步完成创建新订阅的各个步骤

## <a name="configure-subscription-owner-permissions"></a>配置订阅所有者权限

具有订阅所有者权限的用户应创建 vFXT 群集。 需要订阅所有者权限才能接受软件条款和执行其他操作。

有一些解决方法方案允许非所有者创建 Azure 群集的 Avere vFTX。 这些方案涉及限制资源并为创建者分配其他角色。 在这两种情况下，订阅所有者还必须提前[接受 Avere vFXT 软件条款](#accept-software-terms)。

| 方案 | 限制 | 创建 Avere vFXT 群集所需的访问角色 |
|----------|--------|-------|
| 资源组管理员 | 必须在资源组中创建虚拟网络、群集控制器和群集节点 | "[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)" 和 "[参与者](../role-based-access-control/built-in-roles.md#contributor)" 角色，作用域为目标资源组 |
| 外部虚拟网络 | 群集控制器和群集节点在资源组中创建，但使用不同资源组中的现有虚拟网络 | （1） "[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)" 和 "[参与者](../role-based-access-control/built-in-roles.md#contributor)" 角色的作用域为 vFXT 资源组;和（2）[虚拟机参与者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)、[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)和[Avere 参与者](../role-based-access-control/built-in-roles.md#avere-contributor)角色，作用域为虚拟网络的资源组。 |

一种替代方法是提前创建自定义的基于角色的访问控制（RBAC）角色，并为用户分配权限，如[本文](avere-vfxt-non-owner.md)所述。 此方法为这些用户提供了重要权限。

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 群集的配额

以下 Azure 组件必须具有足够的配额。 根据需要[请求增加配额](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。

> [!NOTE]
> 此处列出的虚拟机和 SSD 组件适用于 vFXT 群集本身。 需要为计划用于计算场的 VM 和 SSD 追加配额。  确保为要运行工作流的区域启用了配额。

|Azure 组件|配额|
|----------|-----------|
|虚拟机|3 个或更多个 E32s_v3|
|高级 SSD 存储|200 GB OS 空间加上每个节点的 1 TB 到 4 TB 缓存空间 |
|存储帐户（可选） |v2|
|数据后端存储（可选） |一个新的 LRS Blob 容器 |

## <a name="accept-software-terms"></a>接受软件条款

> [!NOTE]
> 如果订阅所有者创建 Avere vFXT 群集，则不需要此步骤。

在创建群集期间，必须接受 Avere vFXT 软件的服务条款。 如果你不是订阅所有者，请让订阅所有者提前接受条款。 每个订阅只需执行一次此步骤。

提前接受软件条款：

1. 在 Azure 门户中打开 Cloud Shell 或浏览到 <https://shell.azure.com>。 使用订阅 ID 登录。

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. 发出此命令以接受服务条款并启用 Avere vFXT for Azure 软件映像的编程访问：

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>在虚拟网络中创建存储服务终结点（如果需要）

[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)将 Azure Blob 通信保留为本地，而不是将其路由到虚拟网络之外。 建议为 Azure 群集使用 Azure Blob 进行后端数据存储的任何 Avere vFXT。

如果在群集创建过程中提供现有虚拟网络并为后端存储创建新的 Azure Blob 容器，则必须在网络中有一个用于 Microsoft 存储的服务终结点。 此终结点必须在创建群集之前存在，否则创建将失败。

建议为使用 Azure Blob 存储的 Azure 群集的任何 Avere vFXT 提供存储服务终结点，即使稍后添加存储也是如此。

> [!TIP]
>
>* 如果要在创建群集的过程中创建新的虚拟网络，请跳过此步骤。
>* 如果在创建群集的过程中未创建 Blob 存储，则此步骤是可选的。 在这种情况下，如果决定使用 Azure Blob，则可以在以后创建服务终结点。

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

完成这些任务后，就可以开始创建群集。 阅读[部署 vFXT 群集](avere-vfxt-deploy.md)，查看说明。
