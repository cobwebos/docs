---
title: Avere vFXT 非所有者解决方法 - Azure
description: 允许不具备订阅所有者权限的用户部署适用于 Azure 的 Avere vFXT 的解决方法
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: e72e6d969649de09389ee38b94e874fad98ee08f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409203"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>授权非所有者部署 Avere vFXT

这些说明是一种解决方法，允许不具备订阅所有者特权的用户为 Azure 系统创建 Avere vFXT。

（部署 Avere vFXT 系统的推荐方法是让拥有所有者权限的用户执行创建步骤，如[准备创建 Avere vFXT](avere-vfxt-prereqs.md) 中所述。）  

解决方法涉及创建其他访问角色，为其用户提供足够的权限来安装群集。 该角色必须由订阅所有者创建，并且所有者必须将其分配给适当的用户。 

订阅所有者还必须[接受 Avere vFXT 市场映像的使用条款](avere-vfxt-prereqs.md)。 

> [!IMPORTANT] 
> 所有这些不走必须由对用于群集的订阅具有所有者特权的用户执行。

1. 复制这些行并将其保存在文件中（例如，`averecreatecluster.json`）。 在 `AssignableScopes` 语句中使用订阅 ID。

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",
   
           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. 运行此命令以创建角色：

   `az role definition create --role-definition <PATH_TO_FILE>`

    示例：
    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. 将此角色分配给将创建群集的用户：

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

完成此过程后，分配了此角色的任何用户均对订阅有下列权限： 

* 创建和配置网络基础结构
* 创建群集控制器
* 在群集控制器中运行群集创建脚本以创建群集
