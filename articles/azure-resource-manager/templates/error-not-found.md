---
title: 找不到资源错误
description: 介绍当使用 Azure 资源管理器模板进行部署时找不到资源时，如何解决错误。
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773248"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>解决找不到 Azure 资源的错误

本文介绍部署过程中找不到资源时可能看到的错误。

## <a name="symptom"></a>症状

模板包含无法解析的资源的名称时，会收到类似于以下的错误消息：

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

如果对无法解析的资源使用 [reference](template-functions-resource.md#reference) 或 [listKeys](template-functions-resource.md#listkeys) 函数，则会收到以下错误：

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>原因

资源管理器需要检索资源的属性，但不能确定订阅中的资源。

## <a name="solution-1---set-dependencies"></a>解决方案 1 - 设置依赖关系

如果尝试在模板中部署缺少的资源，请检查是否需要添加依赖关系。 如果可能，Resource Manager 将通过并行创建资源来优化部署。 如果一个资源必须在另一个资源之后部署，则需在模板中使用 dependsOn 元素。 例如，在部署 Web 应用时，应用服务计划必须存在。 如果未指定该 Web 应用与应用服务计划的依赖关系，则 Resource Manager 会同时创建这两个资源。 会收到一条错误消息，指出未能找到应用服务计划资源，因为尝试在 Web 应用上设置属性时它尚不存在。 通过在 Web 应用中设置依赖关系可避免此错误。

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

不过，你想要避免设置不必要的依赖项。 存在不必要的依赖项时，会导致不互相依赖的资源无法并行部署，从而延长了部署时间。 此外，可能会创建阻止部署的循环依赖项。 被引用资源在同一模板中部署并通过其名称（而非资源 ID）引用时，[reference](template-functions-resource.md#reference) 函数和 [list*](template-functions-resource.md#list) 函数将在该资源上创建隐式依赖项。 因此，用户拥有的依赖项可以多于在 **dependsOn** 属性中指定的依赖项。 [resourceId](template-functions-resource.md#resourceid) 函数不创建隐式依赖项，也不验证资源是否存在。 当资源通过其资源 ID 引用时，[reference](template-functions-resource.md#reference) 函数和 [list*](template-functions-resource.md#list) 函数不会创建隐式依赖项。 若要创建隐式依赖项，请传递在同一模板中部署的资源的名称。

看到依赖项问题时，需要深入了解资源部署顺序。 查看部署操作顺序的方法如下：

1. 选择资源组的部署历史记录。

   ![选择部署历史记录](./media/error-not-found/select-deployment.png)

2. 从历史记录中选择一个部署，并选择“事件”。

   ![选择部署事件](./media/error-not-found/select-deployment-events.png)

3. 检查每项资源的事件的顺序。 注意每个操作的状态。 例如，下图显示了并行部署的三个存储帐户。 请注意，这三个存储帐户是同时启动的。

   ![并行部署](./media/error-not-found/deployment-events-parallel.png)

   下图显示了非并行部署的三个存储帐户。 第二个存储帐户依赖于第一个存储帐户，第三个存储帐户又依赖于第二个存储帐户。 启动、接受并处理完成第一个存储帐户后才开始对下一个进行操作。

   ![连续部署](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>解决方案 2 - 从其他资源组获取资源

如果资源所在的资源组与要向其部署资源的资源组不同，请使用 [resourceId 函数](template-functions-resource.md#resourceid)获取资源的完全限定名。

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>解决方案 3 - 检查引用函数

查找包含 [reference](template-functions-resource.md#reference) 函数的表达式。 提供的值根据资源是否位于相同的模板、资源组和订阅而有所不同。 再次确认为方案提供的是所需的参数值。 如果资源位于另一资源组，请提供完整的资源 ID。 例如，要引用另一资源组中的存储帐户，请使用：

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>解决方案 4-从资源获取托管标识

如果要部署隐式创建[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)的资源，则必须等到该资源部署完成后，才能检索托管标识的值。 如果将托管标识名称传递到[reference](template-functions-resource.md#reference)函数，资源管理器会在部署资源和标识之前尝试解析引用。 而应传递应用该标识的资源的名称。 此方法确保在资源管理器解析 reference 函数之前部署资源和托管标识。

在 reference 函数中，使用 `Full` 获取包括托管标识在内的所有属性。

例如，若要获取应用于虚拟机规模集的托管标识的租户 ID，请使用：

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```