# 概述
## [什么是 Resource Manager？](resource-group-overview.md)
## [资源提供程序和类型](resource-manager-supported-services.md)
## [Resource Manager 部署和经典部署](resource-manager-deployment-model.md)
## [订阅监管](resource-manager-subscription-governance.md)

# 入门
## [创建和部署模板](resource-manager-create-first-template.md)
## [模板的 VS Code 扩展](resource-manager-vscode-extension.md)
## [Visual Studio 与 Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# 示例
## [代码示例](https://azure.microsoft.com/en-us/resources/samples/?service=azure-resource-manager)
## Azure PowerShell
### [部署模板](resource-manager-samples-powershell-deploy.md)

## Azure CLI
### [部署模板](resource-manager-samples-cli-deploy.md)

# 如何
## 创建模板
### [模板节](resource-group-authoring-templates.md)
### [模板最佳实践](resource-manager-template-best-practices.md)
### [链接到其他模板](resource-group-linked-templates.md)
### [定义资源之间的依赖关系](resource-group-define-dependencies.md)
### [创建多个实例](resource-group-create-multiple.md)
### [设置位置](resource-manager-template-location.md)
### [分配标记](resource-manager-template-tags.md)
### [设置子资源名称和类型](resource-manager-template-child-resource.md)
### [更新资源](resource-manager-update.md)
### [对参数使用对象](resource-manager-objects-as-parameters.md)
### [在链接模板之间共享状态](best-practices-resource-manager-state.md)
### [模板设计模式](best-practices-resource-manager-design-templates.md)


## 部署
### Azure PowerShell
#### [部署模板](resource-group-template-deploy.md)
#### [使用 SAS 令牌部署专用模板](resource-manager-powershell-sas-token.md)
#### [导出模板并进行重新部署](resource-manager-export-template-powershell.md)
### Azure CLI
#### [部署模板](resource-group-template-deploy-cli.md)
#### [使用 SAS 令牌部署专用模板](resource-manager-cli-sas-token.md)
#### [导出模板并进行重新部署](resource-manager-export-template-cli.md)
### Azure 门户
#### [部署资源](resource-group-template-deploy-portal.md)
#### [导出模板](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [跨资源组部署](resource-manager-cross-resource-group-deployment.md)
### [与 Visual Studio Team Services 的持续集成](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [在部署期间传递安全值](resource-manager-keyvault-parameter.md)

## 管理
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure 门户](resource-group-portal.md)
### [REST API](resource-manager-rest-api.md)
### [使用标记来组织资源](resource-group-using-tags.md)
### [将资源移到新组或订阅](resource-group-move-resources.md)
### [使用管理组组织订阅](../billing/billing-enterprise-mgmt-group-overview.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [监管示例](resource-manager-subscription-examples.md)
### [托管应用程序](../managed-applications/overview.md)

## 控制访问
### 创建服务主体
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure 门户](resource-group-create-service-principal-portal.md)
### [用于访问订阅的身份验证 API](resource-manager-api-authentication.md)
### [锁定资源](resource-group-lock-resources.md)

## 设置资源策略
### [什么是资源策略？](resource-manager-policy.md)
### [使用 Azure 门户分配策略](resource-manager-policy-portal.md)
### [使用脚本分配策略](resource-manager-policy-create-assign.md)
### 示例
#### [标记](resource-manager-policy-tags.md)
#### [命名约定](resource-manager-policy-naming-convention.md)
#### [网络](resource-manager-policy-network.md)
#### [存储](resource-manager-policy-storage.md)
#### [Linux VM](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Windows VM](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## 审核
### [查看活动日志](resource-group-audit.md)
### [查看部署操作](resource-manager-deployment-operations.md)

## 故障排除
### [常见部署错误](resource-manager-common-deployment-errors.md)
### [了解部署错误](resource-manager-troubleshoot-tips.md)
### 解决错误
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
#### [InvalidTemplate](resource-manager-invalid-template-errors.md)
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
#### [NotFound](resource-manager-not-found-errors.md)
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### 虚拟机部署错误
#### Linux
##### [部署问题](../virtual-machines/linux/troubleshoot-deploy-vm.md)
##### [预配和分配问题](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
##### [常见错误消息](../virtual-machines/linux/error-messages.md)
#### Windows
##### [部署问题](../virtual-machines/windows/troubleshoot-deploy-vm.md)
##### [预配和分配问题](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
##### [常见错误消息](../virtual-machines/windows/error-messages.md)

# 引用
## [模板格式](/azure/templates/)
## [模板函数](resource-group-template-functions.md)
### [数组和对象函数](resource-group-template-functions-array.md)
### [比较函数](resource-group-template-functions-comparison.md)
### [部署函数](resource-group-template-functions-deployment.md)
### [逻辑函数](resource-group-template-functions-logical.md)
### [数值函数](resource-group-template-functions-numeric.md)
### [资源函数](resource-group-template-functions-resource.md)
### [字符串函数](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [服务更新](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [限制请求](resource-manager-request-limits.md)
## [跟踪异步操作](resource-manager-async-operations.md)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
