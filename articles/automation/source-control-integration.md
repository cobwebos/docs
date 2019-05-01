---
title: Azure 自动化中的源代码管理集成
description: 本文介绍 Azure 自动化中源代码管理与 GitHub 的集成。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 94912d5aa10ddd2e67c33bcbb416f007c85f105c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64574110"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure 自动化中的源代码管理集成

源代码管理可让你可以让你的 runbook 在自动化帐户是与您的脚本在 GitHub 或 Azure 存储库源控件存储库中最新。 使用源代码管理可轻松与团队协作、跟踪更改，以及回退到旧版 Runbook。 例如，通过源代码管理可以将源代码管理中的不同分支同步到开发、测试或生产自动化帐户。 这样可以轻松地将已在开发环境中测试过的代码提升到生产自动化帐户。 源代码管理与自动化集成支持单向同步从源代码管理存储库。

Azure 自动化支持三种类型的源代码管理：

* GitHub
* Azure 存储库 (Git)
* Azure 存储库 (TFVC)

## <a name="pre-requisites"></a>先决条件

* 源代码管理存储库 （GitHub 或 Azure 存储库）
* 一个[运行方式帐户](manage-runas-account.md)
* 请确保您具有[最新的 Azure 模块](automation-update-azure-modules.md)在自动化帐户中

> [!NOTE]
> 源代码管理同步作业以自动化帐户用户身份运行，并且按与其他自动化作业相同的费率计费。

## <a name="configure-source-control---azure-portal"></a>配置源代码管理-Azure 门户

在自动化帐户中，选择**源代码管理**单击 **+ 添加**

![选择“源代码管理”](./media/source-control-integration/select-source-control.png)

选择“源代码管理类型”，单击“身份验证”。 打开一个浏览器窗口，提示你登录，根据提示完成身份验证。

在“源代码管理摘要”页上，填写信息并单击“保存”。 下表显示了可用字段的说明。

|属性  |描述  |
|---------|---------|
|源代码管理名称     | 源控件的友好名称。 *此名称必须包含字母和数字。*        |
|源代码管理类型     | 源代码管理源的类型。 可用选项包括：</br> GitHub</br>Azure 存储库 (Git)</br> Azure 存储库 (TFVC)        |
|存储库     | 存储库或项目的名称。 返回前 200 个存储库。 若要搜索存储库，在字段中键入名称，然后单击**GitHub 上的搜索**。|
|分支     | 要从源文件中提取的分支。 分支目标不是适用于 TFVC 源控件类型。          |
|文件夹路径     | 包含要同步的 runbook 的文件夹。示例：/Runbooks </br>*指定的文件夹中的唯一 runbook 会同步。不支持递归。*        |
|自动同步<sup>1</sup>     | 在源代码管理存储库中提交时打开或关闭自动同步         |
|发布 Runbook     | 如果设置为**上**后从源代码管理，它们将自动发布同步 runbook。         |
|描述     | 用于提供其他详细信息的一个文本字段        |

<sup>1</sup>若要启用自动同步，使用 Azure 存储库配置源代码管理集成时，您必须是项目管理员。

![源代码管理摘要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 你的源代码管理存储库的登录名可能不同于你的 Azure 门户的登录名。 请确保你使用登录的正确帐户为源控件存储库配置源代码管理时。 如果有疑问，请在浏览器中打开新的选项卡并从 visualstudio.com 或 github.com 中注销，然后再次尝试连接源代码管理。

## <a name="configure-source-control---powershell"></a>配置源代码管理-PowerShell

此外可以使用 PowerShell 在 Azure 自动化中配置源代码管理。 若要使用 PowerShell cmdlet 配置源代码管理，需要个人访问令牌 (PAT)。 您使用[新建 AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl)创建源代码管理连接。 该 cmdlet 需要安全字符串的个人访问令牌，若要了解如何创建安全字符串，请参阅[Convertto-securestring](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)。

### <a name="azure-repos-git"></a>Azure 存储库 (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure 存储库 (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>个人访问令牌权限

源代码管理需要一些个人访问令牌的最低权限。 下表包含 GitHub 和 Azure 存储库所需的最小权限。

#### <a name="github"></a>GitHub

有关在 GitHub 中创建个人访问令牌的详细信息，请访问[创建命令行的个人访问令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。

|范围  |描述  |
|---------|---------|
|**存储库**     |         |
|repo:status     | 访问提交状态         |
|repo_deployment      | 访问部署状态         |
|public_repo     | 访问公共存储库         |
|**admin:repo_hook**     |         |
|write:repo_hook     | 写入存储库挂钩         |
|read:repo_hook|读取存储库挂钩|

#### <a name="azure-repos"></a>Azure Repos

有关在 Azure 存储库中创建个人访问令牌的详细信息，请访问[进行身份验证使用个人访问令牌访问](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

|范围  |
|---------|
|代码(读取)     |
|项目和团队(读取)|
|标识(读取)      |
|用户配置文件(读取)     |
|工作项(读取)    |
|服务连接(读取、查询和管理)<sup>1</sup>    |

<sup>1</sup>服务连接权限，才需要是否已启用自动同步。

## <a name="syncing"></a>同步

在选择表中的源**源代码管理**页。 单击“开始同步”以开始同步过程。

可以通过单击“同步作业”选项卡来查看当前同步作业或之前的同步作业的状态。在“源代码管理”下拉列表中，选择一个源代码管理。

![同步状态](./media/source-control-integration/sync-status.png)

单击某个作业可以查看作业输出。 以下示例是源代码管理同步作业的输出。

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

附加的日志记录是可通过选择**的所有日志**上**源控件同步作业摘要**页。 这些额外的日志条目可以帮助您解决使用源代码管理时可能出现的问题。

## <a name="disconnecting-source-control"></a>断开连接源代码管理

若要断开与源代码管理存储库的连接，打开**源代码管理**下**帐户设置**在自动化帐户中。

选择要删除的源代码管理。 在“源代码管理摘要”页面上，单击“删除”。

## <a name="encoding"></a>编码

如果多人正在使用不同的编辑器编辑源代码控制存储库中的 runbook，则可能会遇到编码问题。 这种情况下可能会导致不正确的字符在 runbook 中。 若要了解详细信息，请参阅[常见编码问题的原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>后续步骤

若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)
