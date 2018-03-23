---
title: 排查常见的 Azure 部署错误 | Microsoft Docs
description: 说明如何解决使用 Azure 资源管理器将资源部署到 Azure 时的常见错误。
services: azure-resource-manager
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: 部署错误, azure 部署, 部署到 azure
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 2cf31b32e02923aa573d5586b8ca24bf30b7d97b
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>排查使用 Azure 资源管理器时的常见 Azure 部署错误

本文介绍我们可能会遇到的一些常见 Azure 部署错误，并提供有关如何解决这些错误的信息。 如果找不到部署错误的错误代码，请参阅[查找错误代码](#find-error-code)。

## <a name="error-codes"></a>错误代码

| 错误代码 | 缓解措施 | 详细信息 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | 遵循存储帐户的命名限制。 | [解析存储帐户名称](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | 检查可用的存储帐户属性。 | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | 群集或区域没有可用的资源或无法支持所请求的 VM 大小。 稍后重试请求，或者请求不同的 VM 大小。 | [Linux 预配和分配问题](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)和 [Windows 预配和分配问题](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| AnotherOperationInProgress | 等待并发操作完成。 | |
| AuthorizationFailed | 帐户或服务主体没有足够的访问权限，无法完成部署。 检查帐户所属的角色及其在部署范围内的访问权限。 | [Azure 基于角色的访问控制](../active-directory/role-based-access-control-configure.md) |
| BadRequest | 发送的部署值与资源管理器预期的值不匹配。 检查内部状态消息，以帮助进行故障排除。 | [模板引用](/azure/templates/)和[支持的位置](resource-manager-templates-resources.md#location) |
| 冲突 | 请求的操作在资源的当前状态下不受允许。 例如，仅当创建 VM 或该 VM 已解除分配时，才允许调整磁盘大小。 | |
| DeploymentActive | 等待此资源组上的并发部署完成。 | |
| DeploymentFailed | DeploymentFailed 错误为常规错误，不提供解决错误所需的详细信息。 请查看错误代码的错误详情，其中提供了详细信息。 | [查找错误代码](#find-error-code) |
| DnsRecordInUse | DNS 记录名称必须唯一。 提供不同的名称，或修改现有记录。 | |
| ImageNotFound | 检查 VM 映像设置。 |  |
| InUseSubnetCannotBeDeleted | 如果尝试更新资源，但已通过删除并创建资源处理了请求，则可能会遇到此错误。 确保指定所有未更改值。 | [更新资源](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 获取相应租户的访问令牌。 只能从帐户所属的租户获取该令牌。 | |
| InvalidContentLink | 很可能尝试过链接到不可用的嵌套模板。 再次确认为嵌套模板提供的 URI。 如果模板存在于存储帐户中，请确保 URI 可访问。 可能需要传递 SAS 令牌。 | [链接的模板](resource-group-linked-templates.md) |
| InvalidParameter | 为资源提供的某个值与预期值不匹配。 此错误可能由许多不同的状况造成。 例如，密码强度可能不足，或者 Blob 名称可能不正确。 检查错误消息，确定需要更正哪个值。 | |
| InvalidRequestContent | 部署值包含意外的值，或者缺少必需的值。 确认资源类型的值。 | [模板参考](/azure/templates/) |
| InvalidRequestFormat | 执行部署时启用调试日志记录，并验证请求的内容。 | [调试日志记录](#enable-debug-logging) |
| InvalidResourceNamespace | 检查在 **type** 属性中指定的资源命名空间。 | [模板参考](/azure/templates/) |
| InvalidResourceReference | 资源尚不存在，或错误地引用了它。 检查是否需要添加依赖项。 验证所用的 **reference** 函数是否包含方案所需的参数。 | [解决依赖项问题](resource-manager-not-found-errors.md) |
| InvalidResourceType | 检查在 **type** 属性中指定的资源类型。 | [模板参考](/azure/templates/) |
| InvalidTemplate | 检查模板语法是否有错误。 | [解决模板无效问题](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | 检查帐户所属的租户是否与要部署到的资源组所属的租户相同。 | |
| LinkedInvalidPropertyId | 无法正确解析资源的资源 ID。 检查是否为资源 ID 提供了所有必需值，包括订阅 ID、资源组名称、资源类型、父资源名称（如果需要）和资源名称。 | |
| LocationRequired | 提供资源的位置。 | [设置位置](resource-manager-templates-resources.md#location) |
| MissingRegistrationForLocation | 检查资源提供程序注册状态和支持的位置。 | [解决注册问题](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | 将订阅注册到资源提供程序。 | [解决注册问题](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | 检查资源提供程序注册状态。 | [解决注册问题](resource-manager-register-provider-errors.md) |
| NotFound | 可能正在尝试同时部署依赖资源和父资源。 检查是否需要添加依赖项。 | [解决依赖项问题](resource-manager-not-found-errors.md) |
| OperationNotAllowed | 部署正在尝试执行一个超过了订阅、资源组或区域配额的操作。 如果可能，请修改部署，以保持在配额范围内。 否则，请考虑请求更改配额。 | [解决配额问题](resource-manager-quota-errors.md) |
| ParentResourceNotFound | 确保在创建子资源之前父资源已存在。 | [解决父资源问题](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | 指定的 IP 地址包括 Azure 所需的地址范围。 更改 IP 地址以避免保留的范围。 | [IP 地址](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | 指定的 IP 地址超出子网范围。 更改 IP 地址，使之在子网范围内。 | [IP 地址](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | 无法在已部署的资源上更改某些属性。 更新资源时，请限制为对允许的属性进行更改。 | [更新资源](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | 订阅包含阻止尝试在部署期间执行的操作的资源策略。 查找阻止该操作的策略。 如果可能，请修改部署，以满足策略中的限制。 | [解决策略问题](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | 提供不包含保留名称的资源名称。 | [保留的资源名称](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | 等待删除操作完成。 | |
| ResourceGroupNotFound | 检查部署的目标资源组的名称。 该名称必须已在订阅中存在。 检查订阅上下文。 | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | 部署引用了一个无法解析的资源。 验证所用的 **reference** 函数是否包含方案所需的参数。 | [解决引用问题](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | 部署尝试创建的资源超过了订阅、资源组或区域的配额。 如果可能，请修改基础结构，以保持在配额范围内。 否则，请考虑请求更改配额。 | [解决配额问题](resource-manager-quota-errors.md) |
| SkuNotAvailable | 选择可在所选位置中使用的 SKU（例如 VM 大小）。 | [解决 SKU 问题](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | 为存储帐户提供唯一名称。 | [解析存储帐户名称](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | 为存储帐户提供唯一名称。 | [解析存储帐户名称](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | 检查尝试使用的存储帐户的订阅、资源组和名称。 | |
| SubnetsNotInSameVnet | 一个虚拟机只能有一个虚拟网络。 部署多个 NIC 时，请确保它们属于同一虚拟网络。 | [多个 NIC](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>查找错误代码

可能接收到的错误有两种类型：

* 验证错误
* 部署错误

验证错误源于部署之前可确定的方案。 原因包括模板中的语法错误，或尝试部署超出订阅配额的资源。 部署错误源于部署过程中发生的条件。 原因包括尝试访问并行部署的资源。

两种类型的错误都会返回用于对部署进行故障排除的错误代码。 两种类型的错误都会显示在[活动日志](resource-group-audit.md)中。 但是，验证错误不会显示在部署历史记录中，因为部署从未启动。

### <a name="validation-errors"></a>验证错误

通过门户部署时，提交值后会看到验证错误。

![显示门户验证错误](./media/resource-manager-common-deployment-errors/validation-error.png)

选择消息获取更多详细信息。 下图显示了一条 **InvalidTemplateDeployment** 错误，以及一条指出策略阻止了部署的消息。

![显示验证详细信息](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>部署错误

如果操作通过了验证但部署期间失败，则通知中会出现错误。 选择通知。

![通知错误](./media/resource-manager-common-deployment-errors/notification.png)

查看有关部署的更多详细信息。 选择查找有关错误的详细信息的选项。

![部署失败](./media/resource-manager-common-deployment-errors/deployment-failed.png)

看到错误消息和错误代码。 请注意有两个错误代码。 第一个错误代码 (DeploymentFailed) 表示常规错误，不提供解决错误所需的详细信息。 第二个错误代码 (**StorageAccountNotFound**) 提供所需的详细信息。 

![错误详细信息](./media/resource-manager-common-deployment-errors/error-details.png)

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

   此信息可帮助确定模板中某个值的设置是否错误。

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

或者，假设遇到认为与不正确设置的依赖项相关的部署错误。 通过将模板分解为多个简化模板进行测试。 首先，创建仅部署单个资源（如 SQL Server）的模板。 确定已正确定义该资源后，添加依赖于该资源的资源（如 SQL 数据库）。 正确定义这两个资源后，添加其他从属资源（如审核策略）。 在每个测试部署之间，删除资源组，以确保充分测试依赖关系。


## <a name="next-steps"></a>后续步骤
* 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](resource-group-audit.md)。
* 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](resource-manager-deployment-operations.md)。
