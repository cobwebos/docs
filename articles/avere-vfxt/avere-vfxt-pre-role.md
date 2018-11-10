---
title: 在没有控制器的情况下创建 Avere 角色 - 适用于 Azure 的 Avere vFXT
description: 在没有群集控制器 VM 的情况下创建所需 RBAC 角色的方法
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 547a0e0ac5254408a4064d627ec4c1e7c354a433
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50670024"
---
# <a name="create-the-avere-vfxt-cluster-runtime-access-role-without-a-controller"></a>在没有控制器的情况下创建 Avere vFXT 群集运行时访问角色

本文档介绍了在创建群集控制器 VM 之前从命令行创建集群节点访问角色的方法。 

要从群集控制器创建它，请阅读[创建群集节点访问角色 ](avere-vfxt-deploy.md#create-the-cluster-node-access-role)。 控制器映像包括角色原型文件。 可使用订阅 ID 更新文件，并使用该文件在控制器 VM 上本地定义角色。

## <a name="create-an-azure-rbac-role"></a>创建 Azure RBAC 角色

Avere vFXT 系统使用[基于角色的访问控制 ](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) 授权 vFXT 群集节点执行必要的任务。  

作为 vFXT 群集常规操作的一部分，各个 vFXT 节点需要执行读取 Azure 资源属性、管理存储和控制其他节点的网络接口设置等操作。 

此角色仅用于 vFXT 节点，不用于群集控制器 VM。  

如果要在创建控制器之前创建角色，请按照下列步骤操作： 

1. 在 Azure 门户中打开 Azure Cloud Shell 或浏览到 [https://shell.azure.com](https://shell.azure.com)。

1. 使用 Azure CLI 命令切换到 vFXT 订阅：

   ```az account set --subscription YOUR_SUBSCRIPTION_ID```

1. 使用这些命令从市场映像下载角色定义并进行编辑。 

   ```bash
   wget -O- https://averedistribution.blob.core.windows.net/public/vfxtdistdoc.tgz | tar zxf - avere-cluster.json
   vi avere-cluster.json
   ``` 

4. 编辑文件以包含订阅 ID，并删除其上方的行。 将文件另存为 ``avere-cluster.json``。

   ![控制台文本编辑器，显示订阅 ID 和要删除的“删除此行”](media/avere-vfxt-edit-role.png)

5. 创建角色：  

   ```bash
   az role definition create --role-definition /avere-cluster.json
   ```

创建群集时，请提供角色的名称（在本例中为 `avere-cluster`）。 群集创建脚本会将角色分配给新创建的群集节点。 

## <a name="sample-cluster-node-runtime-role-definition"></a>示例集群节点运行时角色定义

> [!IMPORTANT] 
> 此示例定义取自产品的预公开发行版本。 如果通过当前产品分发获得的版本不同，请改用该版本。

```json

{
    "AssignableScopes": [
        "/subscriptions/YOUR_SUBSCRIPTION_ID_GOES_HERE"
    ],
    "Name": "avere-cluster",
    "IsCustom": "true",
    "Description": "Avere cluster runtime role",
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