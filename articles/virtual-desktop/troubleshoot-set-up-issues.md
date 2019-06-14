---
title: Windows 虚拟桌面租户和主机创建池-Azure
description: 如何排除故障并解决租户和主机的池的问题在 Windows 虚拟桌面租户环境的安装过程。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 88e843c410a750387ecf58497dec79586e2a59d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523331"
---
# <a name="tenant-and-host-pool-creation"></a>创建租户和主机池

本文介绍如何在 Windows 虚拟桌面租户和相关的会话主机池基础结构的初始设置过程的问题。

## <a name="provide-feedback"></a>提供反馈

目前我们不会受理 Windows 虚拟桌面预览版的支持案例。 请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>获取 Windows 10 企业版多会话图像

若要使用 Windows 10 企业版多会话映像，请转到 Azure Marketplace 中，选择**开始** > **Microsoft Windows 10** > 和[为 Windows 10 企业版虚拟桌面预览、 版本 1809年](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)。

![对于虚拟桌面预览版，版本 1809年选择 Windows 10 企业版的屏幕截图。](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>创建 Windows 虚拟桌面租户

创建 Windows 虚拟桌面租户时，此部分介绍了潜在的问题。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>错误：用户未经授权来查询管理服务

![用户未授权管理服务进行的查询的屏幕截图的 PowerShell 窗口。](media/UserNotAuthorizedNewTenant.png)

原始错误的示例：

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

原因：  尚未在其 Azure Active Directory 中的 TenantCreator 角色分配已登录的用户。

**解决方法：** 按照中的说明[将 TenantCreator 应用程序角色分配给 Azure Active Directory 租户中用户](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant)。 按照说明进行操作之后, 您必须分配到 TenantCreator 角色的用户。

![屏幕截图的 TenantCreator 分配角色。](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>创建 Windows 虚拟桌面会话主机 Vm

可以通过多种方式创建会话主机 Vm，但远程桌面服务/Windows 虚拟桌面团队仅支持 VM 预配 Azure 资源管理器模板与相关的问题。 Azure 资源管理器模板现已推出[Azure Marketplace](https://azuremarketplace.microsoft.com/)并[GitHub](https://github.com/)。

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>使用 Windows 虚拟桌面 – 预配主机池 Azure Marketplace 产品/服务的问题

Windows 虚拟桌面 – 预配主机池模板是可从 Azure Marketplace。

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>错误：当使用 GitHub 中的链接，该消息"创建免费帐户"会显示

![创建一个免费帐户的屏幕截图。](media/be615904ace9832754f0669de28abd94.png)

**原因 1：** 没有用来登录到 Azure 的帐户中的活动订阅或使用的帐户没有权限查看订阅。

**修复 1:** 使用具有会话主机 Vm 将要部署 （至少） 对订阅的参与者访问权限的帐户登录。

**原因 2：** 所使用的订阅是 Microsoft 云服务提供商 (CSP) 租户的一部分。

**修复 2:** 转到 GitHub 位置**创建和预配新的 Windows 虚拟机主机池**按照这些说明进行操作：

1. 右键单击**部署到 Azure** ，然后选择**复制链接地址**。
2. 打开**记事本**并粘贴的链接。
3. 在 # 字符之前插入 CSP 最终客户租户名称。
4. 打开一个浏览器和 Azure 门户中的新链接将加载模板。

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure 资源管理器模板和 PowerShell Desired State Configuration (DSC) 错误

按照以下说明来解决 Azure 资源管理器模板和 PowerShell DSC 的不成功部署。

1. 查看在部署中使用的错误[查看部署操作与 Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
2. 如果部署中没有任何错误，请查看在活动日志中使用的错误[查看活动日志以审核对资源的操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
3. 一旦确定该错误，使用的错误消息和中的资源[排查使用 Azure 资源管理器中的常见 Azure 部署错误](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors)来解决问题。
4. 删除以前的部署和重新部署模板的重试过程中创建任何资源。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>错误：你的部署已失败...<hostname>/joindomain

![你的部署失败的屏幕截图。](media/e72df4d5c05d390620e07f0d7328d50f.png)

原始错误的示例：

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因 1：** 提供用于将 Vm 加入到域的凭据不正确。

**修复 1:** Vm 未加入到域中，请参见"不正确的凭据"错误[会话主机 VM 配置](troubleshoot-vm-configuration.md)。

**原因 2：** 未解析的域名。

**修复 2:** Vm 未加入到域中，请参见"域名不能解决"错误[会话主机 VM 配置](troubleshoot-vm-configuration.md)。

### <a name="error-vmextensionprovisioningerror"></a>错误：VMExtensionProvisioningError

![屏幕截图的您部署失败，出现终端预配状态失败。](media/7aaf15615309c18a984673be73ac969a.png)

**原因 1：** 与 Windows 虚拟桌面环境的暂时性错误。

**原因 2：** 与连接的暂时性错误。

**解决方法：** 确认 Windows 虚拟桌面环境处于正常状态的签名中使用 PowerShell。 完成 VM 注册中手动[使用 PowerShell 创建主机池](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>错误：不允许使用指定的管理员用户名

![你部署的屏幕截图中不允许管理员指定的失败。](media/f2b3d3700e9517463ef88fa41875bac9.png)

原始错误的示例：

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

原因：  提供的密码包含已禁止的子字符串 （管理员、 管理员和根）。

**解决方法：** 更新用户名或使用不同的用户。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>错误：VM 在处理扩展插件时报告失败

![资源操作完成时在中部署失败，您的终端预配状态的屏幕截图。](media/49c4a1836a55d91cd65125cf227f411f.png)

原始错误的示例：

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

原因：  PowerShell DSC 扩展程序无法在 VM 上获取管理员访问权限。

**解决方法：** 确认用户名和密码在虚拟机上具有管理访问权限，然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>错误：DeploymentFailed – PowerShell DSC 配置 FirstSessionHost 已完成但出现错误

![通过 PowerShell DSC 配置 FirstSessionHost 已完成，但错误失败部署的屏幕截图。](media/64870370bcbe1286906f34cf0a8646ab.png)

原始错误的示例：

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

原因：  PowerShell DSC 扩展程序无法在 VM 上获取管理员访问权限。

**解决方法：** 确认提供用户名和密码在虚拟机上具有管理访问权限并再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误：DeploymentFailed – InvalidResourceReference

原始错误的示例：

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

原因：  资源组名称的一部分用于特定模板创建的资源。 由于匹配现有资源的名称，模板可能从不同组中选择现有资源。

**解决方法：** 运行时要部署会话主机 Vm 的 Azure 资源管理器模板，请对你的订阅资源组名称是唯一前两个字符。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误：DeploymentFailed – InvalidResourceReference

原始错误的示例：

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

原因：  此错误是因为使用 Azure 资源管理器模板创建的 NIC 具有与另一个 NIC 相同的名称已在 VNET 中。

**解决方法：** 使用不同的主机前缀。

### <a name="error-deploymentfailed--error-downloading"></a>错误：DeploymentFailed – 下载时出错

原始错误的示例：

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

原因：  此错误是由于静态路由、 防火墙规则或 NSG 阻止绑定到 Azure 资源管理器模板的 zip 文件下载。

**解决方法：** 删除静态路由、 防火墙规则或 NSG 阻止。 （可选） 在文本编辑器中打开 Azure 资源管理器模板 json 文件链接到 zip 文件，并采取将资源下载到允许的位置。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>错误：用户未经授权来查询管理服务

原始错误的示例：

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

原因：  指定的 Windows 虚拟桌面租户管理员不具有有效的角色分配。

**解决方法：** 创建 Windows 虚拟桌面租户的用户需要登录到 Windows 虚拟桌面 PowerShell 并将尝试的用户分配角色分配。 如果运行的 GitHub Azure 资源管理器模板参数，请按照这些说明使用 PowerShell 命令：

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>错误：用户需要 Azure 多重身份验证 (MFA)

![部署缺少的多重身份验证 (MFA) 由于失败的屏幕截图](media/MFARequiredError.png)

原始错误的示例：

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

原因：  指定的 Windows 虚拟桌面租户管理员需要 Azure 多重身份验证 (MFA) 进行登录。

**解决方法：** 创建服务主体并将其分配一个角色为 Windows 虚拟桌面租户中的步骤[教程：使用 PowerShell 创建服务主体和角色分配](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)。 验证后，你可以使用登录到 Windows 虚拟桌面的服务主体，请重新运行 Azure Marketplace 产品/服务或 GitHub Azure 资源管理器模板，具体取决于哪种方法使用。 请按照以下说明为你的方法中输入正确的参数。

如果运行的 Azure Marketplace 产品/服务，提供以下参数来正确通过身份验证到 Windows 虚拟机的值：

- Windows 虚拟桌面租户 RDS 所有者：服务主体
- 应用程序 ID：创建新的服务主体的应用程序标识
- 密码/确认密码：生成的服务主体的密码
- Azure AD 租户 ID:创建服务主体的 Azure AD 租户 ID

如果运行的 GitHub Azure 资源管理器模板，提供以下参数来正确通过身份验证到 Windows 虚拟机的值：

- 租户管理员用户主体名称 (UPN) 或应用程序 ID:创建新的服务主体的应用程序标识
- 租户管理员密码：生成的服务主体的密码
- IsServicePrincipal: **，则返回 true**
- AadTenantId:创建服务主体的 Azure AD 租户 ID

## <a name="next-steps"></a>后续步骤

- 有关故障排除 Windows 虚拟桌面，升级进行跟踪的概述，请参阅[故障排除概述、 反馈和支持](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虚拟机中配置虚拟机 (VM) 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要对 Windows 虚拟桌面客户端连接的问题进行故障排除，请参阅[远程桌面客户端连接](troubleshoot-client-connection.md)。
- 若要排查问题时使用 PowerShell 管理 Windows 虚拟桌面，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关预览服务的详细信息，请参阅[Windows Desktop 预览环境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要完成故障排除教程，请参阅[教程：对资源管理器模板部署进行故障排除](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。