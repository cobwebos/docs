# [Azure 资源管理器文档](index.md)

# 概述
## [什么是 Resource Manager？](resource-group-overview.md)
## [资源提供程序和类型](resource-manager-supported-services.md)
## [Resource Manager 部署和经典部署](resource-manager-deployment-model.md)
## [订阅监管](resource-manager-subscription-governance.md)
## [使用管理组组织资源](management-groups-overview.md)

# 入门
## [创建和部署模板](resource-manager-create-first-template.md)
## [模板的 VS Code 扩展](resource-manager-vscode-extension.md)
## [Visual Studio 与 Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# 如何
## 创建模板
### [模板节](resource-group-authoring-templates.md)
#### [参数](resource-manager-templates-parameters.md)
#### [变量](resource-manager-templates-variables.md)
#### [资源](resource-manager-templates-resources.md)
#### [输出](resource-manager-templates-outputs.md)
### [链接和嵌套模板](resource-group-linked-templates.md)
### [定义资源之间的依赖关系](resource-group-define-dependencies.md)
### [创建多个实例](resource-group-create-multiple.md)
### [更新资源](/azure/architecture/building-blocks/extending-templates/update-resource)

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
### [多个资源组或订阅](resource-manager-cross-resource-group-deployment.md)
### [与 Visual Studio Team Services 的持续集成](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [在部署期间传递安全值](resource-manager-keyvault-parameter.md)

## 管理
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure portal](resource-group-portal.md)
### [使用标记来组织资源](resource-group-using-tags.md)
### [将资源移到新组或订阅](resource-group-move-resources.md)
### [创建管理组](management-groups-create.md)
### [管理你的管理组](management-groups-manage.md)
### [监管示例](resource-manager-subscription-examples.md)

## 控制访问
### 创建服务主体
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure portal](resource-group-create-service-principal-portal.md)
### [用于访问订阅的身份验证 API](resource-manager-api-authentication.md)
### [锁定资源](resource-group-lock-resources.md)

## 审核
### [查看活动日志](resource-group-audit.md)
### [查看部署操作](resource-manager-deployment-operations.md)

## 故障排除
### [常见部署错误](resource-manager-common-deployment-errors.md)
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
#### [InvalidTemplate](resource-manager-invalid-template-errors.md)
#### [Linux 部署问题](../virtual-machines/linux/troubleshoot-deploy-vm.md)
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
#### [NotFound](resource-manager-not-found-errors.md)
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [Linux 预配和分配问题](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
#### [Windows 预配和分配问题](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
#### [ReservedResourceName](resource-manager-reserved-resource-name.md)
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
#### [Windows 部署问题](../virtual-machines/windows/troubleshoot-deploy-vm.md)
### [了解部署错误](resource-manager-troubleshoot-tips.md)

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
