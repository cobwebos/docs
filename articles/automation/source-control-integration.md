---
title: Azure 自动化中的源代码管理集成
description: 本文介绍 Azure 自动化中源代码管理与 GitHub 的集成。
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132933"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure 自动化中的源代码管理集成

 Azure 自动化中的源代码管理集成支持源控制存储库中的单方向同步。 源代码管理允许您使用 GitHub 或 Azure 存储库源代码管理存储库中的脚本使自动化帐户中的 Runbook 保持最新。 此功能使开发环境中测试过的代码轻松推广到生产自动化帐户。
 
 源代码管理集成可让您轻松地与团队协作、跟踪更改并回滚到 Runbook 的早期版本。 例如，源代码管理允许您将源代码管理中的不同分支与开发、测试和生产自动化帐户同步。 

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="source-control-types"></a>源代码管理类型

Azure 自动化支持三种类型的源代码管理：

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>先决条件

* 源代码管理存储库（GitHub 或 Azure Repos）
* [以帐户身份运行](manage-runas-account.md)
* 自动化帐户中[的最新 Azure 模块](automation-update-azure-modules.md)，包括`Az.Accounts`模块（相当于`AzureRM.Profile`的 Az 模块）

> [!NOTE]
> 源代码管理同步作业在用户的自动化帐户下运行，并且以与其他自动化作业相同的费率计费。

## <a name="configuring-source-control"></a>配置源代码管理

本节介绍如何为自动化帐户配置源代码管理。 可以使用 Azure 门户或 PowerShell。

### <a name="configure-source-control-in-azure-portal"></a>在 Azure 门户中配置源代码管理

使用此过程使用 Azure 门户配置源代码管理。

1. 在自动化帐户中，选择 **"源控制"，** 然后单击"**添加**"。

    ![选择“源代码管理”](./media/source-control-integration/select-source-control.png)

2. 选择**源代码管理类型**，然后单击 **"身份验证**"。 

3. 将打开浏览器窗口并提示您登录。 按照提示完成身份验证。

4. 在"源控制摘要"页上，使用字段填写下面定义的源代码管理属性。 完成后单击“保存”****。 

    |properties  |描述  |
    |---------|---------|
    |源代码管理名称     | 源代码管理的易记名称。 此名称只能包含字母和数字。        |
    |源代码管理类型     | 源代码管理机制的类型。 可用选项包括：</br> • GitHub</br>• Azure 存储库（Git）</br> • Azure 存储库 （TFVC）        |
    |存储库     | 存储库或项目的名称。 检索前 200 个存储库。 若要搜索存储库，请在字段中键入名称，然后单击“在 GitHub 中搜索”。****|
    |分支     | 分支，从中拉出源文件。 分支目标确定不适用于 TFVC 源代码管理类型。          |
    |文件夹路径     | 包含要同步的 Runbook 的文件夹，例如 **/Runbook**。 仅同步指定文件夹中的 Runbook。 不支持递归。        |
    |自动同步<sup>1</sup>     | 设置在源代码管理存储库中进行提交时打开或关闭自动同步。        |
    |发布 Runbook     | 如果从源代码管理自动发布运行簿后自动发布，则设置 On，否则将关闭。           |
    |描述     | 指定有关源代码管理的其他详细信息的文本。        |

    <sup>1</sup> 只有项目管理员才能在配置源代码管理与 Azure 存储库的集成时启用自动同步。

   ![源代码管理摘要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> 源代码管理存储库的登录名可能与 Azure 门户的登录不同。 在配置源代码管理时，请确保使用源代码管理存储库的正确帐户登录。 如果有疑问，请打开浏览器中的新选项卡，从**dev.azure.com、visualstudio.com**或**github.com**注销，**visualstudio.com**然后尝试重新连接到源代码管理。

### <a name="configure-source-control-in-powershell"></a>在 PowerShell 中配置源代码管理

也可以使用 PowerShell 在 Azure 自动化中配置源代码管理。 要将 PowerShell cmdlet 用于此操作，您需要个人访问令牌 （PAT）。 使用[新阿兹自动化源控制](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
)cmdlet 创建源代码管理连接。 此 cmdlet 需要 PAT 的安全字符串。 要了解如何创建安全字符串，请参阅[转换到安全字符串](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)。

以下小节说明 PowerShell 为 GitHub、Azure 存储库 （Git） 和 Azure 存储库 （TFVC） 创建了源代码管理连接。 

#### <a name="create-source-control-connection-for-github"></a>为 GitHub 创建源代码管理连接

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>为 Azure 存储库 （Git） 创建源代码管理连接

> [!NOTE]
> Azure Repos （Git） 使用访问**dev.azure.com**的 URL，而不是以早期格式使用**visualstudio.com**。 较旧的 URL`https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>`格式已弃用，但仍受支持。 首选新格式。


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>为 Azure 存储库 （TFVC） 创建源代码管理连接

> [!NOTE]
> Azure 存储库 （TFVC） 使用访问**dev.azure.com**的 URL，而不是以较早格式使用**visualstudio.com**。 较旧的 URL`https://<accountname>.visualstudio.com/<projectname>/_versionControl`格式已弃用，但仍受支持。 首选新格式。

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>个人访问令牌 （PAT） 权限

源代码管理需要一些对 PAC 的最小权限。 以下小节包含 GitHub 和 Azure 存储库所需的最小权限。

##### <a name="minimum-pat-permissions-for-github"></a>GitHub 的最低 PAT 权限

下表定义了 GitHub 所需的最小 PAT 权限。 有关在 GitHub 中创建 PAT 的详细信息，请参阅[为命令行创建个人访问令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)。

|范围  |描述  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | 访问提交状态         |
|`repo_deployment`      | 访问部署状态         |
|`public_repo`     | 访问公共存储库         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | 写入存储库挂钩         |
|`read:repo_hook`|读取存储库挂钩|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure 存储库的最低 PAT 权限

下面的列表定义 Azure 存储库所需的最小 PAT 权限。 有关在 Azure 存储库中创建 PAT 的详细信息，请参阅[使用个人访问令牌进行身份验证访问权限](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)。

| 范围  |  访问类型  |
|---------| ----------|
| `Code`      | 读取  |
| `Project and team` | 读取 |
| `Identity` | 读取     |
| `User profile` | 读取     |
| `Work items` | 读取    |
| `Service connections` | 读取、查询、管理<sup>1</sup>    |

<sup>1</sup> `Service connections`仅当您启用了自动同步时，才需要该权限。

## <a name="synchronizing"></a>正在同步

按照以下步骤与源代码管理同步。 

1. 从“源代码管理”页上的表格中选择源。 

2. 单击“开始同步”**** 以开始同步过程。 

3. 单击"**同步作业"** 选项卡查看当前同步作业或以前的同步作业的状态。 

4. 在 **"源代码管理**"下拉菜单上，选择源代码管理机制。

    ![同步状态](./media/source-control-integration/sync-status.png)

5. 单击某个作业可以查看作业输出。 以下示例是源代码管理同步作业的输出。

    ```output
    ===================================================================

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

    ==================================================================

    ```

6. 在“源代码管理同步作业摘要”页上选择“所有日志”可以查看其他日志。**** 这些附加日志条目可以帮助您解决使用源代码管理时可能出现的问题。

## <a name="disconnecting-source-control"></a>断开连接源代码管理

要断开与源代码管理存储库的连接：：

1. 自动化**帐户中的帐户设置**下的开源**控制**。

2. 选择要删除的源代码管理机制。 

3. 在“源代码管理摘要”页面上，单击“删除”。****

## <a name="handling-encoding-issues"></a>处理编码问题

如果多人使用不同的编辑器在源代码管理存储库中编辑 Runbook，则可能发生编码问题。 要了解有关此情况的更多内容，请参阅[编码问题的常见原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)。

## <a name="updating-the-pat"></a>更新 PAT

目前，不能使用 Azure 门户在源代码管理中更新 PAT。 当 PAT 过期或吊销时，可以使用以下方式之一使用新的访问令牌更新源代码：

* 使用[REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update)。
* 使用[更新-阿兹自动化源控制](/powershell/module/az.automation/update-azautomationsourcecontrol)cmdlet。

## <a name="next-steps"></a>后续步骤

要了解有关 Runbook 类型及其优点和限制的更多信息，请参阅[Azure 自动化 Runbook 类型](automation-runbook-types.md)。
