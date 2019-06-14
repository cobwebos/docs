---
title: Avere vFXT 先决条件 - Azure
description: Avere vFXT for Azure 的先决条件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409228"
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

具有订阅所有者权限的用户应创建 vFXT 群集。 需要订阅所有者权限才能接受软件服务条款并执行其他操作。 

有一些解决方法方案来使非所有者来创建 Azure 群集 Avere vFTX。 这些方案都涉及限制资源并将其他角色分配给创建者。 在这种情况下，订阅所有者还必须[接受 Avere vFXT 软件条款](#accept-software-terms)提前。 

| 场景 | 限制 | 若要创建 Avere vFXT 群集所需的访问角色 | 
|----------|--------|-------|
| 资源组管理员 | 必须在资源组中创建虚拟网络、 群集控制器和群集节点 | [用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)并[参与者](../role-based-access-control/built-in-roles.md#contributor)角色，同时仅对目标资源组 | 
| 外部 vnet | 资源组中创建的群集控制器和群集节点，但使用不同的资源组中的现有虚拟网络 | （1)[用户访问管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)并[参与者](../role-based-access-control/built-in-roles.md#contributor)vFXT 资源组; 和 (2) 作为作用域角色[虚拟机参与者](../role-based-access-control/built-in-roles.md#virtual-machine-contributor)，[用户访问权限管理员](../role-based-access-control/built-in-roles.md#user-access-administrator)，并[Avere 参与者](../role-based-access-control/built-in-roles.md#avere-contributor)角色作用域为 VNET 资源组。 |
 
一种替代方法是创建自定义基于角色的访问控制 (RBAC) 角色提前，并将权限分配给用户，如中所述[这篇文章](avere-vfxt-non-owner.md)。 此方法为这些用户提供了重要权限。 

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 群集的配额

以下 Azure 组件必须具有足够的配额。 根据需要[请求增加配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

> [!NOTE]
> 此处列出的虚拟机和 SSD 组件适用于 vFXT 群集本身。 需要为计划用于计算场的 VM 和 SSD 追加配额。  确保为要运行工作流的区域启用了配额。

|Azure 组件|Quota|
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

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>在虚拟网络 （如果需要） 中创建存储服务终结点

一个[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)使 Azure Blob 流量本地而不是虚拟网络外部路由。 建议使用 Azure Blob 存储后端数据的 Azure 群集任何 Avere vFXT。 

如果你要提供现有的 vnet 和群集创建过程中创建新的 Azure Blob 容器的后端存储，你必须为 Microsoft 存储在 vnet 中具有服务终结点。 创建群集之前，必须存在此终结点或创建将失败。 

存储服务终结点适用于使用 Azure Blob 存储的 Azure 群集任何 Avere vFXT，即使将存储添加更高版本。 

> [!TIP] 
> * 如果要为群集创建过程中创建新的虚拟网络，请跳过此步骤。 
> * 此步骤是可选不是在群集创建期间创建 Blob 存储。 在这种情况下，您可以更高版本创建的服务终结点，如果您决定使用 Azure Blob。

从 Azure 门户创建存储服务终结点。 

1. 在门户中，单击左侧的“虚拟网络”  。
1. 选择群集的 vnet。 
1. 单击左侧的“服务终结点”  。
1. 单击顶部的“添加”  。
1. 将服务作为``Microsoft.Storage``选择群集的子网。
1. 在底部单击“添加”  。

   ![带注释的 Azure 门户屏幕截图，注释标明创建服务终结点的步骤](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>后续步骤：创建 vFXT 群集

完成这些任务后，就可以开始创建群集。 阅读[部署 vFXT 群集](avere-vfxt-deploy.md)，查看说明。