---
title: "“找不到 Azure 资源”错误 | Microsoft Docs"
description: "介绍如何解决找不到资源时出现的错误。"
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
ms.openlocfilehash: c76c965c43ca8217faa9488c01975ce09a21daaf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>解决找不到 Azure 资源的错误

本文介绍部署过程中找不到资源时可能遇到的错误。 

## <a name="symptom"></a>症状

模板包含无法解析的资源的名称时，会收到类似于以下的错误消息：

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

如果尝试对无法解析的资源使用 [reference](resource-group-template-functions-resource.md#reference) 或 [listKeys](resource-group-template-functions-resource.md#listkeys) 函数，则会收到以下错误：

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>原因

资源管理器需要检索资源的属性，但不能确定订阅中的资源。

## <a name="solution"></a>解决方案

### <a name="solution-1"></a>解决方案 1

如果尝试在模板中部署缺少的资源，请检查是否需要添加依赖关系。 如果可能，Resource Manager 将通过并行创建资源来优化部署。 如果一个资源必须在另一个资源之后部署，则需在模板中使用 **dependsOn** 元素创建与其他资源的依赖关系。 例如，在部署 Web 应用时，应用服务计划必须存在。 如果未指定该 Web 应用与应用服务计划的依赖关系，则 Resource Manager 会同时创建这两个资源。 会收到一条错误消息，指出未能找到应用服务计划资源，因为尝试在 Web 应用上设置属性时它尚不存在。 通过在 Web 应用中设置依赖关系可避免此错误。

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

有关依赖项错误的故障诊断建议，请参阅[检查部署顺序](resource-manager-troubleshoot-tips.md#check-deployment-sequence)。

### <a name="solution-2"></a>解决方案 2

如果资源所在的资源组与要向其部署资源的资源组不同，请使用 [resourceId 函数](resource-group-template-functions-resource.md#resourceid)获取资源的完全限定名。

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

### <a name="solution-3"></a>解决方案 3

查找包含 [reference](resource-group-template-functions-resource.md#reference) 函数的表达式。 提供的值根据资源是否位于相同的模板、资源组和订阅而有所不同。 再次检查为方案提供的是所需的参数值。 如果资源位于另一资源组，请提供完整的资源 ID。 例如，要引用另一资源组中的存储帐户，请使用：

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```