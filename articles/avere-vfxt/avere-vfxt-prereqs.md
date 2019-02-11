---
title: Avere vFXT 先决条件 - Azure
description: Avere vFXT for Azure 的先决条件
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: 9c3301ba16bfaeb7014658a380e287a36a505be8
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55299198"
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

具有订阅所有者权限的用户应创建 vFXT 群集。 以下操作（以及一些其他操作）需要订阅所有者权限：

* 接受 Avere vFXT 软件的条款
* 创建群集节点访问角色 

如果不希望授予所有者对创建 vFXT 的用户的访问权限，有以下两种解决方法：

* 如果满足以下条件，资源组所有者可以创建群集：

  * 订阅所有者必须[接受 Avere vFXT 软件条款](#accept-software-terms)并[创建群集节点访问角色](#create-the-cluster-node-access-role)。 
  * 必须在资源组内部署所有 Avere vFXT 资源，包括：
    * 群集控制器
    * 群集节点
    * Blob 存储
    * 网络元素
 
* 没有所有者权限的用户可以通过提前使用基于角色的访问控制 (RBAC) 为用户分配权限来创建 vFXT 群集。 此方法为这些用户提供了重要权限。 [本文](avere-vfxt-non-owner.md)介绍了如何创建访问角色来授权非所有者创建群集。

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 群集的配额

以下 Azure 组件必须具有足够的配额。 根据需要[请求增加配额](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

> [!NOTE]
> 此处列出的虚拟机和 SSD 组件适用于 vFXT 群集本身。 需要为计划用于计算场的 VM 和 SSD 追加配额。  确保为要运行工作流的区域启用了配额。

|Azure 组件|Quota|
|----------|-----------|
|虚拟机|至少 3 个 D16s_v3 或 E32s_v3|
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

## <a name="create-access-roles"></a>创建访问角色 

[基于角色的访问控制](../role-based-access-control/index.yml) (RBAC) 授权 vFXT 控制器和群集节点执行必要的任务。

* 群集控制器需要有权创建和修改 VM 才能创建群集。 

* 作为群集常规操作的一部分，各个 vFXT 节点需要执行读取 Azure 资源属性、管理存储和控制其他节点的网络接口设置等操作。

在创建 Avere vFXT 群集之前，必须定义要用于群集节点的自定义角色。 

对于群集控制器，可以接受来自模板的默认角色。 默认角色向群集控制器授予资源组所有者权限。 如果想要为控制器创建自定义角色，请参阅[自定义的控制器访问角色](avere-vfxt-controller-role.md)。

> [!NOTE] 
> 只有订阅所有者或者具有“所有者”或“用户访问管理员”角色的用户可以创建角色。 角色可以提前创建。  

### <a name="create-the-cluster-node-access-role"></a>创建群集节点访问角色

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

必须先创建群集节点角色，然后才能创建 Avere vFXT for Azure 群集。

> [!TIP] 
> Microsoft 内部用户应当使用名为“Avere 群集运行时操作员”的现有角色而非尝试创建一个。 

1. 复制此文件。 在 AssignableScopes 行中添加你的订阅 ID。

   （此文件的当前版本在 github.com/Azure/Avere 存储库中存储为 [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt)。）  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "DataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ]
   }
   ```

1. 将此文件另存为 ``avere-operator.json`` 或容易记住的类似文件名。 


1. 打开 Azure Cloud shell，然后使用你的订阅 ID 进行登录（如[本文档中上文](#accept-software-terms)所述）。 使用此命令以创建角色：

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

创建群集时将使用此角色名称。 在本例中，名称为 ``avere-operator``。

## <a name="next-step-create-the-vfxt-cluster"></a>后续步骤：创建 vFXT 群集

完成这些任务后，就可以开始创建群集。 阅读[部署 vFXT 群集](avere-vfxt-deploy.md)，查看说明。