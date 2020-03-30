---
title: 使用服务主体部署 Windows 虚拟桌面的管理工具 - Azure
description: 如何使用 PowerShell 部署 Windows 虚拟桌面的管理工具。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0838edb03c4868548f3d09f14d71ec7016e670a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127794"
---
# <a name="deploy-a-management-tool-with-powershell"></a>使用 PowerShell 部署管理工具

本文将介绍如何使用 PowerShell 部署管理工具。

## <a name="important-considerations"></a>重要注意事项

每个 Azure 活动目录 （Azure AD） 租户的订阅都需要单独部署管理工具。 此工具不支持 Azure AD 企业对企业 （B2B） 方案。 

此管理工具只是一个示例。 Microsoft 将提供重要的安全更新和质量更新。 [源代码在 GitHub 中可用](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)。 无论您是客户还是合作伙伴，我们鼓励您定制该工具以满足您的业务需求。

以下浏览器与管理工具兼容：

- Google Chrome 68 或更高版本
- Microsoft Edge 40.15063 或更高版本
- Mozilla Firefox 52.0 或更高版本
- Safari 10 或更高版本（仅限 macOS）

## <a name="what-you-need-to-deploy-the-management-tool"></a>部署管理工具所需满足的条件

在部署管理工具之前，需要一个 Azure Active Directory (Azure AD) 用户创建应用注册并部署管理 UI。 此用户必须：

- 有权在 Azure 订阅中创建资源
- 有权创建 Azure AD 应用程序 按照[所需的权限](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)中的说明，执行这些步骤检查用户是否具有所需的权限。

为了成功部署和配置管理工具，您首先需要从[RDS 模板 GitHub 存储库](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)下载以下 PowerShell 脚本，并将其保存到本地计算机上的同一文件夹中。

  - 创建WvdMgmtUxApp注册.ps1
  - 更新WvdMgmtUxApiUrl.ps1

部署和配置管理工具后，建议你要求用户启动管理 UI，以确保一切正常。 启动管理 UI 的用户必须具有使他们能够查看或编辑 Windows 虚拟桌面租户的角色分配。

## <a name="set-up-powershell"></a>设置 PowerShell

通过登录到 Az 和 Azure AD PowerShell 模块开始。 以下是登录方式：

1. 以管理员身份打开 PowerShell 并导航到保存 PowerShell 脚本的目录。
2. 使用对计划用于创建管理工具的 Azure 订阅具有所有者或参与者权限的帐户登录到 Azure，通过运行以下 cmdlet：

    ```powershell
    Login-AzAccount
    ```

3. 运行以下 cmdlet 以使用与 Az PowerShell 模块相同的帐户登录到 Azure AD：

    ```powershell
    Connect-AzureAD
    ```

4. 之后，导航到从 RDS 模板 GitHub 存储库中保存两个 PowerShell 脚本的文件夹。

保持用于登录的 PowerShell 窗口处于打开状态，以在登录时运行其他 PowerShell cmdlet。

## <a name="create-an-azure-active-directory-app-registration"></a>创建 Azure 活动目录应用注册

运行以下命令以创建具有所需 API 权限的应用注册：

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$subscriptionId = Read-Host -Prompt "Enter the Azure subscription ID where you will be deploying the management tool."

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

完成 Azure AD 应用注册后，可以部署管理工具。

## <a name="deploy-the-management-tool"></a>部署管理工具

运行以下 PowerShell 命令以部署管理工具并将其与您刚刚创建的服务主体相关联：
     
```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

创建 Web 应用后，必须将 URI 重定向到 Azure AD 应用程序才能成功登录用户。

## <a name="set-the-redirect-uri"></a>设置重定向 URI

运行以下 PowerShell 命令以检索 Web 应用 URL 并将其设置为身份验证重定向 URI（也称为回复 URL）：

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri  
```

现在，您已经添加了重定向 URI，接下来需要更新 API URL，以便管理工具可以与 API 后端服务进行交互。

## <a name="update-the-api-url-for-the-web-application"></a>更新 Web 应用程序的 API URL

运行以下脚本以更新 Web 应用程序前端中的 API URL 配置：

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

现在，您已经完全配置了管理工具 Web 应用，是时候验证 Azure AD 应用程序并提供同意了。

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>验证 Azure AD 应用程序并提供同意

要验证 Azure AD 应用程序配置并提供同意：：

1. 打开 Internet 浏览器并使用管理帐户登录到[Azure 门户](https://portal.azure.com/)。
2. 从 Azure 门户顶部的搜索栏中，搜索**应用注册**并选择**服务**下的项。
3. 选择**所有应用程序**，并在[创建 Azure 活动目录应用注册](#create-an-azure-active-directory-app-registration)中搜索为 PowerShell 脚本提供的唯一应用名称。
4. 在浏览器左侧的面板中，选择 **"身份验证**"并确保重定向 URI 与管理工具的 Web 应用 URL 相同，如下图所示。
   
   [![具有输入重定向 URI](media/management-ui-redirect-uri-inline.png)的身份验证页](media/management-ui-redirect-uri-expanded.png#lightbox)

5. 在左侧面板中，选择**API 权限**以确认已添加权限。 如果您是全局管理员，请选择 **"授予管理员同意"`tenantname`** 按钮，然后按照对话框提示为您的组织提供管理员同意。
    
    [![API 权限页](media/management-ui-permissions-inline.png)](media/management-ui-permissions-expanded.png#lightbox)

您现在可以开始使用管理工具。

## <a name="use-the-management-tool"></a>使用管理工具

现在，您已随时设置管理工具，您可以随时随地启动它。 以下是启动该工具的方式：

1. 在 Web 浏览器中打开 Web 应用的 URL。 如果您不记得 URL，则可以登录到 Azure，查找为管理工具部署的应用服务，然后选择 URL。
2. 使用 Windows 虚拟桌面凭据登录。
   
   > [!NOTE]
   > 如果您在配置管理工具时未授予管理员同意，则登录的每个用户都需要提供自己的用户同意才能使用该工具。

3. 当系统提示您选择租户组时，请从下拉列表中选择**默认租户组**。
4. 选择“默认租户组”时，将在窗口左侧显示一个菜单****。 在此菜单中，找到租户组的名称并将其选中。
   
   > [!NOTE]
   > 如果你有自定义的租户组，请手动输入名称，而不要从下拉列表中选择。

## <a name="report-issues"></a>报告问题

如果遇到有关管理工具或其他 Windows 虚拟桌面工具的任何问题，请按照[远程桌面服务的 Azure 资源管理器模板](https://github.com/Azure/RDS-Templates/blob/master/README.md)中的说明在 GitHub 上报告这些问题。

## <a name="next-steps"></a>后续步骤

了解如何部署和连接管理工具后，接下来可以了解如何使用 Azure 服务运行状况来监视服务问题和获得运行状况方面的建议。 若要了解详细信息，请参阅[“设置服务警报”教程](./set-up-service-alerts.md)。
