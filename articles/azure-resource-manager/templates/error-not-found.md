---
title: 找不到资源错误
description: 介绍如何解决找不到资源时所发生的错误。 部署 Azure 资源管理器模板或执行管理操作时，可能会发生此错误。
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.openlocfilehash: 224af4ce0fe5053201f25d8207f4ca8cdc73e638
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84667941"
---
# <a name="resolve-resource-not-found-errors"></a>解决“找不到资源”错误

本文介绍操作过程中找不到资源时会看到的错误。 通常情况下，在部署资源时，会看到此错误。 在执行管理任务且 Azure 资源管理器找不到所需资源时，也会显示此错误。 例如，如果尝试将标记添加到不存在的资源，就会收到此错误。

## <a name="symptom"></a>症状

存在两个错误代码，指示找不到资源。 “NotFound”错误返回的结果类似于****：

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

**ResourceNotFound**错误返回类似于以下内容的结果：

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>原因

资源管理器需要检索资源的属性，但找不到订阅中的资源。

## <a name="solution-1---check-resource-properties"></a>解决方案 1 - 检查资源属性

如果在执行管理任务时收到此错误，请检查为资源提供的值。 要检查的三个值为：

* 资源名称
* 资源组名称
* 订阅

如果使用的是 PowerShell 或 Azure CLI，请检查是否在包含该资源的订阅中运行了该命令。 可使用 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) 或 [az account set](/cli/azure/account#az-account-set) 来更改订阅。 许多命令还提供了一个订阅参数，使用该参数可以指定与当前上下文不同的订阅。

如果在验证属性时遇到问题，可登录[门户](https://portal.azure.com)。 找到要尝试使用的资源，并检查资源名称、资源组和订阅。

## <a name="solution-2---set-dependencies"></a>解决方案 2 - 设置依赖项

如果在部署模板时遇到此错误，可能需要添加依赖项。 如果可能，Resource Manager 会通过并行创建资源来优化部署。 如果一个资源必须在另一个资源之后部署，则需在模板中使用 dependsOn 元素****。 例如，在部署 Web 应用时，应用服务计划必须存在。 如果未指定该 Web 应用与应用服务计划的依赖关系，则 Resource Manager 会同时创建这两个资源。 会收到一条错误消息，指出未能找到应用服务计划资源，因为尝试在 Web 应用上设置属性时它尚不存在。 在 Web 应用中设置依赖关系可避免此错误。

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

2. 从历史记录中选择一个部署，并选择“事件” ****。

   ![选择部署事件](./media/error-not-found/select-deployment-events.png)

3. 检查每项资源的事件的顺序。 注意每个操作的状态。 例如，下图显示了并行部署的三个存储帐户。 请注意，这三个存储帐户是同时启动的。

   ![并行部署](./media/error-not-found/deployment-events-parallel.png)

   下图显示了非并行部署的三个存储帐户。 第二个存储帐户依赖于第一个存储帐户，第三个存储帐户又依赖于第二个存储帐户。 启动、接受并处理完成第一个存储帐户后才开始对下一个进行操作。

   ![连续部署](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>解决方案 3 - 获取外部资源

在部署模板时，如果需要获取存在于其他订阅或资源组中的资源，请使用 [resourceId 函数](template-functions-resource.md#resourceid)。 此函数将获取并返回资源的完全限定名称。

resourceId 函数中的订阅和资源组参数是可选的。 如果不指定其值，会默认为当前订阅和资源组。 如果要使用另一个资源组或订阅中的资源，请确保提供相应的值。

以下示例获取另一个资源组中的资源的资源 ID。

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>解决方案 4 - 从资源获取托管标识

如果要部署隐式创建[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)的资源，则必须等到部署该资源后，才能检索托管标识上值。 如果将托管标识名称传递给[引用](template-functions-resource.md#reference)函数，资源管理器会在部署资源和标识之前尝试解析该引用。 请改为应用标识的资源的名称。 此方法可确保在资源管理器解析引用函数之前部署资源和托管标识。

在引用函数中，使用 `Full` 获取包括托管标识在内的所有属性。

模式为：

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> 不要使用该模式：
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> 模板会失败。

例如，若要获取应用于虚拟机的托管标识的主体 ID，请使用：

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

或者，若要获取应用于虚拟机规模集的托管标识的租户 ID，请使用：

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>解决方案 5 - 检查函数

部署模板时，请查找使用 [reference](template-functions-resource.md#reference) 或 [listKeys](template-functions-resource.md#listkeys) 函数的表达式。 提供的值因资源是否位于同一模板、资源组和订阅中而有所不同。 请确认为方案提供的是所需的参数值。 如果资源位于不同的资源组中，请提供完整的资源 ID。 例如，若要引用另一个资源组中的存储帐户，请使用：

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```