---
title: 在 Azure 自动化中部署 Linux 混合 Runbook 辅助角色
description: 本文介绍如何安装 Azure 自动化混合 Runbook 辅助角色，以便在本地数据中心或云环境中基于 Linux 的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 8295b6bba9703c276bf60a0360ded6f0e195369e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776266"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>部署 Linux 混合 Runbook 辅助角色

利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Linux 混合 Runbook 辅助角色以特殊用户身份执行 Runbook，该用户身份可进行权限提升，以运行需要提升权限的命令。 Azure 自动化将存储并管理 Runbook，然后将其传送到一台或多台指定的计算机。 本文介绍了如何在 Linux 计算机上安装混合 Runbook 辅助角色，如何删除辅助角色，以及如何删除混合 Runbook 辅助角色组。

成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保做好以下准备：

### <a name="a-log-analytics-workspace"></a>Log Analytics 工作区

混合 Runbook 辅助角色依赖于 Azure Monitor Log Analytics 工作区来安装和配置角色。 你可通过 [Azure 资源管理器](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)、[PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json) 或在 [Azure 门户](../azure-monitor/learn/quick-create-workspace.md)中创建该工作区。

如果没有 Azure Monitor Log Analytics 工作区，请在创建工作区前查看 [Azure Monitor 日志设计指南](../azure-monitor/platform/design-logs-deployment.md)。

如果你有工作区，但它没有与你的自动化帐户关联，则启用自动化功能会添加 Azure 自动化的功能，包括对混合 Runbook 辅助角色的支持。 当你在 Log Analytics 工作区中启用 Azure 自动化功能之一时，特别 [更新管理](update-management/update-mgmt-overview.md) 或 [更改跟踪和清单](change-tracking.md)，辅助角色组件会自动推送到代理计算机。

若要将更新管理功能添加到工作区，请运行以下 PowerShell cmdlet：

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

若要将更改跟踪和清单功能添加到工作区，请运行以下 PowerShell cmdlet：

```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics 代理

混合 Runbook 辅助角色需要受支持的 Linux 操作系统的 [Log Analytics 代理](../azure-monitor/platform/log-analytics-agent.md)。

>[!NOTE]
>安装适用于 Linux 的 Log Analytics 代理后，不应更改 `sudoers.d` 文件夹或其所有权的权限。 Sudo 权限对于 **nxautomation** 帐户是必需的，后者是混合 Runbook 辅助角色在其下运行的用户上下文。 不应删除这些权限。 将此限制为某些文件夹或命令可能会导致重大更改。
>

### <a name="supported-linux-operating-systems"></a>受支持的 Linux 操作系统

混合 Runbook 辅助角色功能支持以下分发版：

* Amazon Linux 2012.09 到 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、16.04 LTS 和 18.04 (x86/x64)
* SUSE Linux Enterprise Server 12 (x86/x64) 

### <a name="minimum-requirements"></a>最低要求

Linux 混合 Runbook 辅助角色的最低要求如下：

* 双核
* 4 GB RAM
* 端口 443（出站）

| **必需的程序包** | **说明** | **最低版本**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 库| 2.5-12 |
|Openssl| OpenSSL 库 | 1.0（支持 TLS 1.1 和 TLS 1.2）|
|Curl | cURL Web 客户端 | 7.15.5|
|Python-ctype | 需要 Python 2.x |
|PAM | 可插入验证模块|
| **可选包** | **说明** | **最低版本**|
| PowerShell Core | 若要运行 PowerShell runbook，需要安装 PowerShell Core。 请参阅[在 Linux 上安装 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) 了解如何安装。 | 6.0.0 |

## <a name="supported-linux-hardening"></a>支持的 Linux 强化

目前尚不支持以下项：

* CIS

## <a name="supported-runbook-types"></a>支持的 runbook 类型

Linux 混合 Runbook 辅助角色支持 Azure 自动化中有限的一组 Runbook 类型，下表对它们进行了介绍。

|Runbook 类型 | 支持 |
|-------------|-----------|
|Python 2 |是 |
|PowerShell |是<sup>1</sup> |
|PowerShell 工作流 |否 |
|图形 |否 |
|图形 PowerShell 工作流 |否 |

<sup>1</sup>PowerShell Runbook 要求在 Linux 计算机上安装 PowerShell Core。 请参阅[在 Linux 上安装 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) 了解如何安装。

## <a name="install-a-linux-hybrid-runbook-worker"></a>安装 Linux 混合 Runbook 辅助角色

若要安装和配置 Linux 混合 Runbook 辅助角色，请执行以下步骤。

1. 将 Log Analytics 代理部署到目标计算机。

    * 对于 Azure VM，请使用[适用于 Linux 的虚拟机扩展](../virtual-machines/extensions/oms-linux.md)安装适用于 Linux 的 Log Analytics 代理。 该扩展通过 Azure 资源管理器模板或 Azure CLI 在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Log Analytics 工作区中。 安装代理后，可将 VM 添加到自动化帐户中的混合 Runbook 辅助角色组。

    * 对于非 Azure VM，请使用[将 Linux 计算机连接到 Azure Monitor](../azure-monitor/platform/agent-linux.md) 一文中所述的部署选项安装适用于 Linux 的 Log Analytics 代理。 可以对多台计算机重复此过程，以将多个辅助角色添加到环境。 安装代理后，可将 VM 添加到自动化帐户中的混合 Runbook 辅助角色组。

    > [!NOTE]
    > 为了使用 Desired State Configuration (DSC) 管理支持混合 Runbook 辅助角色的计算机配置，必须将计算机添加为 DSC 节点。

    > [!NOTE]
    > 安装 Linux 混合辅助角色期间，必须存在具有相应 sudo 权限的 [nxautomation 帐户](automation-runbook-execution.md#log-analytics-agent-for-linux)。 如果尝试安装辅助角色，但该帐户不存在或没有相应权限，则安装会失败。

2. 验证代理是否向工作区报告。

    适用于 Linux 的 Log Analytics 代理会将计算机连接到 Azure Monitor Log Analytics 工作区。 在计算机上安装代理并将其连接到工作区时，代理会自动下载混合 Runbook 辅助角色所需的组件。

    几分钟后，如果代理已成功连接到 Log Analytics 工作区，则可以运行以下查询，验证是否正在向工作区发送检测信号数据。

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    在搜索结果中，应会看到计算机的检测信号记录，它们指示计算机已连接到服务并将该结果报告给服务。 默认情况下，每个代理都会将一个检测信号记录转发到其分配的工作区。

3. 运行以下命令，以将计算机添加到混合 Runbook 辅助角色组，并为参数 `-w`、`-k`、`-g` 和 `-e` 指定值。

    你可以从自动化帐户中的“密钥”页获取参数 `-k` 和 `-e` 所需的信息。 从页面左侧的“帐户设置”部分下，选择“密钥” 。

    ![“管理密钥”页](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

    * 对于 `-e` 参数，复制 URL 的值。

    * 对于 `-k` 参数，复制主访问密钥的值。

    * 对于 `-g` 参数，请指定新的 Linux 混合 Runbook 辅助角色应加入的混合 Runbook 辅助角色组的名称。 如果自动化帐户中已存在该组，则会将当前计算机添加到其中。 如果该组不存在，则会使用该名称创建它。

    * 对于 `-w` 参数，请指定 Log Analytics 工作区 ID。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. 命令完成后，自动化帐户中的“混合辅助角色组”页会显示新组和成员数。 如果这是现有的组，则成员数会递增。 可以从“混合辅助角色组”页上的列表中选择组，并选择“混合辅助角色”磁贴。 在“混合辅助角色”页上，会列出组的每个成员。

    > [!NOTE]
    > 如果要对 Azure VM 使用用于 Linux 的 Log Analytics 虚拟机扩展，建议将 `autoUpgradeMinorVersion` 设置为 `false`，因为自动升级版本可能会导致混合 Runbook 辅助角色出问题。 若要了解如何手动升级扩展，请参阅 [Azure CLI 部署](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment)。

## <a name="turn-off-signature-validation"></a>关闭签名验证

默认情况下，Linux 混合 Runbook 辅助角色需要签名验证。 如果针对辅助角色运行未签名的 Runbook，将看到 `Signature validation failed` 错误。 若要禁用签名验证，请运行以下命令。 将第二个参数替换为 Log Analytics 工作区 ID。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>从本地 Linux 计算机中删除混合 Runbook 辅助角色

可在混合 Runbook 辅助角色上使用命令 `ls /var/opt/microsoft/omsagent` 获取工作区 ID。 将创建一个使用工作区 ID 命名的文件夹。

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 此脚本不会从计算机中删除适用于 Linux 的 Log Analytics 代理。 它只会删除混合 Runbook 辅助角色的功能和配置。

## <a name="remove-a-hybrid-worker-group"></a>删除混合辅助角色组

若要删除 Linux 计算机的混合 Runbook 辅助角色组，请按照删除 Windows 混合辅助角色组的相同步骤进行操作。 请参阅[删除混合辅助角色组](automation-windows-hrw-install.md#remove-a-hybrid-worker-group)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[排查混合 Runbook 辅助角色问题 - Linux](troubleshoot/hybrid-runbook-worker.md#linux)。
