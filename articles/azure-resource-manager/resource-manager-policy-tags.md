---
title: "适用于标记的 Azure 资源策略 | Microsoft Docs"
description: "提供相关示例，介绍用于管理资源标记的资源策略"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 72d398c529fc7dd5eef450da0e134dcdab534ac5
ms.openlocfilehash: 375a8df763eb6b4b8f7349e0061ab39c076ebfc6


---
# <a name="apply-resource-policies-for-tags"></a>应用适用于标记的资源策略

本主题提供通用策略规则，可应用这些规则确保资源上标记使用的一致性。

向具有现有资源的资源组或订阅应用标记策略时，不会按追溯方式向这些资源应用策略。 若要强制对这些资源应用策略，请触发现有资源更新，如[触发现有资源更新](#trigger-updates-to-existing-resources)中所示。

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>确保资源组中的所有资源都有一个标记/值

常见要求是资源组中的所有资源都有特定的标记和值。 跟踪部门开支时通常需要此要求。 必须满足以下条件：

* 将所需标记和值附加到当前未带任何标记的新资源和更新的资源上。
* 将所需标记和值附加到带其他标记但未带所需标记和值的新资源和更新的资源上。
* 不能从任何现有资源中删除所需标记和值。

可向资源组应用以下&3; 项策略来实现此要求：

* [附加标记](#append-tag) 
* [在其他标记上附加标记](#append-tag-with-other-tags)
* [需要标记和值](#require-tag-and-value)

### <a name="append-tag"></a>附加标记

如果不存在任何标记，以下策略规则将附加具有预定义值的 costCenter 标记：

```json
{
  "if": {
    "field": "tags",
    "exists": "false"
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags",
        "value": {"costCenter":"myDepartment" }
      }
    ]
  }
}
```

### <a name="append-tag-with-other-tags"></a>在其他标记上附加标记

如果存在标记，但未定义 costCenter 标记，以下策略规则将附加具有预定义值的 costCenter 标记：

```json
{
  "if": {
    "allOf": [
      {
        "field": "tags",
        "exists": "true"
      },
      {
        "field": "tags.costCenter",
        "exists": "false"
      }
    ]
  },
  "then": {
    "effect": "append",
    "details": [
      {
        "field": "tags.costCenter",
        "value": "myDepartment"
      }
    ]
  }
}
```

### <a name="require-tag-and-value"></a>需要标记和值

以下策略规则拒绝更新或创建未将 costCenter 标记分配给预定义值的资源。

```json
{
  "if": {
    "not": {
      "field": "tags.costCenter",
      "equals": "myDepartment"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="require-tags-for-a-resource-type"></a>某资源类型需要标记
以下示例演示如何嵌套逻辑运算符，以要求仅对指定资源类型（本例中为存储帐户）使用应用程序标记。

```json
{
    "if": {
        "allOf": [
          {
            "not": {
              "field": "tags",
              "containsKey": "application"
            }
          },
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          }
        ]
    },
    "then": {
        "effect": "audit"
    }
}
```

## <a name="require-tag"></a>需要标记
以下策略拒绝其标记均未含有“costCenter”键（可应用任意值）的请求：

```json
{
  "if": {
    "not" : {
      "field" : "tags",
      "containsKey" : "costCenter"
    }
  },
  "then" : {
    "effect" : "deny"
  }
}
```

## <a name="trigger-updates-to-existing-resources"></a>触发现有资源更新

以下 PowerShell 脚本将触发对现有资源的更新，从而强制实施添加的标记策略。

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($_.Tags -eq $NULL) { @{}} else {$_.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
    }
}
```

## <a name="next-steps"></a>后续步骤
* 定义策略规则后（如上述示例所示），需要创建策略定义并将其分配给作用域。 作用域可以是订阅、资源组或资源。 有关创建和分配策略的示例，请参阅[分配和管理策略](resource-manager-policy-create-assign.md)。 
* 有关资源策略的简介，请参阅[资源策略概述](resource-manager-policy.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。




<!--HONumber=Feb17_HO3-->


