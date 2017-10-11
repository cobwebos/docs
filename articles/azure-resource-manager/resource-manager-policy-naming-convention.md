---
title: "有关命名约定的 Azure 资源策略 | Microsoft Docs"
description: "介绍了有关资源命名约定的 Azure Resource Manager 策略。"
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
ms.date: 06/27/2017
ms.author: tomfitz
ms.openlocfilehash: 51b3519bbba8cb4c768bfdd7dadf92fced434f22
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="apply-resource-policies-for-names-and-text"></a>应用针对名称和文本的资源策略
本主题介绍了多个可应用于设置命名和文本约定的[资源策略](resource-manager-policy.md)。 这些策略可确保资源名称和标记值的一致性。 

## <a name="set-naming-convention-with-wildcard"></a>使用通配符设置命名约定
以下示例演示如何使用 **like** 条件支持的通配符。 该条件指明，如果名称符合所述模式 (namePrefix\*nameSuffix)，则拒绝请求：

```json
{
  "if": {
    "not": {
      "field": "name",
      "like": "namePrefix*nameSuffix"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-naming-convention-with-pattern"></a>使用模式设置命名约定

若要指定与某个模式匹配的资源名称，请使用 match 条件。 下面的示例要求名称以 `contoso` 开头并包含六个其他字母：

```json
{
  "if": {
    "not": {
      "field": "name",
      "match": "contoso??????"
    }
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="set-date-pattern-for-tag-value"></a>设置标记值的日期模式

若要求日期模式为两个数字、短划线、三个字母、短划线和四个数字，请使用以下代码：

```json
{
  "if": {
    "field": "tags.date",
    "match": "##-???-####"
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>后续步骤
* 定义策略规则后（如上述示例所示），需要创建策略定义并将其分配给作用域。 作用域可以是订阅、资源组或资源。 若要通过门户分配策略，请参阅[使用 Azure 门户分配和管理资源策略](resource-manager-policy-portal.md)。 若要通过 REST API、PowerShell 或 Azure CLI 分配策略，请参阅[通过脚本分配和管理策略](resource-manager-policy-create-assign.md)。 
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。

