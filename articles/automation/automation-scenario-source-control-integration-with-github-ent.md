---
title: Azure 自动化中源代码管理与 GitHub Enterprise 的集成
description: 详细介绍如何配置与 GitHub Enterprise 的集成，以便对自动化 Runbook 进行源代码管理。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 65894e40b192c6a5a226fa7a1dfb5cb0cfabb972
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936141"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Azure 自动化方案 - 与 GitHub Enterprise 集成的自动化源代码管理

自动化目前支持源代码管理集成，用于会自动化帐户中的 Runbook 关联到 GitHub 源代码管理存储库。 但是，那些已部署 [GitHub Enterprise](https://enterprise.github.com/home) 来支持其 DevOps 实践的客户也需要使用它管理 Runbook 的生命周期，开发这些 Runbook 的目的是实现业务流程和服务管理操作的自动化。

在此方案中，需将数据中心的一台 Windows 计算机配置为混合 Runbook 辅助角色，其中安装了 Azure 资源管理器模块和 Git 工具。 混合辅助角色计算机具有本地 Git 存储库的克隆。 在混合辅助角色上运行 Runbook 时，会同步 Git 目录并将 Runbook 文件内容导入到自动化帐户中。

本文介绍如何在 Azure 自动化环境中设置此配置。 首先，会为自动化配置安全凭据以及支持此方案所需的 Runbook，并会将混合 Runbook 辅助角色部署到数据中心，以便运行 Runbook 并访问 GitHub Enterprise 存储库，从而通过自动化帐户来同步 Runbook。

## <a name="getting-the-scenario"></a>获取方案

此方案包含的两个 PowerShell Runbook 可以在 Azure 门户中直接从 [Runbook 库](automation-runbook-gallery.md)导入，也可以从 [PowerShell 库](https://www.powershellgallery.com)下载。

### <a name="runbooks"></a>Runbook

Runbook | 说明|
--------|------------|
Export-RunAsCertificateToHybridWorker | Runbook 会将 RunAs 证书从自动化帐户导出到混合辅助角色，使得辅助角色上的 Runbook 能够通过 Azure 进行身份验证，以便将 Runbook 导入到自动化帐户中。|
Sync-LocalGitFolderToAutomationAccount | Runbook 会同步混合计算机上的本地 Git 文件夹，并将 Runbook 文件 (*.ps1) 导入到自动化帐户中。|

### <a name="credentials"></a>凭据

凭据 | 说明|
-----------|------------|
GitHRWCredential | 创建的凭据资产，其中包含具有混合辅助角色权限的用户的用户名和密码。|

## <a name="installing-and-configuring-this-scenario"></a>安装和配置此方案

### <a name="prerequisites"></a>先决条件

1. Sync-LocalGitFolderToAutomationAccount Runbook 使用 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)进行身份验证。

2. 此外还需一个启用并配置了 Azure 自动化解决方案的 Log Analytics 工作区。 如果没有这样一个与自动化帐户（用于安装和配置此方案）关联的工作区，系统会在用户通过混合 Runbook 辅助角色执行 **New-OnPremiseHybridWorker.ps1** 脚本时为其创建和配置一个。

    > [!NOTE]
    > 目前仅以下区域支持自动化与 Log Analytics 集成：澳大利亚东南部、美国东部 2、东南亚以及西欧。

3. 一台计算机，可充当专用混合 Runbook 辅助角色，该角色也可在文件系统的源目录中托管 GitHub 软件并维护 Runbook 文件 (*runbook*.ps1)，以便在 GitHub 和自动化帐户之间进行同步。

### <a name="import-and-publish-the-runbooks"></a>导入和发布 Runbook

若要在 Azure 门户中通过自动化帐户的 Runbook 库导入 *Export-RunAsCertificateToHybridWorker* 和 *Sync-LocalGitFolderToAutomationAccount* Runbook，请按[从 Runbook 库导入 Runbook](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal) 中的过程操作。 在成功将 Runbook 导入到自动化帐户后，发布这些 Runbook。

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>部署和配置混合 Runbook 辅助角色

如果尚未将混合 Runbook 辅助角色部署到数据中心，应查看相关要求，然后按照“Azure 自动化混合 Runbook 辅助角色 - 为 [Windows](automation-windows-hrw-install.md#automated-deployment) 或 [Linux](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker) 自动化安装和配置”中的过程执行自动化安装步骤。 在计算机中成功安装混合辅助角色以后，请执行以下步骤，完成支持此方案所需的配置。

1. 使用一个具有本地管理权限的帐户登录到托管混合 Runbook 辅助角色的计算机，并创建一个目录来保存 Git Runbook 文件。 将内部 Git 存储库克隆到该目录。
1. 如果尚未创建运行方式帐户，或者需要创建一个专用于此目的的新帐户，请从 Azure 门户导航到自动化帐户，选择自动化帐户并创建[凭据资产](automation-credentials.md)，其中包含有权访问混合辅助角色的用户的用户名和密码。
1. 在自动化帐户中，[编辑 Runbook](automation-edit-textual-runbook.md) **Export-RunAsCertificateToHybridWorker** 并使用强密码修改变量 *$Password* 的值。  修改该值后，单击“发布”发布该 Runbook 的草稿版。
1. 启动 Runbook **Export-RunAsCertificateToHybridWorker**，在“启动 Runbook”边栏选项卡的“运行设置”选项下选择“混合辅助角色”选项，并在下拉列表中选择以前为此方案创建的混合辅助角色组。

    此时会将证书导出到混合辅助角色，以便辅助角色上的 Runbook 能够使用 RunAs 连接通过 Azure 进行身份验证，以便管理 Azure 资源（具体就此方案来说 - 将 Runbook 导入到自动化帐户）。

1. 在自动化帐户中，选择此前创建的混合辅助角色组，并[指定运行方式帐户](automation-hrw-run-runbooks.md#runas-account)（用于混合辅助角色组），然后选择刚创建或已经创建一段时间的凭据资产。 这确保同步 Runbook 可以运行 Git 命令。 
1. 启动 Runbook **Sync-LocalGitFolderToAutomationAccount**，提供以下必需的输入参数值，在“启动 Runbook”边栏选项卡的“运行设置”选项下选择“混合辅助角色”选项，并在下拉列表中选择以前为此方案创建的混合辅助角色组：

   * *ResourceGroup* - 与自动化帐户关联的资源组的名称
   * AutomationAccountName - 自动化帐户的名称
   * *GitPath* - 混合 Runbook 辅助角色上的本地文件夹或文件，Git 已设置为将最新更改拉取到其中

    这样会同步混合辅助角色计算机上的本地 Git 文件夹，然后将 .ps1 文件从源目录导入到自动化帐户中。

1. 查看 Runbook 的作业摘要详细信息（方法是在自动化帐户中从“Runbook”边栏选项卡进行选择），并选择“作业”磁贴。 确认成功完成，方法是选择“所有日志”磁贴，并查看详细的日志流。

## <a name="next-steps"></a>后续步骤

* 若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)
* 有关 PowerShell 脚本支持功能的详细信息，请参阅 [Azure 自动化中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
