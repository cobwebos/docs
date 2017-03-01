---
title: "资源锁的 Resource Manager 模板 | Microsoft Docs"
description: "介绍用于通过模板部署资源锁的资源管理器架构。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: de6c0c57-e33a-4960-98e0-900901592003
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 16f87b8859a5d98c2328388ef12b13ef0e261861


---
# <a name="resource-locks-template-schema"></a>资源锁模板架构
在资源及其子资源上创建锁。

## <a name="schema-format"></a>架构格式
若要创建锁，请将以下架构添加到模板的资源节中。

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }



## <a name="values"></a>值
下表描述了需要在架构中设置的值。

| Name | 必选 | 说明 |
| --- | --- | --- |
| type |是 |要创建的资源类型。<br /><br />资源：<br />**{namespace}/{type}/providers/locks**<br /><br/>资源组：<br />**Microsoft.Authorization/locks** |
| apiVersion |是 |要用于创建该资源的 API 版本。<br /><br />使用：<br />**2015-01-01**<br /><br /> |
| 名称 |是 |一个值，同时指定要锁定的资源和锁的名称。 最多为 64 个字符，不能包含 <、>、%、&、? 或任何控制字符。<br /><br />资源：<br />**{resource}/Microsoft.Authorization/{lockname}**<br /><br />资源组：<br />**{lockname}** |
| dependsOn |否 |资源名称或资源唯一标识符的逗号分隔列表。<br /><br />此锁依赖的资源的集合。 如果要锁定的资源部署在同一模板中，请在此元素中包含该资源名称以确保该资源先部署。 |
| properties |是 |一个对象，用于标识锁的类型，以及有关锁的说明。<br /><br />请参阅 [properties 对象](#properties-object) |

### <a name="properties-object"></a>属性对象
| Name | 必选 | 说明 |
| --- | --- | --- |
| 级别 |是 |要应用于作用域的锁的类型。<br /><br />**CannotDelete** - 用户可以修改资源，但不能将其删除。<br />**ReadOnly** - 用户可以读取资源，但不能删除资源，也不能对资源执行任何操作。 |
| 说明 |否 |该锁的说明。 最多可以包含 512 个字符。 |

## <a name="how-to-use-the-lock-resource"></a>如何使用锁资源
将此资源添加到你的模板可防止对资源执行指定的操作。 该锁将应用于所有用户和组。

若要创建或删除管理锁，必须有权访问 **Microsoft.Authorization/*** 或 **Microsoft.Authorization/locks/*** 操作。在内置角色中，只有“所有者”和“用户访问管理员”有权执行这些操作。** 有关基于角色的访问控制的信息，请参阅 [Azure 基于角色的访问控制](../active-directory/role-based-access-control-configure.md)。

锁将应用于指定的资源和任何子资源。

可以使用 PowerShell 命令 **Remove-AzureRmResourceLock** 或 REST API 的 [delete 操作](https://msdn.microsoft.com/library/azure/mt204562.aspx)删除锁。

## <a name="examples"></a>示例
以下示例将一个 cannot-delete 锁应用到 Web 应用。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                  "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

以下示例将一个 cannot-delete 锁应用到资源组。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "CannotDelete",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## <a name="next-steps"></a>后续步骤
* 有关模板结构的信息，请参阅[创作 Azure Resource Manager 模板](resource-group-authoring-templates.md)。
* 有关锁的详细信息，请参阅[使用 Azure Resource Manager 锁定资源](resource-group-lock-resources.md)。




<!--HONumber=Nov16_HO3-->


