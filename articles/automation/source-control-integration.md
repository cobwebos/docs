---
title: Azure 自动化中的源代码管理集成
description: 本文介绍 Azure 自动化中源代码管理与 GitHub 的集成。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/26/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9bbf3582da2664b6e6429677d47aad4d69a7c1bb
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785318"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure 自动化中的源代码管理集成

通过源代码管理，你可以使用 GitHub 或 Azure Dev Ops 源代码管理存储库中的脚本使自动化帐户中的 runbook 保持最新。 使用源代码管理可轻松与团队协作、跟踪更改，以及回退到旧版 Runbook。 例如，通过源代码管理可以将源代码管理中的不同分支同步到开发、测试或生产自动化帐户。 这样可以轻松地将已在开发环境中测试过的代码提升到生产自动化帐户。

Azure 自动化支持 3 种类型的源代码管理：

* GitHub
* Visual Studio Team Services (Git)
* Visual Studio Team Services (TFVC)

## <a name="pre-requisites"></a>先决条件

* 源代码管理存储库（GitHub 或 Visual Studio Team Services）
* 对源代码管理存储库的相应[权限](#personal-access-token-permissions)
* 一个[运行方式帐户和连接](manage-runas-account.md)

> [!NOTE]
> 源代码管理同步作业以自动化帐户用户身份运行，并且按与其他自动化作业相同的费率计费。

## <a name="configure-source-control"></a>配置源代码管理

在你的自动化帐户中，选择“源代码管理(预览版)”，然后单击“+ 添加”

![选择“源代码管理”](./media/source-control-integration/select-source-control.png)

选择“源代码管理类型”，单击“身份验证”。

查看应用请求权限页，然后单击“接受”。

在“源代码管理摘要”页上，填写信息并单击“保存”。 下表显示了可用字段的说明。

|属性  |Description  |
|---------|---------|
|源代码管理名称     | 源代码管理的友好名称        |
|源代码管理类型     | 源代码管理源的类型。 可用选项包括：</br> Github</br>Visual Studio Team Services (Git)</br> Visual Studio Team Services (TFVC)        |
|存储库     | 存储库或项目的名称。 此值从源代码管理存储库中拉取。 示例：$/ContosoFinanceTFVCExample         |
|分支     | 要从源文件中提取的分支。 分支目标确定不适用于 TFVC 源代码管理类型。          |
|文件夹路径     | 包含要同步的 runbook 的文件夹。示例：/Runbooks         |
|自动同步     | 在源代码管理存储库中提交时打开或关闭自动同步         |
|发布 Runbook     | 如果设置为“打开”，在从源代码管理同步 runbook 后，它们将自动发布。         |
|Description     | 用于提供其他详细信息的一个文本字段        |

![源代码管理摘要](./media/source-control-integration/source-control-summary.png)

## <a name="syncing"></a>同步

如果在配置源代码管理集成时配置了自动同步，则将自动启动初始同步。 如果未设置自动同步，请从“源代码管理(预览版)”页面上的表中选择源。 单击“开始同步”以开始同步过程。  

可以通过单击“同步作业”选项卡来查看当前同步作业或之前的同步作业的状态。在“源代码管理”下拉列表中，选择一个源代码管理。

![同步状态](./media/source-control-integration/sync-status.png)

单击某个作业可以查看作业输出。 以下示例是源代码管理同步作业的输出。

```output
========================================================================================================

Azure Automation Source Control Public Preview.
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

## <a name="personal-access-token-permissions"></a>个人访问令牌权限

源代码管理需要一些个人访问令牌的最低权限。 下表包含 GitHub 和 Azure DevOps 所需的最低权限。

### <a name="github"></a>GitHub

|范围  |Description  |
|---------|---------|
|**存储库**     |         |
|repo:status     | 访问提交状态         |
|repo_deployment      | 访问部署状态         |
|public_repo     | 访问公共存储库         |
|**admin:repo_hook**     |         |
|write:repo_hook     | 写入存储库挂钩         |
|read:repo_hook|读取存储库挂钩|

### <a name="azure-devops"></a>Azure DevOps

|范围  |
|---------|
|代码(读取)     |
|项目和团队(读取)|
|标识(读取)      |
|用户配置文件(读取)     |
|工作项(读取)    |
|服务连接(读取、查询和管理)<sup>1</sup>    |

<sup>1</sup>只有在启用了自动同步时才需要“服务连接”权限。

## <a name="disconnecting-source-control"></a>断开连接源代码管理

若要从源代码管理存储库断开连接，请在你的自动化帐户中的“帐户设置”下打开“源代码管理(预览版)”。

选择要删除的源代码管理。 在“源代码管理摘要”页面上，单击“删除”。

## <a name="next-steps"></a>后续步骤

若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)
