---
title: Azure 自动化 Linux 混合 Runbook 辅助角色
description: 本文介绍如何安装 Azure 自动化混合 Runbook 辅助角色，以便在本地数据中心或云环境的基于 Linux 的计算机上运行 Runbook。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8507cf99ea22b24aa3026565cb7c4139e4c3742d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267859"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>部署 Linux 混合 Runbook 辅助角色

利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Linux 混合 Runbook 辅助角色以特殊用户身份执行 Runbook，该用户身份可进行权限提升，以运行需要提升权限的命令。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一个或多个指定计算机。

本文介绍如何在 Linux 计算机上安装混合 Runbook 辅助角色。

## <a name="supported-linux-operating-systems"></a>支持的 Linux 操作系统

混合 Runbook 辅助角色功能支持以下分发版：

* Amazon Linux 2012.09 到 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS 和 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 和 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>安装 Linux 混合 Runbook 辅助角色

若要在 Linux 计算机上安装和配置混合 Runbook 辅助角色，请按照一个简单明了的过程手动安装和配置此角色。 它需要启用 Azure Log Analytics 工作区中的“自动化混合辅助角色”解决方案，然后运行一组命令将计算机注册为辅助角色，并且将它添加到组中。

Linux 混合 Runbook 辅助角色的最低要求如下：

* 双核
* 4 GB RAM
* 端口 443（出站）

### <a name="package-requirements"></a>程序包要求

| **必需的程序包** | **说明** | **最低版本**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 库| 2.5-12 |
|Openssl| OpenSSL 库 | 0.9.8e 或 1.0|
|Curl | cURL Web 客户端 | 7.15.5|
|Python-ctype | |
|PAM | 可插入验证模块|
| **可选包** | **说明** | **最低版本**|
| PowerShell Core | 若要运行 PowerShell Runbook，需要安装 PowerShell，请参阅[在 Linux 上安装 PowerShell Core](/powershell/scripting/setup/installing-powershell-core-on-linux) 了解如何安装。  | 6.0.0 |

### <a name="installation"></a>安装

在继续操作之前，请记下自动化帐户链接到的 Log Analytics 工作区。 另请记下自动化帐户的主密钥。 在 Azure 门户中选择自己的自动化帐户，选择工作区 ID 对应的“工作区”，然后选择主密钥对应的“密钥”，即可找到这两个值。 有关混合 Runbook 辅助角色所需的端口和地址的信息，请参阅[配置网络](automation-hybrid-runbook-worker.md#network-planning)。

1. 使用以下方法之一，在 Azure 中启用“自动化混合辅助角色”解决方案：

   * 使用[将 Log Analytics 管理解决方案添加到工作区](../log-analytics/log-analytics-add-solutions.md)中所述的过程，将“自动化混合辅助角色”解决方案添加到订阅。
   * 运行以下 cmdlet：

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 运行以下命令，安装适用于 Linux 的 OMS 代理。 请将 \<WorkspaceID\> 和 \<WorkspaceKey\> 替换为工作区中的相应值。

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. 运行以下命令，更改 *-w*、*-k*、*-g* 和 *-e* 参数的值。 对于 *-g* 参数，请将值替换为新的 Linux 混合 Runbook 辅助角色应加入的混合 Runbook 辅助角色组的名称。 如果自动化帐户中尚不存在该名称，系统会使用该名称生成一个新的混合 Runbook 辅助角色组。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. 命令完成后，Azure 门户中的“混合辅助角色组”页面会显示新组和成员数。 如果这是现有的组，则成员数会递增。 可以从“混合辅助角色组”页上的列表中选择组，并选择“混合辅助角色”磁贴。 在“混合辅助角色”页上，会列出组的每个成员。

## <a name="turning-off-signature-validation"></a>关闭签名验证

默认情况下，Linux 混合 Runbook 辅助角色需要签名验证。 如果针对辅助角色运行未签名的 runbook，将看到显示“签名验证失败”字样的错误。 若要禁用签名验证，请运行以下命令。 将第二个参数替换为 Log Analytics 工作区 ID。

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>支持的 runbook 类型

Linux 混合 Runbook 辅助角色并非支持 Azure 自动化中的全套 Runbook 类型。

以下 runbook 类型可以在 Linux 混合辅助角色上工作：

* Python 2
* PowerShell

  > [!NOTE]
  > PowerShell Runbook 要求在 Linux 计算机上安装 PowerShell Core。 请参阅[在 Linux 上安装 PowerShell Core](/powershell/scripting/setup/installing-powershell-core-on-linux) 了解如何安装。

以下 Runbook 类型不能在 Linux 混合辅助角色上运行：

* PowerShell 工作流
* 图形
* 图形 PowerShell 工作流

## <a name="troubleshooting"></a>故障排除

Linux 混合 Runbook 辅助角色依靠适用于 Linux 的 OMS 代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因。

### <a name="the-oms-agent-for-linux-isnt-running"></a>适用于 Linux 的 OMS 代理未运行

如果适用于 Linux 的 OMS 代理未运行，则 Linux 混合 Runbook 辅助角色无法与 Azure 自动化通信。 输入命令 `ps -ef | grep python`，验证代理是否正在运行。 

应会看到如下所示的输出（使用 **nxautomation** 用户帐户的 Python 进程）。 如果未启用更新管理或 Azure 自动化解决方案，则以下任何进程都不会运行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

针对 Linux 混合 Runbook 辅助角色启动了以下进程。 这些进程全都位于 `/var/opt/microsoft/omsagent/state/automationworker/` 目录中。

* **oms.conf**：这是辅助角色管理器进程。 它直接从 Desired State Configuration (DSC) 启动。

* **worker.conf**：这是自动注册的混合辅助角色进程。 它由辅助角色管理器启动。 此进程由更新管理使用且对用户而言是透明的。 仅当已在计算机上启用了更新管理解决方案时，才会显示此进程。

* **diy/worker.conf**：这是 DIY 混合辅助角色进程。 DIY 混合辅助角色进程用于执行混合 Runbook 辅助角色的用户 Runbook。 它与自动注册的混合辅助角色进程的唯一差别在于它使用不同的配置。 仅当已启用 Azure 自动化解决方案并且已注册 DIY Linux 混合辅助角色时，才会显示此进程。

如果适用于 Linux 的 OMS 代理未运行，请运行以下命令启动该服务：`sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="the-specified-class-doesnt-exist"></a>指定的类不存在

如果 `/var/opt/microsoft/omsconfig/omsconfig.log` 中出现错误“指定的类不存在”，需要更新适用于 Linux 的 OMS 代理。 运行以下命令重新安装 OMS 代理：

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

有关如何对更新管理问题进行故障排除的其他步骤，请参阅[更新管理：故障排除](automation-update-management.md#troubleshooting)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。
* 有关如何删除混合 Runbook 辅助角色的说明，请参阅[删除 Azure 自动化混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)。
