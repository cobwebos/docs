---
title: Azure 父资源错误 | Microsoft Docs
description: 说明如何在使用父资源时解决错误。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 3042ea1a523f12ae0311545a1b9bc67306f266dd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447296"
---
# <a name="resolve-errors-for-parent-resources"></a>解决父资源的错误

本文介绍部署依赖于父资源的资源时可能遇到的错误。

## <a name="symptom"></a>症状

在部署的资源是另一个资源的子级时，可能会收到以下错误：

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>原因

如果一个资源是另一个资源的子级，则在创建子资源之前，父资源必须存在。 子资源的名称定义与父资源的连接。 子资源名称采用格式 `<parent-resource-name>/<child-resource-name>` 例如，SQL 数据库可能定义为：

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

如果在同一模板中部署服务器和数据库，但未在服务器上指定依赖关系，则可以在部署服务器之前启动数据库部署。 

如果父资源已存在且未部署在同一模板中，则当资源管理器无法将子资源与父资源关联时，会出现此错误。 当子资源的格式不正确，或者子资源部署到与父资源的资源组不同的资源组时，可能会发生此错误。

## <a name="solution"></a>解决方案

要在同一模板中部署父资源和子资源时解决此错误，请包含依赖关系。

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

要在父资源先前部署在其他模板中时解决此错误，请不要设置依赖关系。 而是将子资源部署到同一资源组并提供父资源的名称。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

有关详细信息，请参阅[定义 Azure 资源管理器模板中部署资源的顺序](resource-group-define-dependencies.md)。