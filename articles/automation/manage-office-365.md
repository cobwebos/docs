---
title: 使用 Azure 自动化管理 Office 365 服务
description: 介绍如何使用 Azure 自动化来管理 Office 365 订阅服务。
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550417"
---
# <a name="manage-office-365-services-using-azure-automation"></a>使用 Azure 自动化管理 Office 365 服务

对于 Microsoft Word 和 Microsoft Outlook 等产品，你可以使用 Azure 自动化来管理 Office 365 订阅服务。 与 Office 365 的交互由[Azure Active Directory （Azure AD）](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)启用。 请参阅[使用 Azure 自动化中的 Azure AD 对 azure 进行身份验证](automation-use-azure-ad.md)。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="prerequisites"></a>先决条件

在 Azure 自动化中管理 Office 365 订阅服务需要以下各项。

* Azure 订阅。 请参阅[订阅决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)。
* Azure 中用于保存用户帐户凭据和 runbook 的自动化对象。 请参阅[Azure Automation 简介](https://docs.microsoft.com/azure/automation/automation-intro)。
* 单一登录。 请参阅[使用 Azure 自动化中的 Azure AD 对 azure 进行身份验证](automation-use-azure-ad.md)。
* Office 365 租户，包含帐户。 请参阅[设置 Office 365 租户](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)。

## <a name="installing-the-msonline-and-msonlineext-modules"></a>安装 MSOnline 和 MSOnlineExt 模块

在 Azure 自动化中使用 Office 365 需要为 Windows PowerShell （`MSOnline`模块） Microsoft Azure Active Directory。 还需要模块，该模块[`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)简化了单租户环境和多租户环境中的 Azure AD 管理。 按照[使用 Azure 自动化中的 Azure AD 中](automation-use-azure-ad.md)所述安装模块，以便向 Azure 进行身份验证。

>[!NOTE]
>若要使用 MSOnline PowerShell，你必须是 Azure AD 的成员。 来宾用户无法使用模块。

## <a name="creating-an-azure-automation-account"></a>创建 Azure 自动化帐户

若要完成本文中的步骤，需要使用 Azure 自动化中的帐户。 请参阅[创建 Azure 自动化帐户](automation-quickstart-create-account.md)。
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>将 MSOnline 和 MSOnlineExt 添加为资产

现在，请添加已安装的 MSOnline 和 MSOnlineExt 模块，以启用 Office 365 功能。 请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。

1. 在 Azure 门户中，选择 "**自动化帐户**"。
2. 选择自动化帐户。
3. 选择 "**共享资源**" 下的 "**模块库**"。
4. 搜索 MSOnline。
5. 选择`MSOnline` PowerShell 模块，然后单击 "**导入**" 以将模块导入为资产。
6. 重复步骤4和步骤5，查找并导`MSOnlineExt`入模块。 

## <a name="creating-a-credential-asset-optional"></a>创建凭据资产（可选）

对于有权运行脚本的 Office 365 管理用户，可选择创建凭据资产。 但这有助于防止在 PowerShell 脚本中公开用户名和密码。 有关说明，请参阅[创建凭据资产](automation-use-azure-ad.md#creating-a-credential-asset)。

## <a name="creating-an-office-365-service-account"></a>创建 Office 365 服务帐户

若要运行 Office 365 订阅服务，需要有一个有权执行所需操作的 Office 365 服务帐户。 您可以使用一个全局管理员帐户、每个服务一个帐户，或者要执行一个函数或脚本。 在任何情况下，服务帐户都需要一个复杂的安全密码。 请参阅[设置 Office 365 for business](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide)。 

## <a name="connecting-to-the-azure-ad-online-service"></a>连接到 Azure AD online 服务

>[!NOTE]
>若要使用 MSOnline 模块 cmdlet，必须从 Windows PowerShell 运行它们。 PowerShell Core 不支持这些 cmdlet。

可以使用 MSOnline 模块连接到 Office 365 订阅中的 Azure AD。 该连接使用 Office 365 用户名和密码，或使用多重身份验证（MFA）。 你可以使用 Azure 门户或 Windows PowerShell 命令提示符（无需提升）进行连接。

PowerShell 示例如下所示。 [获取](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7)凭据 cmdlet 会提示输入凭据，并将`Msolcred`其存储在变量中。 然后， [connect-msolservice](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) cmdlet 使用凭据连接到 Azure 目录联机服务。 如果要连接到特定的 Azure 环境，请使用`AzureEnvironment`参数。

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

如果未收到任何错误，则已成功连接。 快速测试是运行 Office 365 cmdlet，例如`Get-MsolUser`，并查看结果。 如果收到错误，请注意，常见问题是错误的密码。

>[!NOTE]
>还可以使用 AzureRM 模块或 Az 模块连接到 Office 365 订阅中的 Azure AD。 主连接 cmdlet 为[AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)。 此 cmdlet 支持特定`AzureEnvironmentName` Office 365 环境的参数。

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>从现有脚本创建 PowerShell runbook

可以从 PowerShell 脚本访问 Office 365 功能。 下面是名`Office-Credentials`为的凭据的脚本示例，用户名为`admin@TenantOne.com`。 它使用`Get-AutomationPSCredential`导入 Office 365 凭据。

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

## <a name="running-the-script-in-a-runbook"></a>在 runbook 中运行脚本

可以在 Azure 自动化 runbook 中使用脚本。 例如，我们将使用 PowerShell runbook 类型。

1. 创建新的 PowerShell runbook。 请参阅[创建 Azure 自动化 runbook](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)。
2. 在自动化帐户中，选择 "**流程自动化**" 下的 " **runbook** "。
3. 选择新的 runbook，然后单击 "**编辑**"。
4. 复制你的脚本，并将其粘贴到 runbook 的文本编辑器中。
5. 选择 "**资产**"，然后展开 "**凭据**"，并验证 Office 365 凭据是否存在。
6. 单击 **“保存”** 。
7. 选择**测试窗格**，然后单击 "**开始**" 以开始测试 runbook。 请参阅[在 Azure 自动化中管理 runbook](https://docs.microsoft.com/azure/automation/manage-runbooks)。
8. 测试完成后，退出 "测试" 窗格。

## <a name="publishing-and-scheduling-the-runbook"></a>发布和计划 runbook

若要发布和计划 runbook，请参阅[在 Azure 自动化中管理 runbook](https://docs.microsoft.com/azure/automation/manage-runbooks)。

## <a name="next-steps"></a>后续步骤

* 可以在[Azure 自动化中的凭据资产](shared-resources/credentials.md)中找到有关自动化凭据资产的信息。
* 请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)以了解如何使用自动化模块。
* 有关 runbook 管理的概述，请参阅[在 Azure 自动化中管理 runbook](https://docs.microsoft.com/azure/automation/manage-runbooks)。
* 若要详细了解可用于在 Azure 自动化中启动 runbook 的方法，请参阅[在 Azure 自动化中启动 runbook](automation-starting-a-runbook.md)。
* 有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅[Powershell 文档](https://docs.microsoft.com/powershell/scripting/overview)。