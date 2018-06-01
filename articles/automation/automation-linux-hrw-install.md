---
title: Azure 自动化 Linux 混合 Runbook 辅助角色
description: 本文介绍如何安装 Azure 自动化混合 Runbook 辅助角色，该角色可以用于在本地数据中心或云环境的基于 Linux 的计算机上运行 Runbook。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aca68b6e8d0e6b80a1504b16b9b3462f20fdc6c4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195652"
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>如何部署 Linux 混合 Runbook 辅助角色

利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Linux 混合 Runbook 辅助角色以特殊用户身份执行 Runbook，该用户身份可进行权限提升，以运行需要提升权限的命令。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一个或多个指定计算机。 本文介绍如何在 Linux 计算机上安装混合 Runbook 辅助角色。

## <a name="supported-linux-operating-systems"></a>支持的 Linux 操作系统

以下是支持的 Linux 分发版本的列表：

* Amazon Linux 2012.09 --> 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 和 12 (x86/x64)

## <a name="installing-linux-hybrid-runbook-worker"></a>安装 Linux 混合 Runbook 辅助角色

若要在 Linux 计算机上安装和配置混合 Runbook 辅助角色，请按照一个简单明了的过程手动安装和配置此角色。 它需要启用 Log Analytics 工作区中的“自动化混合辅助角色”解决方案，然后运行一组命令将计算机注册为辅助角色，并且将它添加至一个新组或现有的组中。

Linux 混合 Runbook 辅助角色的最低要求如下：

* 至少双核
* 至少 4 GB RAM
* 端口 443（出站）

### <a name="package-requirements"></a>程序包要求

| **必需的程序包** | **说明** | **最低版本**|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 库| 2.5-12 |
|Openssl| OpenSSL 库 | 0.9.8e 或 1.0|
|Curl | cURL Web 客户端 | 7.15.5|
|Python-ctype | |
|PAM | 可插入验证模块|

继续操作前，需要记录自动化帐户关联的 Log Analytics 工作区及自动化帐户的主键。 通过选择自动化帐户，选择工作区 ID 的“工作区”及选择主键的“键”，可从门户中找到这两者。 有关混合 Runbook 辅助角色所需的端口和地址的信息，请参阅[配置网络](automation-hybrid-runbook-worker.md#network-planning)。

1. 启用 Azure 中的“自动化混合辅助角色”解决方案。 可通过以下任一方式实现：

   1. 通过[将 Log Analytics 管理解决方案添加到工作区](../log-analytics/log-analytics-add-solutions.md)中所述的过程，将“自动化混合辅助角色”解决方案添加到你的订阅。
   1. 运行以下 cmdlet：

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. 运行以下命令安装适用于 Linux 的 OMS 代理，并将 \<WorkspaceID\> 和 \<WorkspaceKey\> 替换为工作区中的相应值。

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. 运行以下命令，更改 *-w*、*-k*、*-g* 和 *-e* 参数的值。 对于 *-g* 参数，请将值替换为新的 Linux 混合 Runbook 辅助角色应加入的混合 Runbook 辅助角色组的名称。 如果自动化帐户中尚不存在该名称，则将使用该名称生成一个新的混合 Runbook 辅助角色组。

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. 此命令完成后，Azure 门户中的“混合辅助角色组”页面会显示新组和成员数，或者会将成员数进行递增（如果组已存在）。 可以从“混合辅助角色组”页上的列表中选择组，并选择“混合辅助角色”磁贴。 在“混合辅助角色”页上，会列出组的每个成员。

## <a name="turning-off-signature-validation"></a>关闭签名验证

默认情况下，Linux 混合 Runbook 辅助角色需要签名验证。 如果针对辅助角色运行未签名的 runbook，将看到包含“签名验证失败”字样的错误。 若要关闭签名验证，请运行以下命令，并将第二个参数替换为 Log Analytics 工作区 ID：

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>支持的 runbook 类型

Linux 混合 Runbook 辅助角色并非支持 Azure 自动化中存在的全套 runbook 类型。

以下 runbook 类型可以在 Linux 混合辅助角色上工作：

* Python 2
* PowerShell

以下 runbook 类型不能在 Linux 混合辅助角色上工作：

* PowerShell 工作流
* 图形
* 图形 PowerShell 工作流

## <a name="troubleshooting"></a>故障排除

Linux 混合 Runbook 辅助角色依靠适用于 Linux 的 OMS 代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

### <a name="the-oms-agent-for-linux-is-not-running"></a>适用于 Linux 的 OMS 代理未运行

如果适用于 Linux 的 OMS 代理未运行，这会导致 Linux 混合 Runbook 辅助角色无法与 Azure 自动化通信。 输入以下命令，验证代理是否正在运行：`ps -ef | grep python`。 你应该看到类似如下的输出，即使用 **nxautomation** 用户帐户的 python 进程。 如果未启用更新管理或 Azure 自动化解决方案，则以下任何进程都不会运行。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

以下列表显示针对 Linux 混合 Runbook 辅助角色启动的进程。 它们都位于 `/var/opt/microsoft/omsagent/state/automationworker/` 目录中。

* **oms.conf**：这是辅助角色管理器进程，直接从 DSC 启动。

* **worker.conf**：此进程是自动注册混合辅助角色进程，由辅助角色管理器启动。 此进程由更新管理使用且对用户而言是透明的。 如果未在计算机上启用更新管理解决方案，则不会显示此进程。

* **diy/worker.conf**：此进程是 DIY 混合辅助角色进程。 DIY 混合辅助角色进程用于执行混合 Runbook 辅助角色的用户 Runbook。 它仅与使用不同配置的自动注册混合辅助角色进程在主要细节上有所不同。 如果未启用 Azure 自动化解决方案，并且 DIY Linux 混合辅助角色未注册，则不会显示此进程。

如果适用于 Linux 的 OMS 代理未运行，请运行以下命令启动该服务：`sudo /opt/microsoft/omsagent/bin/service_control restart`。

### <a name="the-specified-class-does-not-exist"></a>指定的类不存在

如果看到错误**指定的类不存在..** （在 `/var/opt/microsoft/omsconfig/omsconfig.log` 中），则需要更新适用于 Linux 的 OMS 代理。 运行以下命令重新安装 OMS 代理：

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

有关如何对更新管理问题进行故障排除的其他步骤，请参阅[更新管理 - 故障排除](automation-update-management.md#troubleshooting)

## <a name="next-steps"></a>后续步骤

* 查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。
* 若要获取有关如何删除混合 Runbook 辅助角色的说明，请参阅[删除 Azure 自动化混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)
