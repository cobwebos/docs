---
title: 锁定 Azure 资源以防止更改 | Microsoft Docs
description: 通过对所有用户和角色应用锁，来防止用户更新或删除关键 Azure 资源。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: a6c7983d22eed4a4232fbb2db490c1743684a04c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65813390"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>锁定资源以防止意外更改 

管理员可能需要锁定订阅、资源组或资源，以防止组织中的其他用户意外删除或修改关键资源。 可以将锁定级别设置为 **CanNotDelete** 或 **ReadOnly**。 在门户中，锁定分别称为**删除**和**只读**。

* **CanNotDelete** 表示经授权的用户仍可读取和修改资源，但不能删除资源。 
* **ReadOnly** 表示经授权的用户可以读取资源，但不能删除或更新资源。 应用此锁类似于将所有经授权的用户限制于使用“读者”  角色授予的权限。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>锁的应用方式

在父范围应用锁时，该范围内所有资源都将继承相同的锁。 即使是之后添加的资源也会从父作用域继承该锁。 继承中限制性最强的锁优先执行。

与基于角色的访问控制不同，可以使用管理锁来对所有用户和角色应用限制。 若要了解如何为用户和角色设置权限，请参阅 [Azure 基于角色的访问控制](../role-based-access-control/role-assignments-portal.md)。

Resource Manager 锁仅适用于管理平面内发生的操作，包括发送到 `https://management.azure.com` 的操作。 这类锁不会限制资源如何执行各自的函数。 资源更改将受到限制，但资源操作不受限制。 例如，SQL 数据库上的 ReadOnly 锁会阻止你删除或修改数据库。 它不会阻止你在数据库中创建、更新或删除数据。 允许数据事务，因为这些操作不会发送到 `https://management.azure.com`。

将应用**ReadOnly**会导致意外的结果，因为似乎修改的资源没有某些操作实际上需要的锁阻塞的操作。 **ReadOnly**锁可以应用到资源或包含该资源的资源组。 被阻止的操作的一些常见示例**ReadOnly**锁是：

* 一个**ReadOnly**存储帐户上的锁将阻止所有用户列出密钥。 列出密钥操作通过 POST 请求进行处理，因为返回的密钥可用于写入操作。

* 应用服务资源上的 **ReadOnly** 锁将阻止 Visual Studio 服务器资源管理器显示资源文件，因为该交互需要写访问权限。

* 一个**ReadOnly**包含虚拟机的资源组上的锁将阻止所有用户启动或重新启动虚拟机。 这些操作需要 POST 请求。

## <a name="who-can-create-or-delete-locks"></a>谁可以创建或删除锁
若要创建或删除管理锁，必须有权执行 `Microsoft.Authorization/*` 或 `Microsoft.Authorization/locks/*` 操作。 在内置角色中，只有“所有者”和“用户访问管理员”有权执行这些操作。  

## <a name="managed-applications-and-locks"></a>托管应用程序和锁

使用某些 Azure 服务，例如 Azure Databricks[托管应用程序](../managed-applications/overview.md)来实现该服务。 在这种情况下，该服务创建两个资源组。 一个资源组包含服务的概述，并且不会锁定。 另一个资源组包含服务的基础结构，并且被锁定。

如果尝试删除基础结构资源组，您将获得一个错误，指出该资源组已锁定。 如果你尝试删除基础结构资源组的锁定，您将获取一个错误，指出无法删除该锁，因为它属于系统应用程序。

相反，删除服务，这也会删除基础结构资源组。

对于托管应用程序，选择部署的服务。

![选择服务](./media/resource-group-lock-resources/select-service.png)

请注意该服务包含的链接**管理的资源组**。 该资源组包含基础结构，并且被锁定。 不能直接删除。

![显示被管理的组](./media/resource-group-lock-resources/show-managed-group.png)

若要删除所有内容对于服务，包括基础结构已锁定的资源组，请选择**删除**服务。

![删除服务](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>门户
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>模板

使用资源管理器模板来部署某个锁定时，请根据锁定范围对名称和类型使用不同的值。

对**资源**应用锁定时，请使用以下格式：

* name - `{resourceName}/Microsoft.Authorization/{lockName}`
* type - `{resourceProviderNamespace}/{resourceType}/providers/locks`

对**资源组**或**订阅**应用锁定时，请使用以下格式：

* name - `{lockName}`
* type - `Microsoft.Authorization/locks`

以下示例演示可创建应用服务计划、网站和网站上的锁的模板。 锁的资源类型是要锁定的资源的资源类型和 **/providers/locks**。 锁名是通过将包含 **/Microsoft.Authorization/** 的资源名称与锁名连接起来创建的。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

如需在资源组上设置锁定的示例，请参阅[创建资源组并将其锁定](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)。

## <a name="powershell"></a>PowerShell
可以通过 Azure PowerShell 使用 [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 命令锁定已部署的资源。

若要锁定某个资源，请提供该资源的名称、其资源类型及其资源组名称。

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

若要锁定某个资源组，请提供该资源组的名称。

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

若要获取有关某个锁的信息，请使用 [Get-AzureRmResourceLock](/powershell/module/az.resources/get-azresourcelock)。 若要获取订阅中的所有锁，请使用：

```azurepowershell-interactive
Get-AzResourceLock
```

若要获取某个资源的所有锁，请使用：

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

若要获取某个资源组的所有锁，请使用：

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

若要删除锁，请使用：

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

可以通过 Azure CLI 使用 [az lock create](/cli/azure/lock#az-lock-create) 命令锁定已部署的资源。

若要锁定某个资源，请提供该资源的名称、其资源类型及其资源组名称。

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

若要锁定某个资源组，请提供该资源组的名称。

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

若要获取有关某个锁的信息，请使用 [az lock list](/cli/azure/lock#az-lock-list)。 若要获取订阅中的所有锁，请使用：

```azurecli
az lock list
```

若要获取某个资源的所有锁，请使用：

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

若要获取某个资源组的所有锁，请使用：

```azurecli
az lock list --resource-group exampleresourcegroup
```

若要删除锁，请使用：

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
可以使用[管理锁的 REST API](https://docs.microsoft.com/rest/api/resources/managementlocks) 锁定已部署的资源。 REST API 可用于创建和删除锁，并且检索有关现有锁的信息。

若要创建一个锁，请运行：

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

作用域可能是订阅、资源组或资源。 锁名称可以是想要对该锁使用的任何称谓。 对于 api 版本，请使用 **2016-09-01**。

在请求中，包括指定锁属性的 JSON 对象。

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>后续步骤
* 有关使用逻辑方式组织资源的信息，请参阅[使用标记来组织资源](resource-group-using-tags.md)
* 可以使用自定义策略对订阅应用限制和约定。 有关详细信息，请参阅[什么是 Azure Policy？](../governance/policy/overview.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](/azure/architecture/cloud-adoption-guide/subscription-governance)。

