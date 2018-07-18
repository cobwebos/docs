---
title: Azure 无效模板错误 | Microsoft Docs
description: 说明如何解决无效模板错误。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 59f07b9ba8116cb1a4b5ab50382d89d01a78853b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357684"
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

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>解决方案 1 - 语法错误

如果收到指示模板验证失败的错误消息，则说明模板中可能存在语法问题。

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

此错误很容易发生，因为模板表达式可能很复杂。 例如，存储帐户的以下名称分配具有一组方括号、三个函数、三组圆括号、一组单引号和一个属性：

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

如果未提供匹配的语法，该模板会生成一个不同于所需的值。

收到此类错误时，请仔细检查表达式语法。 考虑使用 [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 或 [Visual Studio Code](resource-manager-vs-code.md) 等 JSON 编辑器，此类编辑器在出现语法错误时可以发出警告。

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>解决方案 2 - 段长度不正确

当资源名称的格式不正确时，会发生另一种模板无效错误。

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

根级别的资源其名称中的段必须比资源类型中的段少一个。 段之间用斜杠隔开。 在下面的示例中，类型有两个段，名称有一个段，因此为有效名称。

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

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>解决方案 3 - 参数无效

如果所提供的的参数值不是允许值之一，则会收到类似于以下错误的消息：

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

请仔细检查模板中的允许值，并提供在部署过程中提供这些值之一。 有关允许参数值的详细信息，请参阅 [Azure 资源管理器模板的参数部分](resource-manager-templates-parameters.md)。

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>解决方案 4 - 太多目标资源组

如果在单个部署中指定的目标资源组超过五个，则会收到此错误。 请考虑合并部署中的资源组数，或者部署某些模板作为单独的部署。 有关详细信息，[将 Azure 资源部署到多个订阅或资源组](resource-manager-cross-resource-group-deployment.md)。

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>解决方案 5 - 检测到循环依赖项

当资源以某种方式互相依赖，导致部署无法启动时，就会出现此错误。 将多个相互依赖的项组合在一起时，会导致两个或两个以上的资源等待其他资源，而后者也在进行等待。 例如，resource1 依赖于 resource3，resource2 依赖于 resource1，resource3 依赖于 resource2。 通常情况下，删除不必要的依赖项即可解决此问题。

解决循环依赖项问题的步骤：

1. 在模板中找到循环依赖项中标识的资源。 
2. 检查该资源的 **dependsOn** 属性并使用 **reference** 函数查看其所依赖的资源。 
3. 检查这些资源，看其依赖于哪些资源。 顺着这些依赖项检查下去，直到找到依赖于原始资源的资源。
5. 对于循环依赖项所牵涉的资源，请仔细检查所有使用 **dependsOn** 属性的情况，确定不需要的依赖项。 删除这些依赖项。 如果不确定某个依赖项是否为必需依赖项，可尝试删除它。 
6. 重新部署模板。

部署模板时，删除 **dependsOn** 属性中的值可能导致错误。 如果遇到错误，可将依赖项添加回模板。 

如果该方法无法解决循环依赖项问题，可考虑将部分部署逻辑移至子资源（例如扩展或配置设置）中。 将这些子资源配置为在循环依赖项所牵涉的资源之后部署。 例如，假设要部署两个虚拟机，但必须在每个虚拟机上设置引用另一虚拟机的属性。 可以按下述顺序部署这两个虚拟机：

1. vm1
2. vm2
3. vm1 上的扩展依赖于 vm1 和 vm2。 扩展在 vm1 上设置的值是从 vm2 获取的。
4. vm2 上的扩展依赖于 vm1 和 vm2。 扩展在 vm2 上设置的值是从 vm1 获取的。

同一方法适用于应用服务应用。 可以考虑将配置值移到应用资源的子资源中。 可以按下述顺序部署两个 Web 应用：

1. webapp1
2. webapp2
3. webapp1 的配置依赖于 webapp1 和 webapp2。 它包含应用设置，其值来自 webapp2。
4. webapp2 的配置依赖于 webapp1 和 webapp2。 它包含应用设置，其值来自 webapp1。
