# 概述
## [什么是 Resource Manager？](resource-group-overview.md)
## [支持的服务](resource-manager-supported-services.md)
## [Resource Manager 部署和经典部署](resource-manager-deployment-model.md)
## [订阅监管](resource-manager-subscription-governance.md)
## [托管应用程序](managed-application-overview.md)

# 入门
## [导出模板](resource-manager-export-template.md)
## [创建第一个模板](resource-manager-create-first-template.md)
## [Visual Studio 与 Resource Manager](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# 示例
## PowerShell
### [部署模板](resource-manager-samples-powershell-deploy.md)

## Azure CLI
### [部署模板](resource-manager-samples-cli-deploy.md)

# 如何
## 创建模板
### [模板最佳实践](resource-manager-template-best-practices.md)
### [模板节](resource-group-authoring-templates.md)
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
### PowerShell
#### [部署模板](resource-group-template-deploy.md)
#### [使用 SAS 令牌部署专用模板](resource-manager-powershell-sas-token.md)
#### [导出模板并进行重新部署](resource-manager-export-template-powershell.md)
### Azure CLI
#### [部署模板](resource-group-template-deploy-cli.md)
#### [使用 SAS 令牌部署专用模板](resource-manager-cli-sas-token.md)
#### [导出模板并进行重新部署](resource-manager-export-template-cli.md)
### [门户](resource-group-template-deploy-portal.md)
### [REST API](resource-group-template-deploy-rest.md)
### [跨资源组部署](resource-manager-cross-resource-group-deployment.md)
### [与 Visual Studio Team Services 的持续集成](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [在部署期间传递安全值](resource-manager-keyvault-parameter.md)

## 管理
### [PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [门户](resource-group-portal.md)
### [REST API](resource-manager-rest-api.md)
### [使用标记来组织资源](resource-group-using-tags.md)
### [将资源移到新组或订阅](resource-group-move-resources.md)
### [监管示例](resource-manager-subscription-examples.md)

## 控制访问
### 创建服务主体
#### [PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure CLI 1.0](resource-group-authenticate-service-principal-cli.md)
#### [门户](resource-group-create-service-principal-portal.md)
### [用于访问订阅的身份验证 API](resource-manager-api-authentication.md)
### [锁定资源](resource-group-lock-resources.md)
### [安全注意事项](best-practices-resource-manager-security.md)

## 设置资源策略
### [什么是资源策略？](resource-manager-policy.md)
### [门户策略分配](resource-manager-policy-portal.md)
### [脚本策略分配](resource-manager-policy-create-assign.md)
### [资源标记策略](resource-manager-policy-tags.md)
### [存储策略](resource-manager-policy-storage.md)
### [Linux VM 策略](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Windows VM 策略](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## 使用托管应用程序
### [发布托管应用程序](managed-application-publishing.md)
### [使用托管应用程序](managed-application-consumption.md)
### [创建 UI 定义](managed-application-createuidefinition-overview.md)

## 审核
### [查看活动日志](resource-group-audit.md)
### [查看部署操作](resource-manager-deployment-operations.md)

## 故障排除
### [常见部署错误](resource-manager-common-deployment-errors.md)

# 引用
## [模板格式](/azure/templates/)
## [模板函数](resource-group-template-functions.md)
### [数组和对象函数](resource-group-template-functions-array.md)
### [比较函数](resource-group-template-functions-comparison.md)
### [部署函数](resource-group-template-functions-deployment.md)
### [数值函数](resource-group-template-functions-numeric.md)
### [资源函数](resource-group-template-functions-resource.md)
### [字符串函数](resource-group-template-functions-string.md)
## [UI 定义函数](managed-application-createuidefinition-functions.md)
## [UI 定义元素](managed-application-createuidefinition-elements.md)
### [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
### [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
### [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
### [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
### [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
### [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
### [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
### [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
### [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
### [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
### [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
### [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
### [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# 资源
## [限制请求](resource-manager-request-limits.md)
## [跟踪异步操作](resource-manager-async-operations.md)
## [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
## [服务更新](https://azure.microsoft.com/updates/?product=azure-resource-manager)
