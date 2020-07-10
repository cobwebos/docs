---
title: 使用 Azure 自动化管理 Office 365 服务
description: 本文介绍如何使用 Azure 自动化管理 Office 365 订阅服务。
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 91f5ac0c3adabf9880078d7a4d3703e2757cb97f
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185307"
---
# <a name="manage-office-365-services"></a>管理 Office 365 服务

可以使用 Azure 自动化来管理 Microsoft Word 和 Microsoft Outlook 等产品的 Office 365 订阅服务。 与 Office 365 的交互由 [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 启用。 请参阅[在 Azure 自动化中使用 Azure AD 向 Azure 进行身份验证](automation-use-azure-ad.md)。

## <a name="prerequisites"></a>先决条件

需要满足以下先决条件才能在 Azure 自动化中管理 Office 365 订阅服务。

* 具备 Azure 订阅。 请参阅[订阅决策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)。
* 在 Azure 中有一个用于保存用户帐户凭据和 Runbook 的自动化对象。 请参阅 [Azure 自动化简介](./automation-intro.md)。
* 具备 Azure AD。 请参阅[在 Azure 自动化中使用 Azure AD 向 Azure 进行身份验证](automation-use-azure-ad.md)。
* 具备一个 Office 365 租户，其中包含一个帐户。 请参阅[设置 Office 365 租户](/sharepoint/dev/spfx/set-up-your-developer-tenant)。

## <a name="install-the-msonline-and-msonlineext-modules"></a>安装 MSOnline 和 MSOnlineExt 模块

在 Azure 自动化中使用 Office 365 需要安装 Microsoft Azure Active Directory for Windows PowerShell（`MSOnline` 模块）。 还需要模块 [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)，该模块简化了单租户和多租户环境中的 Azure AD 管理。 按照[在 Azure 自动化中使用 Azure AD 向 Azure 进行身份验证](automation-use-azure-ad.md)中所述安装模块。

>[!NOTE]
>必须成为 Azure AD 的成员才能使用 MSOnline PowerShell。 来宾用户无法使用该模块。

## <a name="create-an-azure-automation-account"></a>创建 Azure 自动化帐户

若要完成本文中的步骤，需要在 Azure 自动化中拥有一个帐户。 请参阅[创建 Azure 自动化帐户](automation-quickstart-create-account.md)。
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>将 MSOnline 和 MSOnlineExt 添加为资产

现在，请添加已安装的 MSOnline 和 MSOnlineExt 模块，以启用 Office 365 功能。 请参阅[管理 Azure 自动化中的模块](shared-resources/modules.md)。

1. 在 Azure 门户中选择“自动化帐户”。
2. 选择你的自动化帐户。
3. 选择“共享资源”下的“模块库”。 
4. 搜索 MSOnline。
5. 选择 `MSOnline` PowerShell 模块，然后单击“导入”以将该模块作为资产导入。
6. 重复步骤 4 和 5，找到并导入 `MSOnlineExt` 模块。 

## <a name="create-a-credential-asset-optional"></a>创建凭据资产（可选）

对于有权运行你的脚本的 Office 365 管理用户而言，创建凭据资产的操作是可选的。 但该操作有助于防止在 PowerShell 脚本中公开用户名和密码。 有关说明，请参阅[创建凭据资产](automation-use-azure-ad.md#create-a-credential-asset)。

## <a name="create-an-office-365-service-account"></a>创建 Office 365 服务帐户

若要运行 Office 365 订阅服务，需有一个有权执行所需操作的 Office 365 服务帐户。 可以使用一个全局管理员帐户，对每个服务使用一个帐户，或者具有一个可执行的函数或脚本。 在任一情况下，服务帐户都需要复杂且安全的密码。 请参阅[设置 Office 365 for Business](/microsoft-365/admin/setup/setup?view=o365-worldwide)。 

## <a name="connect-to-the-azure-ad-online-service"></a>连接到 Azure AD 在线服务

>[!NOTE]
>若要使用 MSOnline 模块 cmdlet，必须从 Windows PowerShell 运行这些 cmdlet。 PowerShell Core 不支持这些 cmdlet。

可以使用 MSOnline 模块从 Office 365 订阅连接到 Azure AD。 该连接使用 Office 365 用户名和密码，或使用多重身份验证 (MFA)。 可以使用 Azure 门户或 Windows PowerShell 命令提示（无需提升权限）进行连接。

下面显示了 PowerShell 示例。 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) cmdlet 会提示输入凭据，并将其存储在 `Msolcred` 变量中。 然后，[Connect-MsolService](/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) cmdlet 使用这些凭据连接到 Azure 目录在线服务。 若要连接到特定的 Azure 环境，请使用 `AzureEnvironment` 参数。

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

如果未收到任何错误，则表示连接成功。 若要进行快速测试，可以运行某个 Office 365 cmdlet（例如 `Get-MsolUser`），然后查看结果。 如果收到错误，请注意，常见的原因是密码不正确。

>[!NOTE]
>也可以使用 AzureRM 模块或 Az 模块从 Office 365 订阅连接到 Azure AD。 主连接 cmdlet 是 [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0)。 此 cmdlet 支持对特定的 Office 365 环境使用 `AzureEnvironmentName` 参数。

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>从现有脚本创建 PowerShell Runbook

可以从 PowerShell 脚本访问 Office 365 功能。 下面是使用名为 `Office-Credentials` 的凭据和用户名 `admin@TenantOne.com` 运行的脚本示例。 此脚本使用 `Get-AutomationPSCredential` 导入 Office 365 凭据。

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

## <a name="run-the-script-in-a-runbook"></a>在 Runbook 中运行脚本

可以在 Azure 自动化 Runbook 中使用脚本。 在示例中，我们将使用 PowerShell Runbook 类型。

1. 创建新的 PowerShell Runbook。 请参阅[创建 Azure 自动化 Runbook](./automation-quickstart-create-runbook.md)。
2. 在你的自动化帐户中的“流程自动化”下选择“Runbook”。 
3. 选择新的 Runbook，然后单击“编辑”。
4. 复制脚本，并将其粘贴到 Runbook 的文本编辑器中。
5. 选择“资产”，然后展开“凭据”并检查 Office 365 凭据是否在此处。 
6. 单击“ **保存**”。
7. 选择“测试”窗格，然后单击“开始”以开始测试 Runbook。  请参阅[在 Azure 自动化中管理 Runbook](./manage-runbooks.md)。
8. 测试完成后，退出“测试”窗格。

## <a name="publish-and-schedule-the-runbook"></a>发布和计划 Runbook

若要发布和计划 Runbook，请参阅[在 Azure 自动化中管理 Runbook](./manage-runbooks.md)。

## <a name="next-steps"></a>后续步骤

* 有关凭据用法的详细信息，请参阅[在 Azure 自动化中管理凭据](shared-resources/credentials.md)。
* 有关模块的信息，请参阅[在 Azure 自动化中管理模块](shared-resources/modules.md)。
* 如果需要启动 Runbook，请参阅[在 Azure 自动化中启动 Runbook](start-runbooks.md)。
* 有关 PowerShell 的详细信息，请参阅 [PowerShell 文档](/powershell/scripting/overview)。
