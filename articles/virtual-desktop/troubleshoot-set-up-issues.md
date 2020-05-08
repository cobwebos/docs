---
title: Windows 虚拟桌面环境主机池创建-Azure
description: 如何在 Windows 虚拟桌面环境的安装过程中排除和解决租户和主机池问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 65a61babe58e1cb9438262186a7f4cf37cb10a34
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612523"
---
# <a name="host-pool-creation"></a>主机池创建

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/troubleshoot-set-up-issues-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍了 Windows 虚拟桌面租户和相关的会话主机池基础结构的初始安装过程中的问题。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>获取 Windows 10 企业多会话映像

若要使用 Windows 10 企业多会话映像，请转到 Azure Marketplace，选择 "**入门** > **Microsoft Windows 10** > 和[Windows 10 企业版多会话，版本 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)。

## <a name="issues-with-using-the-azure-portal-to-create-host-pools"></a>使用 Azure 门户创建主机池时遇到的问题

### <a name="error-create-a-free-account-appears-when-accessing-the-service"></a>错误：访问服务时出现 "创建免费帐户"

![显示 "创建免费帐户" 消息 Azure 门户的图像](media/create-new-account.png)

**原因**：你使用登录到 Azure 的帐户中没有活动的订阅，或者该帐户无权查看订阅。 

**修复**：使用至少具有参与者级别访问权限的帐户登录到要将会话主机虚拟机（vm）部署到的订阅。

### <a name="error-exceeding-quota-limit"></a>错误： "超过配额限制"

如果操作超过配额限制，可以执行以下操作之一： 

- 使用相同的参数创建一个新的主机池，但 Vm 和 VM 内核数更少。

- 在浏览器中打开在 statusMessage 字段中看到的链接，以提交为指定 VM SKU 增加 Azure 订阅配额的请求。

## <a name="azure-resource-manager-template-errors"></a>Azure 资源管理器模板错误

按照以下说明解决 Azure 资源管理器模板和 PowerShell DSC 的不成功部署。

1. 使用[Azure 资源管理器的 "查看部署" 操作](../azure-resource-manager/resource-manager-deployment-operations.md)查看部署中的错误。
2. 如果部署中没有错误，请使用查看活动日志查看活动日志中的错误，[以审核对资源的操作](../azure-resource-manager/resource-group-audit.md)。
3. 确定错误后，请使用错误消息和资源[解决 azure 资源管理器的常见 Azure 部署错误](../azure-resource-manager/resource-manager-common-deployment-errors.md)，以解决此问题。
4. 删除在之前的部署过程中创建的任何资源，然后重试部署模板。

### <a name="error-your-deployment-failedhostnamejoindomain"></a>错误：部署失败 ...\<主机名>/joindomain

![部署失败，屏幕截图。](media/failure-joindomain.png)

原始错误的示例：

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**原因1：** 为将 Vm 加入到域而提供的凭据不正确。

**修复1：** 如果 Vm 未加入[会话主机 VM 配置](troubleshoot-vm-configuration.md)中的域，请参阅 "凭据错误"。

**原因2：** 域名未解析。

**修复2：** 请参阅[错误：未](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve)在[会话主机 VM 配置](troubleshoot-vm-configuration.md)中解析域名。

**原因3：** 虚拟网络（VNET） DNS 配置已设置为**默认值**。

若要解决此问题，请执行以下操作：

1. 打开 Azure 门户并中转到 "**虚拟网络**" 选项卡。
2. 找到 VNET，然后选择 " **DNS 服务器**"。
3. "DNS 服务器" 菜单应该出现在屏幕的右侧。 在该菜单上，选择 "**自定义**"。
4. 请确保 "自定义" 下列出的 DNS 服务器匹配域控制器或 Active Directory 域。 如果看不到你的 DNS 服务器，可以通过在 "**添加 dns 服务器**" 字段中输入其值来添加它。

### <a name="error-your-deployment-failedunauthorized"></a>错误：部署失败。 ..\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**原因：** 你使用的订阅的类型无法访问客户尝试部署的区域中的所需功能。 例如，MSDN、免费或教育订阅可能显示此错误。

**修复：** 更改你的订阅类型或区域，使其可以访问所需的功能。

### <a name="error-vmextensionprovisioningerror"></a>错误： VMExtensionProvisioningError

![部署的屏幕截图失败，终端预配状态为 "失败"。](media/failure-vmextensionprovisioning.png)

**原因1：** Windows 虚拟桌面环境出现暂时性错误。

**原因2：** 与连接发生暂时性错误。

**修复：** 使用 PowerShell 登录，确认 Windows 虚拟桌面环境正常运行。 在[使用 PowerShell 创建主机池](create-host-pools-powershell.md)中手动完成 VM 注册。

### <a name="error-the-admin-username-specified-isnt-allowed"></a>错误：不允许指定的管理员用户名

![不允许管理员指定的部署的屏幕截图。](media/failure-username.png)

原始错误的示例：

```Error
 { …{ "provisioningOperation": 
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId": 
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message": 
 "The Admin Username specified is not allowed.", "target": "adminUsername" } … }
```

**原因：** 提供的密码包含禁止的子字符串（admin、administrator、root）。

**修复：** 更新用户名或使用其他用户。

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>错误： VM 在处理扩展时报告了故障

![部署中具有终端预配状态的资源操作的屏幕截图失败。](media/failure-processing.png)

原始错误的示例：

```Error
{ … "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code": 
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] … }
```

**原因：** PowerShell DSC 扩展无法获取 VM 上的管理员访问权限。

**修复：** 确认用户名和密码在虚拟机上具有管理访问权限，然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>错误： DeploymentFailed – PowerShell DSC 配置 "FirstSessionHost" 已完成，但出现错误

![部署的屏幕截图失败，PowerShell DSC 配置 "FirstSessionHost" 已完成，但出现错误。](media/failure-dsc.png)

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

**原因：** PowerShell DSC 扩展无法获取 VM 上的管理员访问权限。

**修复：** 确认提供的用户名和密码对虚拟机具有管理访问权限，然后再次运行 Azure 资源管理器模板。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误： DeploymentFailed – InvalidResourceReference

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

**原因：** 资源组名称的一部分用于由模板创建的某些资源。 由于与现有资源匹配的名称，模板可能会从不同的组中选择现有资源。

**修复：** 运行 Azure 资源管理器模板来部署会话主机 Vm 时，请使订阅资源组名称具有唯一的前两个字符。

### <a name="error-deploymentfailed--invalidresourcereference"></a>错误： DeploymentFailed – InvalidResourceReference

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

**原因：** 此错误是因为通过 Azure 资源管理器模板创建的 NIC 与 VNET 中的另一个 NIC 具有相同的名称。

**修复：** 使用其他主机前缀。

### <a name="error-deploymentfailed--error-downloading"></a>错误： DeploymentFailed –错误下载

原始错误的示例：

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

**原因：** 此错误是由于静态路由、防火墙规则或 NSG 阻止下载关联到 Azure 资源管理器模板的 zip 文件引起的。

**修复：** 删除阻止的静态路由、防火墙规则或 NSG。 （可选）在文本编辑器中打开 Azure 资源管理器模板 json 文件，获取 zip 文件的链接，并将资源下载到允许的位置。

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面故障排除和升级跟踪的概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机（VM）时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决 Windows 虚拟桌面客户端连接问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要解决远程桌面客户端的问题，请参阅[排查远程桌面客户端](troubleshoot-client.md)问题
- 若要解决将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关该服务的详细信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
- 若要浏览疑难解答教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
