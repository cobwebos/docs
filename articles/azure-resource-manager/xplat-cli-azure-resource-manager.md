---
title: 使用 Azure CLI 管理资源 | Microsoft Docs
description: 使用 Azure 命令行接口 (CLI) 管理 Azure 资源和组
editor: ''
manager: timlt
documentationcenter: ''
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: 90dd1b6b7e65178f6b339e4ac0bb781fb74a25a6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>使用 Azure CLI 管理 Azure 资源和资源组

本文介绍如何使用 Azure CLI 和 Azure 资源管理器管理解决方案。 如果不熟悉 Resource Manager，请参阅 [Resource Manager 概述](resource-group-overview.md)。 本文重点介绍管理任务。 将能够：

1. 创建资源组
2. 将资源添加到资源组
3. 向资源添加标记
4. 根据名称或标记值查询资源
5. 向资源应用和删除锁
6. 删除资源组

本文不演示如何将 Resource Manager 模板部署到订阅。 有关该信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](resource-group-template-deploy-cli.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本地安装和使用 CLI，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。

## <a name="set-subscription"></a>设置订阅

如果有多个订阅，可切换到其他订阅。 首先，请看帐户的所有订阅。

```azurecli-interactive
az account list
```

它将返回已启用和已禁用订阅的列表。

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

请注意，一个订阅已标记为默认。 此订阅是操作的当前上下文。 若要切换到其他订阅，请使用 **az account set** 命令提供订阅名称。

```azurecli-interactive
az account set -s "Example Subscription Two"
```

若要显示当前订阅上下文，请使用不带参数的 **az account show**：

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>创建资源组

必须先创建将包含资源的资源组，才能向订阅部署任何资源。

若要创建资源组，请使用 **az group create** 命令。 该命令使用 **name** 参数指定资源组的名称，并使用 **location** 参数指定其位置。

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

输入格式如下：

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

如果稍后需要检索资源组，请使用以下命令：

```azurecli-interactive
az group show --name TestRG1
```

若要获取订阅中的所有资源组，请使用：

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>将资源添加到资源组

要将资源添加到资源组中，可使用 **az resource create** 命令或特定于要创建的资源类型的命令（例如 **az storage account create**）。 使用特定于资源类型的命令可能更轻松，因为它包含新资源所需属性的参数。 要使用 **az resource create**，必须了解不会提示而要设置的所有属性。

但是，通过脚本添加资源可能导致将来出现混乱，因为新的资源不存在于资源管理器模板中。 通过模板，可以可靠地重复部署解决方案。

以下命令创建存储帐户。 请勿使用示例所示的名称，而是为存储帐户提供唯一名称。 此名称必须为 3 到 24 个字符，只能使用数字和小写字母。 如果使用示例所示名称，将收到错误，因为该名称被使用。

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

如果稍后需要检索此资源，请使用以下命令：

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>添加标记

标记可用于根据属性组织资源。 例如，可能有不同资源组中的多项资源属于同一部门。 可对这些资源应用部门标签和值，将其标记为属于同一类别。 也可标记资源是用于生产环境还是测试环境。 在本文中，只对一项资源应用标记，但在环境中最好向所有资源应用标记。

以下命令将向存储帐户应用两个标记：

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

各个标记作为单个对象更新。 若要向已包含标记的资源添加标记，请首先检索现有标记。 将新标记添加到包含现有标记的对象，并将所有标记重新应用到资源。

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>搜索资源

使用 **az resource list** 命令可按不同搜索条件检索资源。

* 若要按名称获取资源，请提供 **name** 参数：

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* 若要获取资源组中的所有资源，请提供 **resource-group** 参数：

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* 若要获取具有某个标记名称和值的所有资源，请提供 **tag** 参数：

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* 若要获取具有特定资源类型的所有资源，请提供 **resource-type** 参数：

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>获取资源 ID

很多命令采用资源 ID 作为参数。 若要获取资源 ID 并将其存储在变量中，请使用：

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>锁定资源

需要确保不会意外删除或修改关键资源时，请对资源应用锁定。 可指定 **CanNotDelete** 或 **ReadOnly**。

若要创建或删除管理锁，必须有权执行 `Microsoft.Authorization/*` 或 `Microsoft.Authorization/locks/*` 操作。 在内置角色中，只有“所有者”和“用户访问管理员”有权执行这些操作。

若要应用锁定，请使用以下命令：

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

上例中，在删除锁之前，无法删除锁定的资源。 若要删除所，请使用：

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

有关设置锁的详细信息，请参阅[使用 Azure 资源管理器锁定资源](resource-group-lock-resources.md)。

## <a name="remove-resources-or-resource-group"></a>删除资源或资源组
可以删除资源或资源组。 删除资源组时，还会删除该资源组中的所有资源。

* 若要从资源组中删除某个资源，请针对要删除的资源类型使用删除命令。 此命令将删除该资源，但不会删除该资源组。

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* 若要删除资源组及其所有资源，请使用 **az group delete** 命令。

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

使用这两个命令，都会要求确认是否要删除资源或资源组。

## <a name="next-steps"></a>后续步骤
* 若要了解如何创建资源管理器模板，请参阅[创作 Azure 资源管理器模板](resource-group-authoring-templates.md)。
* 若要了解如何部署模板，请参阅[使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy-cli.md)。
* 可以将现有资源移动到新的资源组。 有关示例，请参阅[将资源移动到新的资源组或订阅中](resource-group-move-resources.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。