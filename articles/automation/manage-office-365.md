---
title: 使用 Azure 自动化管理 Office 365 服务
description: 介绍如何使用 Azure 自动化来管理 Office 365 订阅服务。
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550417"
---
# <a name="manage-office-365-services-using-azure-automation"></a>使用 Azure 自动化管理 Office 365 服务

对于 Microsoft Word 和 Microsoft Outlook 等产品，可以使用 Azure 自动化管理 Office 365 订阅服务。 与 Office 365 的交互由[Azure 活动目录 （Azure AD） 启用](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)。 请参阅[在 Azure 自动化中使用 Azure AD 以对 Azure 进行身份验证](automation-use-azure-ad.md)。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="prerequisites"></a>先决条件

在 Azure 自动化中管理 Office 365 订阅服务需要以下操作。

* Azure 订阅。 请参阅[订阅决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)。
* Azure 中用于保存用户帐户凭据和 Runbook 的自动化对象。 请参阅[Azure 自动化 简介](https://docs.microsoft.com/azure/automation/automation-intro)。
* 单一登录。 请参阅[在 Azure 自动化中使用 Azure AD 以对 Azure 进行身份验证](automation-use-azure-ad.md)。
* 具有帐户的 Office 365 租户。 请参阅[设置 Office 365 租户](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)。

## <a name="installing-the-msonline-and-msonlineext-modules"></a>安装 MSOnline 和 MSOnlineExt 模块

在 Azure 自动化中使用 Office 365 需要为 Windows PowerShell（`MSOnline`模块）使用 Microsoft Azure 活动目录。 您还需要该模块，该模块[`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)简化了单租户和多租户环境中的 Azure AD 管理。 安装 Azure[自动化中使用 Azure AD 中描述的模块，以便对 Azure 进行身份验证](automation-use-azure-ad.md)。

>[!NOTE]
>要使用 MSOnline PowerShell，您必须是 Azure AD 的成员。 来宾用户无法使用该模块。

## <a name="creating-an-azure-automation-account"></a>创建 Azure 自动化帐户

要完成本文中的步骤，需要在 Azure 自动化中建立一个帐户。 请参阅[创建 Azure 自动化帐户](automation-quickstart-create-account.md)。
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>将 MSOnline 和 MSOnlineExt 添加为资产

现在添加已安装的 MSOnline 和 MSOnlineExt 模块，以启用 Office 365 功能。 请参阅[Azure 自动化 中的管理模块](shared-resources/modules.md)。

1. 在 Azure 门户中，选择 **"自动化帐户**"。
2. 选择您的自动化帐户。
3. 在**共享资源**下选择**模块库**。
4. 搜索 MSOnline。
5. 选择`MSOnline`PowerShell 模块，然后单击 **"导入**"以将模块导入为资产。
6. 重复步骤 4 和 5 以`MSOnlineExt`定位和导入模块。 

## <a name="creating-a-credential-asset-optional"></a>创建凭据资产（可选）

为具有运行脚本权限的 Office 365 管理用户创建凭据资产是可选的。 但是，它可以帮助防止在 PowerShell 脚本中公开用户名和密码。 有关说明，请参阅[创建凭据资产](automation-use-azure-ad.md#creating-a-credential-asset)。

## <a name="creating-an-office-365-service-account"></a>创建 Office 365 服务帐户

要运行 Office 365 订阅服务，您需要一个具有权限的 Office 365 服务帐户来执行所需的操作。 您可以使用一个全局管理员帐户，每个服务一个帐户，或者要执行一个函数或脚本。 在任何情况下，服务帐户都需要复杂和安全的密码。 有关[业务，请参阅设置 Office 365。](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide) 

## <a name="connecting-to-the-azure-ad-online-service"></a>连接到 Azure AD 联机服务

>[!NOTE]
>要使用 MSOnline 模块 cmdlet，必须从 Windows PowerShell 运行它们。 PowerShell 核心不支持这些 cmdlet。

您可以使用 MSOnline 模块从 Office 365 订阅连接到 Azure AD。 连接使用 Office 365 用户名和密码或使用多重身份验证 （MFA）。 您可以使用 Azure 门户或 Windows PowerShell 命令提示符进行连接（不必提升）。

PowerShell 示例如下所示。 [获取凭据](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7)cmdlet 提示凭据并将其存储在变量中`Msolcred`。 然后，[连接-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) cmdlet 使用凭据连接到 Azure 目录联机服务。 如果要连接到特定的 Azure 环境，请使用 参数`AzureEnvironment`。

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

如果未收到任何错误，则已成功连接。 例如，快速测试是运行 Office 365 cmdlet，`Get-MsolUser`并查看结果。 如果收到错误，请注意，常见问题是密码不正确。

>[!NOTE]
>您还可以使用 AzureRM 模块或 Az 模块从 Office 365 订阅连接到 Azure AD。 主连接 cmdlet 是[连接 AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)。 此 cmdlet`AzureEnvironmentName`支持特定 Office 365 环境的参数。

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>从现有脚本创建 PowerShell 运行簿

您从 PowerShell 脚本访问 Office 365 功能。 下面是名为 的`Office-Credentials`凭据的`admin@TenantOne.com`脚本示例， 它用于`Get-AutomationPSCredential`导入 Office 365 凭据。

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>在 Runbook 中运行脚本

您可以在 Azure 自动化手册中使用脚本。 例如，我们将使用 PowerShell 运行簿类型。

1. 创建新的 PowerShell 运行簿。 请参阅[创建 Azure 自动化运行簿](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)。
2. 从您的自动化帐户中，在 **"流程自动化**"下选择**Runbook。**
3. 选择新的 Runbook，然后单击 **"编辑**"。
4. 复制脚本并将其粘贴到 Runbook 的文本编辑器中。
5. 选择**ASSETS，** 然后展开**凭据**并验证 Office 365 凭据是否在那里。
6. 单击“保存”  。
7. 选择 **"测试"窗格**，然后单击"**开始"** 开始测试 Runbook。 请参阅[在 Azure 自动化中管理 Runbook。](https://docs.microsoft.com/azure/automation/manage-runbooks)
8. 测试完成后，退出"测试"窗格。

## <a name="publishing-and-scheduling-the-runbook"></a>发布和调度 Runbook

要发布并安排 Runbook，请参阅[在 Azure 自动化 中管理 Runbook。](https://docs.microsoft.com/azure/automation/manage-runbooks)

## <a name="next-steps"></a>后续步骤

* 您可以在[Azure 自动化 中的凭据资产中找到](shared-resources/credentials.md)有关自动化凭据资产的信息。
* 请参阅[管理 Azure 自动化中的模块](shared-resources/modules.md)，了解如何使用自动化模块。
* 有关 Runbook 管理的概述，请参阅[在 Azure 自动化 中管理 Runbook。](https://docs.microsoft.com/azure/automation/manage-runbooks)
* 要了解有关可用于在 Azure 自动化中启动 Runbook 的方法，请参阅[在 Azure 自动化 中启动 Runbook。](automation-starting-a-runbook.md)
* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅[PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。