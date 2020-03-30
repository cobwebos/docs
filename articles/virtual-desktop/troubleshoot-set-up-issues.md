---
title: Windows 虚拟桌面租户主机池创建 - Azure
description: 在设置 Windows 虚拟桌面租户环境期间如何排除和解决租户和主机池问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371590"
---
# <a name="tenant-and-host-pool-creation"></a>创建租户和主机池

本文介绍 Windows 虚拟桌面租户和相关会话主机池基础结构的初始设置期间的问题。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>获取 Windows 10 企业多会话映像

要使用 Windows 10 企业多会话映像，请转到 Azure 应用商店，选择"**启动** > **Microsoft Windows 10** >和[适用于虚拟桌面的 Windows 10 企业版，版本 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)。

![选择适用于虚拟桌面的 Windows 10 企业版的屏幕截图，版本 1809。](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>创建 Windows 虚拟桌面租户

本节介绍创建 Windows 虚拟桌面租户时的潜在问题。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>错误：用户无权查询管理服务

![无权查询管理服务的 PowerShell 窗口的屏幕截图。](media/UserNotAuthorizedNewTenant.png)

原始错误示例：

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

**原因：** 已登录的用户尚未在其 Azure 活动目录中分配租户创建者角色。

**修复：** 按照将[租户创建者应用程序角色分配给 Azure 活动目录租户中的用户](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role)的说明进行操作。 按照说明操作后，您将将用户分配给租户创建者角色。

![已分配租户创建者角色的屏幕截图。](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>创建 Windows 虚拟桌面会话主机 VM

可以通过多种方式创建会话主机 VM，但 Windows 虚拟桌面团队仅支持与[Azure 应用商店](https://azuremarketplace.microsoft.com/)产品相关的 VM 预配问题。 有关详细信息，请参阅使用[Windows 虚拟桌面的问题 - 预配主机池 Azure 应用商店产品](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)。

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>使用 Windows 虚拟桌面的问题 – 预配主机池 Azure 应用商店产品

Windows 虚拟桌面 – 预配主机池模板可从 Azure 应用商店获得。

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>错误：使用 GitHub 中的链接时，将显示消息"创建免费帐户"

![创建免费帐户的屏幕截图。](media/be615904ace9832754f0669de28abd94.png)

**原因 1：** 用于登录到 Azure 的帐户中没有活动订阅，或者所使用的帐户没有查看订阅的权限。

**修复 1：** 使用具有参与者访问权限（至少）的帐户登录，该订阅将部署会话主机 VM。

**原因 2：** 正在使用的订阅是 Microsoft 云服务提供商 （CSP） 租户的一部分。

**修复 2：** 转到 GitHub 位置**以创建和预配新的 Windows 虚拟桌面主机池**，然后按照以下说明操作：

1. 右键单击 **"部署到 Azure"** 并选择 **"复制链接地址**"。
2. 打开**记事本**并粘贴链接。
3. 在 # 字符之前，插入 CSP 最终客户租户名称。
4. 在浏览器中打开新链接，Azure 门户将加载模板。

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>错误：您收到"模板部署无效"错误

!["模板部署的屏幕截图...无效"错误](media/troubleshooting-marketplace-validation-error-generic.png)

在采取特定操作之前，您需要检查活动日志以查看部署验证失败的详细错误。

要查看活动日志中的错误，请进行以下工作：

1. 退出当前 Azure 应用商店部署产品。
2. 在顶部搜索栏中，搜索并选择**活动日志**。
3. 查找状态为 **"失败"** 的名为 **"验证部署"** 的活动并选择该活动。
   ![具有 [失败] 状态的单个 [验证部署] 活动的屏幕截图](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. 选择 JSON，然后向下滚动到屏幕底部，直到看到"状态消息"字段。
   ![失败活动的屏幕截图，JSON 文本的状态Message属性周围有一个红色框。](media/troubleshooting-marketplace-validation-error-json-boxed.png)

如果操作模板超出配额限制，则可以执行以下操作之一来修复它：

 - 使用首次使用的参数运行 Azure 应用商店，但这次使用的 VM 和 VM 内核较少。
 - 打开您在浏览器中**的状态Message**字段中看到的链接，以提交请求，以增加指定 VM SKU 的 Azure 订阅配额。

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure 资源管理器模板和 PowerShell 所需状态配置 （DSC） 错误

请按照这些说明对 Azure 资源管理器模板和 PowerShell DSC 的不成功部署进行故障排除。

1. 使用 Azure 资源管理器使用[视图部署操作](../azure-resource-manager/resource-manager-deployment-operations.md)查看部署中的错误。
2. 如果部署中没有错误，请使用"查看活动日志"审核活动日志中的错误[以审核对资源的操作](../azure-resource-manager/resource-group-audit.md)。
3. 识别错误后，请使用与[Azure 资源管理器一起排除常见 Azure 部署错误](../azure-resource-manager/resource-manager-common-deployment-errors.md)中的错误消息和资源来解决此问题。
4. 删除上次部署期间创建的任何资源，然后重试再次部署模板。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>错误：您的部署失败>...\<

![您的部署失败屏幕截图。](media/e72df4d5c05d390620e07f0d7328d50f.png)

原始错误示例：

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因 1：** 为将 VM 加入域提供的凭据不正确。

**修复 1：** 请参阅会话[主机 VM 配置](troubleshoot-vm-configuration.md)中 VM 中未加入域的 VM 的"凭据不正确"错误。

**原因 2：** 域名无法解析。

**修复 2：** 请参阅错误：在[会话主机 VM 配置](troubleshoot-vm-configuration.md)中[，域名未解析](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve)。

**原因 3：** 您的虚拟网络 （VNET） DNS 配置设置为 **"默认**"。

要解决此问题，可以执行以下操作：

1. 打开 Azure 门户并转到**虚拟网络**选项卡。
2. 查找您的 VNET，然后选择**DNS 服务器**。
3. DNS 服务器菜单应显示在屏幕右侧。 在该菜单上，选择 **"自定义**"。
4. 确保自定义下列出的 DNS 服务器与您的域控制器或活动目录域匹配。 如果看不到 DNS 服务器，则可以通过在"**添加 DNS 服务器"** 字段中输入其值来添加它。

### <a name="error-your-deployment-failedunauthorized"></a>错误：您的部署失败...

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**原因：** 您使用的订阅是无法访问客户尝试部署的区域所需的功能的类型。 例如，MSDN、免费订阅或教育订阅可以显示此错误。

**修复：** 将订阅类型或区域更改为可以访问所需功能的区域。

### <a name="error-vmextensionprovisioningerror"></a>错误：VM 扩展预配错误

![部署的屏幕截图失败，终端预配状态失败。](media/7aaf15615309c18a984673be73ac969a.png)

**原因 1：** Windows 虚拟桌面环境的暂时性错误。

**原因 2：** 连接时瞬态错误。

**修复：** 使用 PowerShell 登录确认 Windows 虚拟桌面环境是否正常。 在[使用 PowerShell 创建主机池](create-host-pools-powershell.md)中手动完成 VM 注册。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>错误：不允许指定管理员用户名

![部署的屏幕截图失败，不允许指定管理员。](media/f2b3d3700e9517463ef88fa41875bac9.png)

原始错误示例：

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**原因：** 提供的密码包含禁止的子字符串（管理员、管理员、根）。

**修复：** 更新用户名或使用不同的用户。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>错误：VM 在处理扩展时报告失败

![在部署失败中终端预配状态完成的资源操作的屏幕截图。](media/49c4a1836a55d91cd65125cf227f411f.png)

原始错误示例：

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
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

**原因：** PowerShell DSC 扩展无法在 VM 上获取管理员访问权限。

**修复：** 确认用户名和密码在虚拟机上具有管理访问权限，然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>错误：部署失败 – 电源外壳 DSC 配置"第一会话主机"完成，错误（s）

![使用 PowerShell DSC 配置"第一会话主机"完成且错误完成的部署屏幕截图失败。](media/64870370bcbe1286906f34cf0a8646ab.png)

原始错误示例：

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

**原因：** PowerShell DSC 扩展无法在 VM 上获取管理员访问权限。

**修复：** 确认提供的用户名和密码在虚拟机上具有管理访问权限，然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误：部署失败 = 无效资源引用

原始错误示例：

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

**原因：** 资源组名称的一部分用于模板正在创建的某些资源。 由于与现有资源匹配的名称，模板可以从其他组中选择现有资源。

**修复：** 运行 Azure 资源管理器模板以部署会话主机 VM 时，使订阅资源组名称的前两个字符唯一。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误：部署失败 = 无效资源引用

原始错误示例：

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

**原因：** 此错误是因为使用 Azure 资源管理器模板创建的 NIC 的名称与 VNET 中已有的另一个 NIC 具有相同的名称。

**修复：** 使用其他主机前缀。

### <a name="error-deploymentfailed--error-downloading"></a>错误：部署失败 = 下载错误

原始错误示例：

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**原因：** 此错误是由于静态路由、防火墙规则或 NSG 阻止下载绑定到 Azure 资源管理器模板的 zip 文件。

**修复：** 删除阻塞的静态路由、防火墙规则或 NSG。 或者，在文本编辑器中打开 Azure 资源管理器模板 json 文件，获取指向 zip 文件的链接，并将资源下载到允许的位置。

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>错误：用户无权查询管理服务

原始错误示例：

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

**原因：** 指定的 Windows 虚拟桌面租户管理员没有有效的角色分配。

**修复：** 创建 Windows 虚拟桌面租户的用户需要登录到 Windows 虚拟桌面 PowerShell，并分配尝试的用户角色分配。 如果您正在运行 GitHub Azure 资源管理器模板参数，请使用 PowerShell 命令按照以下说明操作：

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>错误：用户需要 Azure 多重身份验证 （MFA）

![由于缺乏多重身份验证 （MFA），部署的屏幕截图失败](media/MFARequiredError.png)

原始错误示例：

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**原因：** 指定的 Windows 虚拟桌面租户管理员需要 Azure 多重身份验证 （MFA） 才能登录。

**修复：** 按照教程中的步骤：[使用 PowerShell 创建服务主体和角色分配，](create-service-principal-role-powershell.md)创建服务主体并为 Windows 虚拟桌面租户分配角色。 在验证可以使用服务主体登录到 Windows 虚拟桌面后，请重新运行 Azure 应用商店产品或 GitHub Azure 资源管理器模板，具体取决于您正在使用的方法。 按照以下说明输入方法的正确参数。

如果运行 Azure 应用商店产品/服务，请提供以下参数的值，以便对 Windows 虚拟桌面进行正确的身份验证：

- Windows 虚拟桌面租户 RDS 所有者：服务主体
- 应用程序 ID：您创建的新服务主体的应用程序标识
- 密码/确认密码：您为服务主体生成的密码密钥
- Azure AD 租户 ID：您创建的服务主体的 Azure AD 租户 ID

如果运行 GitHub Azure 资源管理器模板，请提供以下参数的值，以便对 Windows 虚拟桌面进行正确的身份验证：

- 租户管理员用户主体名称 （UPN） 或应用程序 ID：您创建的新服务主体的应用程序标识
- 租户管理员密码：您为服务主体生成的密码密钥
- 服务主体：**真实**
- AadTenantId：您创建的服务主体的 Azure AD 租户 ID

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面和升级跟踪的故障排除概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 要在 Windows 虚拟桌面中配置虚拟机 （VM） 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 要解决 Windows 虚拟桌面客户端连接的问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 要解决远程桌面客户端的问题，请参阅[排除远程桌面客户端的故障](troubleshoot-client.md)
- 要解决在 Windows 虚拟桌面中使用 PowerShell 时的问题，请参阅[Windows 虚拟桌面电源外壳](troubleshoot-powershell.md)。
- 要了解有关该服务的更多信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
- 要完成疑难解答教程，请参阅[教程：解决资源管理器模板部署的疑难解答](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
