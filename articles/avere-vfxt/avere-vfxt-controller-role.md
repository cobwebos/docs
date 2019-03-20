---
title: 自定义的控制器访问角色 - Avere vFXT for Azure
description: 本文介绍如何为 Avere vFXT 群集控制器创建自定义访问角色
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: c408efa7ca01928e25ac03f5ca63d0aef7d88839
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770025"
---
# <a name="customized-controller-access-role"></a>自定义的控制器访问角色

Avere vFXT for Azure 群集控制器使用托管标识和基于角色的访问控制 (RBAC) 以允许其创建和管理群集。 

默认情况下，为群集控制器分配[内置所有者角色](../role-based-access-control/built-in-roles.md#owner)。 此外，控制器的访问范围限定为其资源组，这样它就无法修改群集资源组之外的元素。

本文介绍如何为群集控制器创建自己的访问角色，而不是使用默认设置。 

## <a name="edit-the-role-prototype"></a>编辑角色原型

从 <https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereContributor.txt> 提供的原型角色开始。

```json
{
  "AssignableScopes": [
    "/subscriptions/YOUR SUBSCRIPTION ID HERE"
  ],
  "Name": "Avere custom contributor",
  "IsCustom": true,
  "Description": "Can create and manage an Avere vFXT cluster.",
  "NotActions": [],
  "Actions": [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/disks/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/*/read",
    "Microsoft.Network/networkInterfaces/*",
    "Microsoft.Network/virtualNetworks/subnets/join/action",
    "Microsoft.Network/virtualNetworks/subnets/read",
    "Microsoft.Resources/deployments/*",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Support/*"
  ],
  "DataActions": []
}
```

在 AssignableScopes 语句中添加 Avere vFXT for Azure 部署的订阅 ID。 根据需要自定义名称并添加或更改定义。 

在限制权限时请小心谨慎。 如果控制器没有足够的访问权限，群集创建可能会失败。 

若要了解群集控制器创建群集所需的权限，请[打开支持票证](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)以获取帮助。 

将自定义角色定义另存为 .json 文件。 

## <a name="define-the-role"></a>定义角色 

请按照以下步骤将自定义角色定义添加到订阅中。 

1. 在 Azure 门户中打开 Azure Cloud Shell 或浏览到 [https://shell.azure.com](https://shell.azure.com)。

1. 使用 Azure CLI 命令切换到 vFXT 订阅：

   ```azurecli
   az account set --subscription YOUR_SUBSCRIPTION_ID
   ```

1. 创建角色：

   ```azurecli
   az role definition create --role-definition /avere-contributor-custom.json
   ```

   使用你的文件名和路径替代本示例中的 ```/avere-contributor-custom.json```。 

保存角色定义命令的输出，它包含需要提供给群集创建模板的角色标识符。 

## <a name="find-the-role-id"></a>查找角色 ID

Avere vFXT 部署模板需要角色的全局唯一标识符 (GUID) 来为控制器分配一个自定义角色。 

角色 GUID 是一个包含 32 个字符的字符串，格式如下：8e3af657-a8ff-443c-a75c-2fe8c4bcb635

要查找角色的 GUID，请使用以下命令，并在 ```--name``` 参数中使用你的角色名称。

```azurecli
az role definition list --query '[*].{roleName:roleName, name:name}' -o table --name 'YOUR ROLE NAME'
```
部署 Avere vFXT for Azure 时，在“Avere 群集创建角色 ID”字段中输入此字符串。

## <a name="next-steps"></a>后续步骤

阅读[部署 vFXT 群集](avere-vfxt-deploy.md)，了解如何部署 Avere vFXT for Azure
