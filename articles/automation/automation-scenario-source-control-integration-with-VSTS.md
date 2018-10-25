---
title: 将 Azure 自动化与 Azure DevOps Services 源代码管理进行集成
description: 本方案分步讲解了如何设置 Azure 自动化帐户与 Azure DevOps Services 源代码管理的集成。
services: automation
author: eamonoreilly
ms.author: eamono
keywords: azure powershell, Azure DevOps Services, 源代码管理, 自动化
ms.service: automation
ms.component: process-automation
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: b06e315cc12856976dce87791b423d10f002c6df
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801431"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-azure-devops"></a>Azure 自动化方案 - 与 Azure DevOps 集成的自动化源代码管理

> [!NOTE]
> 有一种新的源代码管理体验。 若要了解有关新体验的更多信息，请参阅[源代码管理（预览）](source-control-integration.md)。

在本方案中，你将使用一个 Azure DevOps 项目来管理源代码管理中的 Azure 自动化 Runbook 或 DSC 配置。

本文介绍乐如何将 Azure DevOps 与 Azure 自动化环境集成，以便每次签入时都能发生持续集成。

## <a name="getting-the-scenario"></a>获取方案

此方案包含的两个 PowerShell Runbook 可以在 Azure 门户中直接从 [Runbook 库](automation-runbook-gallery.md)导入，也可以从 [PowerShell 库](https://www.powershellgallery.com)下载。

### <a name="runbooks"></a>Runbook

Runbook | Description|
--------|------------|
Sync-VSTS | 执行签入时从 Azure DevOps 源代码管理导入 Runbook 或配置。 如果手动运行，它会将所有 Runbook 或配置导入并发布到自动化帐户。| 
Sync-VSTSGit | 执行签入时在 Git 源代码管理下从 Azure DevOps 导入 Runbook 或配置。 如果手动运行，它会将所有 Runbook 或配置导入并发布到自动化帐户。|

### <a name="variables"></a>变量

变量 | Description|
-----------|------------|
VSToken | 所创建的安全变量资产，包含 Azure DevOps 个人访问令牌。 可在 [Azure DevOps 身份验证页](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)上了解如何创建 Azure DevOps 个人访问令牌。

## <a name="installing-and-configuring-this-scenario"></a>安装和配置此方案

在 Azure DevOps 中创建[个人访问令牌](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)，它用来将 Runbook 或配置同步到自动化帐户。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

在自动化帐户中创建一个[安全变量](automation-variables.md)用于保存个人访问令牌，使 Runbook 能够向 Azure DevOps 进行身份验证，将 Runbook 或配置同步到自动化帐户。 可将此变量命名为 VSToken。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

导入用于将 Runbook 或配置同步到自动化帐户的 Runbook。 根据是要使用 Azure DevOps 源代码管理还是要使用包含 Git 的 Azure DevOps 以及是否要部署到自动化帐户，可以从 [PowerShell 库](https://www.powershellgallery.com)使用 [Azure DevOps 示例 Runbook](https://www.powershellgallery.com/packages/Sync-VSTS) 或[包含 Git 的 Azure DevOps 示例 Runbook](https://www.powershellgallery.com/packages/Sync-VSTSGit)。

![PowerShell 库](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

现在，可以[发布](automation-creating-importing-runbook.md#publishing-a-runbook)此 Runbook，以便创建 Webhook。

![发布 runbook](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

为此 Sync-VSTS Runbook 创建 [Webhook](automation-webhooks.md) 并按如下所示填写参数。 请务必复制 Webhook URL，因为到时需要使用它在 Azure DevOps 中创建服务挂钩。 VSAccessTokenVariableName 是前面创建用来保存个人访问令牌的安全变量的名称 (VSToken)。

与 Azure DevOps (Sync-VSTS.ps1) 的集成会采用以下参数：

### <a name="sync-vsts-parameters"></a>Sync-VSTS 参数

参数 | Description|
--------|------------|
WebhookData | 此项包含从 Azure DevOps 服务挂钩发送的签入信息。 应将此参数留空。| 
resourceGroup | 这是自动化帐户所在的资源组的名称。|
AutomationAccountName | 与 Azure DevOps 同步的自动化帐户的名称。|
VSFolder | Azure DevOps 中的、Runbook 和配置所在的文件夹的名称。|
VSAccount | Azure DevOps 组织的名称。|
VSAccessTokenVariableName | 保存 Azure DevOps 个人访问令牌的安全变量的名称 (VSToken)。|

![Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

如果使用的是包含 GIT 的 Azure DevOps (Sync-VSTSGit.ps1)，该集成会采用以下参数。

参数 | Description|
--------|------------|
WebhookData | 这将包含从 Azure DevOps 服务挂钩发送的签入信息。 应将此参数留空。|
resourceGroup | 这是自动化帐户所在的资源组的名称。|
AutomationAccountName | 与 Azure DevOps 同步的自动化帐户的名称。|
VSAccount | Azure DevOps 组织的名称。|
VSProject | Azure DevOps 中的、Runbook 和配置所在的项目的名称。|
GitRepo | Git 存储库的名称。|
GitBranch | Azure DevOps Git 存储库中的分支名称。|
Folder | Azure DevOps Git 分支中的文件夹的名称。|
VSAccessTokenVariableName | 保存 Azure DevOps 个人访问令牌的安全变量的名称 (VSToken)。|

![GIT Webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

在 Azure DevOps 中为文件夹签入创建服务挂钩，以便在签入代码时触发此 Webhook。 选择“Web 挂钩”作为创建新订阅时要集成的服务。 可以通过 [Azure DevOps 服务挂钩文档](https://www.visualstudio.com/docs/marketplace/integrate/service-hooks/get-started)了解有关服务挂钩的详细信息。
![服务挂钩](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

现在，应该可以向 Azure DevOps 执行所有的 Runbook 和配置签入，让这些 Runbook 和配置自动同步到自动化帐户。

![同步 runbook 输出](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

如果在未由 Azure DevOps 触发的情况下手动运行此 Runbook，可将 webhookdata 参数留空，这样就会从指定的 Azure DevOps 文件夹执行完全同步。

如果想要卸载本方案，请从 Azure DevOps 中删除服务挂钩，并删除 Runbook 和 VSToken 变量。
