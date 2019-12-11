---
title: Azure 自动化中的源代码管理集成
description: 本文介绍 Azure 自动化中源代码管理与 GitHub 的集成。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/10/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4e5e0135e4ab56d319c78b7daeab9dd2e9d101f9
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996504"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure 自动化中的源代码管理集成

源代码管理使你能够在你的 GitHub 或 Azure Repos 源代码管理存储库中，使你的自动化帐户中的 runbook 保持最新。 使用源代码管理可轻松与团队协作、跟踪更改，以及回退到旧版 Runbook。 例如，通过源代码管理可以将源代码管理中的不同分支同步到开发、测试或生产自动化帐户。 这样可以轻松地将已在开发环境中测试过的代码提升到生产自动化帐户。 源代码管理与自动化的集成支持从源代码管理存储库同步单方向。

Azure 自动化支持三种类型的源代码管理：

* GitHub
* Azure Repos （Git）
* Azure Repos （TFVC）

## <a name="pre-requisites"></a>必备组件

* 源代码管理存储库（GitHub 或 Azure Repos）
* [运行方式帐户](manage-runas-account.md)
* 确保在自动化帐户中具有[最新的 Azure 模块](automation-update-azure-modules.md)，包括**AzureRM**模块。 

> [!NOTE]
> 源代码管理同步作业以自动化帐户用户身份运行，并且按与其他自动化作业相同的费率计费。

## <a name="configure-source-control---azure-portal"></a>配置源代码管理-Azure 门户

在自动化帐户中，选择 "**源代码管理**"，然后单击 " **+ 添加**"

![选择“源代码管理”](./media/source-control-integration/select-source-control.png)

选择“源代码管理类型”，单击“身份验证”。 此时会打开一个浏览器窗口，提示你登录，请按照提示完成身份验证。

在“源代码管理摘要”页上，填写信息并单击“保存”。 下表显示了可用字段的说明。

|properties  |描述  |
|---------|---------|
|源代码管理名称     | 源代码管理的友好名称。 *此名称只能包含字母和数字。*        |
|源代码管理类型     | 源代码管理源的类型。 可用选项包括：</br> GitHub</br>Azure Repos （Git）</br> Azure Repos （TFVC）        |
|存储库     | 存储库或项目的名称。 返回前200个存储库。 要搜索存储库，请在字段中键入名称，然后单击**GitHub 上**的 "搜索"。|
|分支     | 要从源文件中提取的分支。 分支目标不能用于 TFVC 源控件类型。          |
|文件夹路径     | 包含要同步的 runbook 的文件夹。示例：/Runbooks </br>*仅同步指定的文件夹中的 runbook。不支持递归。*        |
|自动同步<sup>1</sup>     | 在源代码管理存储库中提交时打开或关闭自动同步         |
|发布 Runbook     | 如果设置为**On**，则在 runbook 从源代码管理同步后，它们将自动发布。         |
|描述     | 用于提供其他详细信息的一个文本字段        |

<sup>1</sup>若要在配置与 Azure Repos 的源代码管理集成时启用自动同步，您必须是项目管理员。

![源代码管理摘要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 你的源代码管理存储库的登录名可能不同于 Azure 门户的登录名。 配置源代码管理时，请确保您已登录到您的源代码管理存储库的正确帐户。 如果有疑问，请在浏览器中打开新的选项卡并从 visualstudio.com 或 github.com 中注销，然后再次尝试连接源代码管理。

## <a name="configure-source-control---powershell"></a>配置源代码管理-PowerShell

还可以使用 PowerShell 在 Azure 自动化中配置源代码管理。 若要使用 PowerShell cmdlet 配置源代码管理，需要个人访问令牌（PAT）。 使用[AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl)创建源代码管理连接。 Cmdlet 需要个人访问令牌的安全字符串，若要了解如何创建安全字符串，请参阅[convertto-html-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)。

### <a name="azure-repos-git"></a>Azure Repos （Git）

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos （TFVC）

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>个人访问令牌权限

源代码管理需要一些个人访问令牌的最低权限。 下表包含 GitHub 和 Azure Repos 所需的最小权限。

#### <a name="github"></a>GitHub

有关在 GitHub 中创建个人访问令牌的详细信息，请访问[创建用于命令行的个人访问令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。

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

有关在 Azure Repos 中创建个人访问令牌的详细信息，请访问[使用个人访问令牌验证访问权限](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

|范围  |
|---------|
|代码(读取)     |
|项目和团队(读取)|
|标识(读取)      |
|用户配置文件(读取)     |
|工作项(读取)    |
|服务连接(读取、查询和管理)<sup>1</sup>    |

<sup>1</sup>仅当启用了 autosync 时，才需要服务连接权限。

## <a name="syncing"></a>同步

从 "**源代码管理**" 页上的表中选择源。 单击“开始同步”以开始同步过程。

可以通过单击 "**同步作业**" 选项卡来查看当前同步作业或以前的同步作业的状态。在 "**源代码管理**" 下拉控件上，选择一个源代码管理。

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

通过选择 "**源代码管理同步作业摘要**" 页上的 "**所有日志**"，可以获得其他日志记录。 这些附加日志条目有助于排查使用源代码管理时可能出现的问题。

## <a name="disconnecting-source-control"></a>断开连接源代码管理

若要从源代码管理存储库断开连接，请在自动化帐户中的 "**帐户设置**" 下打开**源代码管理**。

选择要删除的源代码管理。 在“源代码管理摘要”页面上，单击“删除”。

## <a name="encoding"></a>编码

如果有多个用户正在使用不同的编辑器在源代码管理存储库中编辑 runbook，则有可能遇到编码问题。 这种情况可能会导致 runbook 中出现不正确的字符。 若要了解有关此问题的详细信息，请参阅[编码问题的常见原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-access-token"></a>更新访问令牌

当前无法从门户更新源代码管理中的访问令牌。 个人访问令牌过期或吊销后，可以通过以下方式使用新的访问令牌来更新源代码管理：

* 通过[REST Api](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update)。
* 使用[AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) cmdlet。

## <a name="next-steps"></a>后续步骤

若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)
