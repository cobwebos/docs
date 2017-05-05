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
ms.date: 04/20/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 04338b62d942774368149b27e8b35713b77f8d7c
ms.lasthandoff: 04/21/2017


---
# <a name="apply-resource-policies-for-tags"></a>应用适用于标记的资源策略

本主题提供通用策略规则，可应用这些规则确保资源上标记使用的一致性。

向具有现有资源的资源组或订阅应用标记策略时，不会按追溯方式向这些资源应用策略。 若要强制对这些资源应用策略，请触发现有资源更新，如[触发现有资源更新](#trigger-updates-to-existing-resources)中所示。

## <a name="ensure-all-resources-in-a-resource-group-have-a-tagvalue"></a>确保资源组中的所有资源都有一个标记/值

常见要求是资源组中的所有资源都有特定的标记和值。 跟踪部门开支时通常需要此要求。 必须满足以下条件：

* 将所需标记和值追加到未带该标记的新资源和更新的资源上。
* 不能从任何现有资源中删除所需标记和值。

可通过将两个内置策略应用于资源组来实现此要求。

| ID | 说明 |
| ---- | ---- |
| 2a0e14a6-b0a6-4fab-991a-187a4f81c498 | 用户未指定时，将应用所需的标记及其默认值。 |
| 1e30110a-5ceb-460c-a204-c1c3969c6d62 | 强制执行所需的标记及其值。 |

### <a name="powershell"></a>PowerShell

以下 PowerShell 脚本将两个内置策略定义分配给资源组。 在运行脚本前，请将所有所需的标记分配给资源组。 资源组中的每个标记是组中的资源所必需的。 若要分配到订阅中的所有资源组，获取资源组时请不要提供 `-Name` 参数。

```powershell
$appendpolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '2a0e14a6-b0a6-4fab-991a-187a4f81c498'}
$denypolicy = Get-AzureRmPolicyDefinition | Where-Object {$_.Name -eq '1e30110a-5ceb-460c-a204-c1c3969c6d62'}

$rgs = Get-AzureRMResourceGroup -Name ExampleGroup

foreach($rg in $rgs)
{
    $tags = $rg.Tags
    foreach($key in $tags.Keys){
        $key 
        $tags[$key]
        New-AzureRmPolicyAssignment -Name ("append"+$key+"tag") -PolicyDefinition $appendpolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
        New-AzureRmPolicyAssignment -Name ("denywithout"+$key+"tag") -PolicyDefinition $denypolicy -Scope $rg.ResourceId -tagName $key -tagValue  $tags[$key]
    }
}
```

分配策略后，可以触发对所有现有资源的更新，以强制执行已添加的标记策略。 以下脚本会保留资源上存在的任何其他标记：

```powershell
$group = Get-AzureRmResourceGroup -Name "ExampleGroup" 

$resources = Find-AzureRmResource -ResourceGroupName $group.ResourceGroupName 

foreach($r in $resources)
{
    try{
        $r | Set-AzureRmResource -Tags ($a=if($r.Tags -eq $NULL) { @{}} else {$r.Tags}) -Force -UsePatchSemantics
    }
    catch{
        Write-Host  $r.ResourceId + "can't be updated"
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

## <a name="next-steps"></a>后续步骤
* 定义策略规则后（如上述示例所示），需要创建策略定义并将其分配给作用域。 作用域可以是订阅、资源组或资源。 若要通过门户分配策略，请参阅[使用 Azure 门户分配和管理资源策略](resource-manager-policy-portal.md)。 若要通过 REST API、PowerShell 或 Azure CLI 分配策略，请参阅[通过脚本分配和管理策略](resource-manager-policy-create-assign.md)。
* 有关资源策略的简介，请参阅[资源策略概述](resource-manager-policy.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。


