---
title: "Azure 无效模板错误 | Microsoft Docs"
description: "说明如何解决无效模板错误。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: e58c10cfb4cdd4ba49945e6c19845cbc957d6326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-invalid-template"></a>解决无效模板错误

本文说明如何解决无效模板错误。

## <a name="symptom"></a>症状

部署模板时将收到一个指示以下内容的错误：

```
Code=InvalidTemplate
Message=<varies>
```

错误消息取决于错误的类型。

## <a name="cause"></a>原因

此错误可能由几种不同类型的错误导致。 它们通常涉及模板中的语法或结构错误。

## <a name="solution"></a>解决方案

### <a name="solution-1---syntax-error"></a>解决方案 1 - 语法错误

如果收到指示模板验证失败的错误消息，则说明模板中可能存在语法问题。

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

此错误很容易发生，因为模板表达式可能很复杂。 例如，存储帐户的以下名称分配包含一组方括号、三个函数、三组圆括号、一组单引号和一个属性：

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

如果未提供匹配的语法，该模板会生成一个不同于所需的值。

收到此类错误时，请仔细检查表达式语法。 考虑使用 [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 或 [Visual Studio Code](resource-manager-vs-code.md) 等 JSON 编辑器，此类编辑器在出现语法错误时可以发出警告。

### <a name="solution-2---incorrect-segment-lengths"></a>解决方案 2 - 段长度不正确

当资源名称的格式不正确时，会发生另一种模板无效错误。

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

根级别的资源其名称中的段必须比资源类型中的段少一个。 段之间用斜杠隔开。 在下面的示例中，类型有两个段，名称有一个段，因此为**有效名称**。

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

但下一个示例**不是有效名称**，因为其段数与类型的段数相同。

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

对于子资源来说，类型和名称的段数相同。 此段数是有意义的，因为子资源的全名和类型包括父名称和类型。 因此，全名仍然比完整类型少一个段。

```json
"resources": [
    {
        "type": "Microsoft.KeyVault/vaults",
        "name": "contosokeyvault",
        ...
        "resources": [
            {
                "type": "secrets",
                "name": "appPassword",
                ...
            }
        ]
    }
]
```

确保段数正确对于 Resource Manager 类型来说可能很困难，这些类型应用到各个资源提供程序。 例如，对网站应用资源锁需要使用包含四个段的类型。 因此，该名称包含三个段：

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

### <a name="solution-3---parameter-is-not-valid"></a>解决方案 3 - 参数无效

如果模板为一个参数指定了允许值，而所提供的值不是这些值之一，则会收到类似于以下错误的消息：

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

请仔细检查模板中的允许值，并提供在部署过程中提供这些值之一。

### <a name="solution-4---circular-dependency-detected"></a>解决方案 4 - 检测到循环依赖项

当资源以某种方式互相依赖，导致部署无法启动时，就会出现此错误。 将多个相互依赖的项组合在一起时，会导致两个或两个以上的资源等待其他资源，而后者也在进行等待。 例如，resource1 依赖于 resource3，resource2 依赖于 resource1，resource3 依赖于 resource2。 通常情况下，删除不必要的依赖项即可解决此问题。
