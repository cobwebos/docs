---
title: Windows 虚拟桌面租户和主机池创建-Azure
description: 如何在 Windows 虚拟桌面租户环境的安装过程中排除和解决租户和主机池问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: 7ab8ec9536af74102d2c9384ea3d0d0503f58f63
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816492"
---
# <a name="tenant-and-host-pool-creation"></a>创建租户和主机池

本文介绍了 Windows 虚拟桌面租户和相关的会话主机池基础结构的初始安装过程中的问题。

## <a name="provide-feedback"></a>提供反馈

目前我们不会受理 Windows 虚拟桌面预览版的支持案例。 请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>获取 Windows 10 企业多会话映像

若要使用 Windows 10 企业多会话映像, 请转到 Azure Marketplace, 选择 "**入门** > **Microsoft Windows 10** > 和[用于虚拟桌面的 Windows 10 企业版预览, 版本 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)。

![为虚拟桌面预览版选择 Windows 10 企业版 (版本 1809) 的屏幕截图。](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>正在创建 Windows 虚拟桌面租户

本部分介绍创建 Windows 虚拟桌面租户时可能出现的问题。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>错误：用户无权查询管理服务

![PowerShell 窗口的屏幕截图, 其中用户无权查询管理服务。](media/UserNotAuthorizedNewTenant.png)

原始错误的示例:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

原因：未在 Azure Active Directory 中为登录用户分配 "TenantCreator" 角色。

**能够**按照[为 Azure Active Directory 租户中的用户分配 TenantCreator 应用程序角色](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant)中的说明进行操作。 按照说明操作后, 你将拥有一个分配给 TenantCreator 角色的用户。

![已分配 TenantCreator 角色的屏幕截图。](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>正在创建 Windows 虚拟桌面会话主机 Vm

可以通过多种方式创建会话主机 Vm, 但远程桌面服务/Windows 虚拟桌面团队仅支持 Azure 资源管理器模板相关的 VM 设置问题。 Azure [Marketplace](https://azuremarketplace.microsoft.com/)和[GitHub](https://github.com/)中提供了 azure 资源管理器模板。

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>使用 Windows 虚拟桌面的问题–预配主机池 Azure Marketplace 产品/服务

Windows 虚拟桌面–预配主机池模板可从 Azure Marketplace 获得。

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>错误：使用 GitHub 中的链接时, 将显示消息 "创建免费帐户"

![用于创建免费帐户的屏幕截图。](media/be615904ace9832754f0669de28abd94.png)

**原因 1：** 用于登录到 Azure 的帐户中没有活动的订阅, 或者使用的帐户无权查看订阅。

**修复 1:** 使用至少具有参与者访问权限的帐户登录, 以便在其中部署会话主机 Vm。

**原因 2：** 使用的订阅是 Microsoft 云服务提供程序 (CSP) 租户的一部分。

**修复 2:** 请访问 GitHub 位置以**创建和设置新的 Windows 虚拟桌面主机池**, 并按照以下说明进行操作:

1. 右键单击 "**部署到 Azure** ", 然后选择 "**复制链接地址**"。
2. 打开**记事本**并粘贴链接。
3. 在 # 字符之前, 请插入 CSP 最终客户租户名称。
4. 在浏览器中打开新链接, Azure 门户将加载模板。

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure 资源管理器模板和 PowerShell 所需状态配置 (DSC) 错误

按照以下说明解决 Azure 资源管理器模板和 PowerShell DSC 的不成功部署。

1. 使用[Azure 资源管理器的 "查看部署" 操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)查看部署中的错误。
2. 如果部署中没有错误, 请使用查看活动日志查看活动日志中的错误,[以审核对资源的操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
3. 确定错误后, 请使用错误消息和资源[解决 azure 资源管理器的常见 Azure 部署错误](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors), 以解决此问题。
4. 删除在之前的部署过程中创建的任何资源, 然后重试部署模板。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>错误：部署失败 ...\<主机名 >/joindomain

![部署失败, 屏幕截图。](media/e72df4d5c05d390620e07f0d7328d50f.png)

原始错误的示例:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因 1：** 为将 Vm 加入到域而提供的凭据不正确。

**修复 1:** 如果 Vm 未加入[会话主机 VM 配置](troubleshoot-vm-configuration.md)中的域, 请参阅 "凭据错误"。

**原因 2：** 域名未解析。

**修复 2:** 如果 Vm 未加入[会话主机 VM 配置](troubleshoot-vm-configuration.md)中的域, 请参阅 "域名不能解析" 错误。


### <a name="error-your-deployment-failedunauthorized"></a>错误：部署失败。 ..\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

原因：你使用的订阅的类型无法访问客户尝试部署的区域中的所需功能。 例如, MSDN、免费或教育订阅可能显示此错误。

**能够**更改你的订阅类型或区域, 使其可以访问所需的功能。

### <a name="error-vmextensionprovisioningerror"></a>错误：VMExtensionProvisioningError

![部署的屏幕截图失败, 终端预配状态为 "失败"。](media/7aaf15615309c18a984673be73ac969a.png)

**原因 1：** Windows 虚拟桌面环境出现暂时性错误。

**原因 2：** 与连接发生暂时性错误。

**能够**使用 PowerShell 登录, 确认 Windows 虚拟桌面环境正常运行。 在[使用 PowerShell 创建主机池](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)中手动完成 VM 注册。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>错误：不允许指定的管理员用户名

![不允许管理员指定的部署的屏幕截图。](media/f2b3d3700e9517463ef88fa41875bac9.png)

原始错误的示例:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

原因：提供的密码包含禁止的子字符串 (admin、administrator、root)。

**能够**更新用户名或使用其他用户。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>错误：VM 在处理扩展时报告了故障

![部署中具有终端预配状态的资源操作的屏幕截图失败。](media/49c4a1836a55d91cd65125cf227f411f.png)

原始错误的示例:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

原因：PowerShell DSC 扩展无法获取 VM 上的管理员访问权限。

**能够**确认用户名和密码在虚拟机上具有管理访问权限, 然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>错误：DeploymentFailed – PowerShell DSC 配置 "FirstSessionHost" 已完成, 但出现错误

![部署的屏幕截图失败, PowerShell DSC 配置 "FirstSessionHost" 已完成, 但出现错误。](media/64870370bcbe1286906f34cf0a8646ab.png)

原始错误的示例:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

原因：PowerShell DSC 扩展无法获取 VM 上的管理员访问权限。

**能够**确认提供的用户名和密码对虚拟机具有管理访问权限, 然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误：DeploymentFailed – InvalidResourceReference

原始错误的示例:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

原因：资源组名称的一部分用于由模板创建的某些资源。 由于与现有资源匹配的名称, 模板可能会从不同的组中选择现有资源。

**能够**运行 Azure 资源管理器模板来部署会话主机 Vm 时, 请使订阅资源组名称具有唯一的前两个字符。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误：DeploymentFailed – InvalidResourceReference

原始错误的示例:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

原因：此错误是因为通过 Azure 资源管理器模板创建的 NIC 与 VNET 中的另一个 NIC 具有相同的名称。

**能够**使用其他主机前缀。

### <a name="error-deploymentfailed--error-downloading"></a>错误：DeploymentFailed –错误下载

原始错误的示例:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

原因：此错误是由于静态路由、防火墙规则或 NSG 阻止下载关联到 Azure 资源管理器模板的 zip 文件引起的。

**能够**删除阻止的静态路由、防火墙规则或 NSG。 (可选) 在文本编辑器中打开 Azure 资源管理器模板 json 文件, 获取 zip 文件的链接, 并将资源下载到允许的位置。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>错误：用户无权查询管理服务

原始错误的示例:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

原因：指定的 Windows 虚拟桌面租户管理员没有有效的角色分配。

**能够**创建 Windows 虚拟桌面租户的用户需要登录到 Windows 虚拟桌面 PowerShell, 并为尝试的用户分配角色分配。 如果正在运行 GitHub Azure 资源管理器模板参数, 请使用 PowerShell 命令执行以下说明:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>错误：用户需要 Azure 多重身份验证 (MFA)

![由于缺少多重身份验证 (MFA), 部署的屏幕截图失败](media/MFARequiredError.png)

原始错误的示例:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

原因：指定的 Windows 虚拟桌面租户管理员要求 Azure 多重身份验证 (MFA) 进行登录。

**能够**按照教程中[的步骤, 创建服务主体, 并为其分配 Windows 虚拟桌面租户的角色:在 PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)中创建服务主体和角色分配。 在验证是否可以使用服务主体登录到 Windows 虚拟桌面后, 重新运行 Azure Marketplace 产品/服务或 GitHub Azure 资源管理器模板, 具体取决于所使用的方法。 按照以下说明为方法输入正确的参数。

如果你运行的是 Azure Marketplace 产品/服务, 请提供以下参数的值, 以正确地向 Windows 虚拟桌面进行身份验证:

- Windows 虚拟桌面租户 RDS 所有者:服务主体
- 应用程序 ID:你创建的新服务主体的应用程序标识
- 密码/确认密码:为服务主体生成的密码密码
- Azure AD 租户 ID:你创建的服务主体的 Azure AD 租户 ID

如果正在运行 GitHub Azure 资源管理器模板, 请提供以下参数的值, 以正确地向 Windows 虚拟桌面进行身份验证:

- 租户管理员用户主体名称 (UPN) 或应用程序 ID:你创建的新服务主体的应用程序标识
- 租户管理员密码:为服务主体生成的密码密码
- IsServicePrincipal: **true**
- AadTenantId你创建的服务主体的 Azure AD 租户 ID

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面故障排除和升级跟踪的概述, 请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机 (VM) 时遇到的问题, 请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决 Windows 虚拟桌面客户端连接问题, 请参阅[远程桌面客户端连接](troubleshoot-client-connection.md)。
- 若要解决将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题, 请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关预览服务的详细信息, 请参阅[Windows 桌面预览环境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)问题。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
