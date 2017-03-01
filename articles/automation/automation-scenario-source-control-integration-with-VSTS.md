---
title: "将 Azure 自动化与 Visual Stuido Team Services 源代码管理集成 | Microsoft 文档"
description: "本方案逐步讲解如何设置 Azure 自动化帐户与 Visual Stuido Team Services 源代码管理的集成。"
services: automation
documentationcenter: 
author: eamono
manager: 
editor: 
keywords: "azure powershell, VSTS, 源代码管理, 自动化"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
translationtype: Human Translation
ms.sourcegitcommit: f68563587d375dde22fd1ca45591fd49042533d0
ms.openlocfilehash: 01f9c01c9e04e02dbb548b68cf99684ba6ddd57e
ms.lasthandoff: 02/21/2017

---
# <a name="azure-automation-scenario---automation-source-control-integration-with-visual-studio-team-services"></a>Azure 自动化方案 - 自动化源代码管理与 Visual Studio Team Services 的集成

在本方案中，你要使用一个 Visual Studio Team Services 项目来管理源代码管理中的 Azure 自动化 Runbook 或 DSC 配置。
本文介绍如何将 VSTS 与 Azure 自动化环境集成，以便每次签入时都能发生持续集成。

## <a name="getting-the-scenario"></a>获取方案

此方案包含的两个 PowerShell Runbook 可以在 Azure 门户中直接从 [Runbook 库](automation-runbook-gallery.md)导入，也可以从 [PowerShell 库](https://www.powershellgallery.com)下载。

### <a name="runbooks"></a>Runbook

Runbook | 说明| 
--------|------------|
Sync-VSTS | 执行签入时从 VSTS 源代码管理导入 Runbook 或配置。 如果手动运行该 Runbook，它会将所有 Runbook 或配置导入并发布到自动化帐户。| 
Sync-VSTSGit | 执行签入时从 Git 源代码管理中的 VSTS 导入 Runbook 或配置。 如果手动运行该 Runbook，它会将所有 Runbook 或配置导入并发布到自动化帐户。|

### <a name="variables"></a>变量

变量 | 说明|
-----------|------------|
VSToken | 要创建的安全变量资产，包含 VSTS 个人访问令牌。 可在 [VSTS 身份验证页](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview)上了解如何创建 VSTS 个人访问令牌。 
## <a name="installing-and-configuring-this-scenario"></a>安装和配置此方案

在 VSTS 中创建[个人访问令牌](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview)，到时需要使用它来将 Runbook 或配置同步到自动化帐户。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

在自动化帐户中创建一个[安全变量](automation-variables.md)用于保存个人访问令牌，使 Runbook 能够向 VSTS 进行身份验证，将 Runbook 或配置同步到自动化帐户。 可将此变量命名为 VSToken。 

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

导入用于将 Runbook 或配置同步到自动化帐户的 Runbook。 根据是要使用 VSTS 源代码管理还是包含 Git 的 VSTS 以及是否要部署到自动化帐户，可以从 PowerShellGallery.com 使用 [VSTS 示例 Runbook](https://www.powershellgallery.com/packages/Sync-VSTS/1.0/DisplayScript) 或 [包含 Git 的 VSTS 示例 Runbook] (https://www.powershellgallery.com/packages/Sync-VSTSGit/1.0/DisplayScript)。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

现在，可以[发布](automation-creating-importing-runbook.md#publishing-a-runbook)此 Runbook，以便创建 Webhook。 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

为此 Sync-VSTS Runbook 创建 [Webhook](automation-webhooks.md) 并按如下所示填写参数。 请务必复制 Webhook URL，因为到时需要使用它在 VSTS 中创建服务挂钩。 VSAccessTokenVariableName 是前面创建用来保存个人访问令牌的安全变量的名称 (VSToken)。 

与 VSTS (Sync-VSTS.ps1) 的集成将采用以下参数。
### <a name="sync-vsts-parameters"></a>Sync-VSTS 参数

参数 | 说明| 
--------|------------|
WebhookData | 包含 VSTS 服务挂钩发送的签入信息。 应将此参数留空。| 
resourceGroup | 这是自动化帐户所在的资源组的名称。|
AutomationAccountName | 将与 VSTS 同步的自动化帐户的名称。|
VSFolder | VSTS 中的、Runbook 和配置所在的文件夹的名称。|
VSAccount | Visual Studio Team Services 帐户的名称。| 
VSAccessTokenVariableName | 保存 VSTS 个人访问令牌的安全变量的名称 (VSToken)。| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

如果使用的是包含 GIT 的 VSTS (Sync-VSTSGit.ps1)，该集成将采用以下参数。

参数 | 说明|
--------|------------|
WebhookData | 包含 VSTS 服务挂钩发送的签入信息。 应将此参数留空。| resourceGroup | 这是自动化帐户所在的资源组的名称。|
AutomationAccountName | 将与 VSTS 同步的自动化帐户的名称。|
VSAccount | Visual Studio Team Services 帐户的名称。|
VSProject | VSTS 中的、Runbook 和配置所在的项目的名称。|
GitRepo | Git 存储库的名称。|
GitBranch | VSTS Git 存储库中的分支名称。|
文件夹 | VSTS Git 分支中的文件夹的名称。|
VSAccessTokenVariableName | 保存 VSTS 个人访问令牌的安全变量的名称 (VSToken)。|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

在 VSTS 中为文件夹签入创建服务挂钩，以便在签入代码时触发此 Webhook。 选择“Web 挂钩”作为创建新订阅时要集成的服务。 可以通过 [VSTS 服务挂钩文档](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started)了解有关服务挂钩的详细信息。
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

现在，应该可以向 VSTS 执行所有的 Runbook 和配置签入，让这些 Runbook 和配置将自动同步到你的自动化帐户。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

如果在未由 VSTS 触发的情况下手动运行此 Runbook，可将 webhookdata 参数留空，这样就会从指定的 VSTS 文件夹执行完全同步。

如果想要卸载本方案，请从 VSTS 中删除服务挂钩，然后删除 Runbook 和 VSToken 变量。

