---
title: "了解 Azure 部署错误 | Microsoft Docs"
description: "介绍如何了解 Azure 部署错误。"
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "部署错误, azure 部署, 部署到 azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.contentlocale: zh-cn
ms.lasthandoff: 07/15/2017

---

# <a name="understand-azure-deployment-errors"></a>了解 Azure 部署错误
本主题介绍部署错误，以及如何了解有关错误的详细信息。 有关常见部署错误的解决方法，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。

## <a name="two-types-of-errors"></a>两种类型的错误
可能接收到的错误有两种类型：

* 验证错误
* 部署错误

下图显示一个订阅的活动日志。 它表示两个部署。 在一个部署中，模板未通过验证（**验证**），因此未能继续。 在另一个部署中，模板已通过验证，但在创建资源时失败（**编写部署**）。 

![显示错误代码](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

验证错误源于部署之前可确定的方案。 原因包括模板中的语法错误，或尝试部署超出订阅配额的资源。 部署错误源于部署过程中发生的条件。 原因包括尝试访问并行部署的资源。

两种类型的错误都会返回用于对部署进行故障排除的错误代码。 两种类型的错误都会显示在[活动日志](resource-group-audit.md)中。 但是，验证错误不会显示在部署历史记录中，因为部署从未启动。

## <a name="determine-error-code"></a>确定错误代码

可以通过查看错误消息和错误代码来了解错误。 [排查使用 Azure Resource Manager 时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)一文按错误代码列出了解决方法。 本主题说明如何使用 Azure 门户了解错误代码。

### <a name="validation-errors"></a>验证错误

通过门户部署时，提交值后会看到验证错误。

![显示门户验证错误](./media/resource-manager-troubleshoot-tips/validation-error.png)

选择消息获取更多详细信息。 下图显示了一条 **InvalidTemplateDeployment** 错误，以及一条指出策略阻止了部署的消息。

![显示验证详细信息](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>部署错误

如果操作通过了验证但部署期间失败，则通知中会出现错误。 选择通知。

![通知错误](./media/resource-manager-troubleshoot-tips/notification.png)

查看有关部署的更多详细信息。 选择查找有关错误的详细信息的选项。

![部署失败](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

看到错误消息和错误代码。 请注意有两个错误代码。 第一个错误代码 (**DeploymentFailed**) 表示常规错误，不提供解决错误所需的详细信息。 第二个错误代码 (**StorageAccountNotFound**) 提供所需的详细信息。 

![错误详细信息](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>启用调试日志记录
有时需要有关请求和响应的详细信息才能了解出现的问题。 使用 PowerShell 或 Azure CLI 可以请求在部署期间记录更多信息。

- PowerShell

   在 PowerShell 中，将 **DeploymentDebugLogLevel** 参数设置为 All、ResponseContent 或 RequestContent。

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   使用以下 cmdlet 检查请求内容：

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   或者，使用以下信息检查响应内容：

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   此信息可帮助确定模板中的值是否设置不正确。

- Azure CLI

   使用以下命令查看部署操作：

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- 嵌套模板

   若要记录嵌套模板的调试信息，请使用 **debugSetting** 元素。

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>创建故障排除模板
在某些情况下，排除模板故障的最简单方法是测试部分模板。 可以创建一个简化的模板，通过该模板将测试重点放在最可能导致错误的部分。 例如，假设在引用资源时收到错误消息。 创建一个模板，该模板返回可能导致问题的部分，而不是处理整个模板。 这可以帮助确定传入的是否是正确的参数，是否正确使用模板函数，以及是否获得所需的资源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

或者，假设遇到你认为与不正确设置的依赖项相关的部署错误。 通过将模板分解为多个简化模板进行测试。 首先，创建仅部署单个资源（如 SQL Server）的模板。 确定已正确定义该资源后，添加依赖于该资源的资源（如 SQL 数据库）。 正确定义这两个资源后，添加其他从属资源（如审核策略）。 在每个测试部署之间，删除资源组，以确保充分测试依赖关系。 

## <a name="check-deployment-sequence"></a>检查部署顺序

如果以意外的顺序部署资源，可能发生许多部署错误。 依赖关系设置不当就会出现这些错误。 缺少必需的依赖项时，就会出现一项资源尝试使用另一资源的值，但后者却不存在的情况。 出现一个错误，指出找不到资源。 可能会间歇性地遇到此类错误，因为资源的部署时间各不相同。 例如，第一次尝试部署资源成功，因为所需资源偶然但却及时地完成了相关操作。 但是，第二次尝试却失败了，因为所需资源未及时完成相关操作。 

不过，你想要避免设置不必要的依赖项。 存在不必要的依赖项时，会导致不互相依赖的资源无法并行部署，从而延长了部署时间。 此外，可能会创建阻止部署的循环依赖项。 在同一模板中部署被引用资源时，[reference](resource-group-template-functions-resource.md#reference) 函数在该资源上创建隐式依赖项。 因此，用户拥有的依赖项可以多于在 **dependsOn** 属性中指定的依赖项。 [resourceId](resource-group-template-functions-resource.md#resourceid) 函数不创建隐式依赖项，也不验证资源是否存在。

遇到依赖项问题时，需了解资源部署顺序。 查看部署操作顺序的方法如下：

1. 选择资源组的部署历史记录。

   ![选择部署历史记录](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. 从历史记录中选择一个部署，然后选择“事件”。

   ![选择部署事件](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. 检查每项资源的事件的顺序。 注意每个操作的状态。 例如，下图显示了并行部署的三个存储帐户。 请注意，这三个存储帐户是同时启动的。

   ![并行部署](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   下图显示了非并行部署的三个存储帐户。 第二个存储帐户依赖于第一个存储帐户，第三个存储帐户又依赖于第二个存储帐户。 因此，启动、接受并处理完成第一个存储帐户后才开始对下一个进行操作。

   ![连续部署](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

即使对于更复杂的方案，也可以通过相同方法发现每个资源的部署开始时间和完成时间。 浏览部署事件，看其顺序是否不同于预期的顺序。 如果答案为是，请重新评估该资源的依赖项。

Resource Manager 可在模板验证过程中确定循环依赖项。 它会返回一条错误消息，明确指出存在循环依赖项。 解决循环依赖项问题的步骤：

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


## <a name="next-steps"></a>后续步骤
* 有关常见部署错误的解决方法，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。
* 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](resource-group-audit.md)。
* 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](resource-manager-deployment-operations.md)。

