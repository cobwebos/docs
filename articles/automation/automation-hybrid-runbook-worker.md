---
title: Azure 自动化混合 Runbook 辅助角色
description: 本文介绍如何安装和使用混合 Runbook 辅助角色，该角色是 Azure 自动化的一项功能，可以用于在本地数据中心或云提供商的计算机上运行 Runbook。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 270990d5dea53c2467bf2d7df4695a14b3dbec8c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194615"
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>使用混合 Runbook 辅助角色使数据中心或云端的资源实现自动化

Azure 自动化中的 Runbook 可能无法访问其他云或本地环境中的资源，因为它们在 Azure 云中运行。 利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Runbook 在 Azure 自动化中进行存储和管理，然后发送到一个或多个指定计算机。

下图说明了此功能：

![混合 Runbook 辅助角色概述](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>混合 Runbook 辅助角色组

每个混合 Runbook 辅助角色都是你在安装代理时指定的混合 Runbook 辅助角色组的成员。 一个组可以包含一个代理，但是可以在一个组中安装多个代理，以实现高可用性。

在混合 Runbook 辅助角色中启动 Runbook 时，可以指定该辅助角色会在其中运行的组。 组中的每个辅助角色都会轮询 Azure 自动化以查看是否有可用作业。 如果有可用的作业，则获取作业的第一个辅助角色将执行该作业。 不能指定特定的辅助角色。

## <a name="installing-a-hybrid-runbook-worker"></a>安装混合 Runbook 辅助角色

安装混合 Runbook 辅助角色的过程根据 OS 而有所不同。 下表包含指向可用于安装混合 Runbook 辅助角色的不同方法的链接。 若要安装和配置 Windows 混合 Runbook 辅助角色，可以使用两种方法。 建议的方法是使用自动化 Runbook 来彻底实现配置 Windows 计算机所需的过程的自动化。 第二种方法使用分步过程来手动安装和配置角色。 对于 Linux 计算机，运行 Python 脚本以在计算机上安装代理

|操作系统  |部署类型  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[手动](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> 为了使用所需状态配置 (DSC) 管理支持混合 Runbook 辅助角色的服务器配置，需将其添加为 DSC 节点。 若要进一步了解如何载入它们以供 DSC 管理，请参阅[载入由 Azure 自动化 DSC 管理的计算机](automation-dsc-onboarding.md)。
>
>如果启用[更新管理解决方案](automation-update-management.md)，任何连接到 Log Analytics 工作区的计算机将自动配置为混合 Runbook 辅助角色，以支持此解决方案中包括的 Runbook。 但是，该计算机未注册到任何已在自动化帐户中定义的混合辅助角色组。 只要将同一个帐户同时用于解决方案和混合 Runbook 辅助角色组成员身份，即可将该计算机添加到自动化帐户的混合 Runbook 辅助角色组，以支持自动化 Runbook。 此功能已添加到 7.2.12024.0 版本的混合 Runbook 辅助角色。

开始部署混合 Runbook 辅助角色之前，请查看[有关规划网络的信息](#network-planning)。 成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

## <a name="removing-hybrid-runbook-worker"></a>删除混合 Runbook 辅助角色

可以从组中删除一个或多个混合 Runbook 辅助角色，或者根据要求删除该组。 若要从本地计算机中删除混合 Runbook 辅助角色，请执行以下步骤：

1. 在 Azure 门户中，导航到自动化帐户。
2. 在“设置”边栏选项卡中，选择“密钥”并记下“URL”和“主访问密钥”字段的值。 下一步需要用到此信息。

### <a name="windows"></a>Windows

在管理员模式下打开 PowerShell 会话，并运行以下命令。 可使用 **-Verbose** 开关获取删除过程的详细日志。

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

若要从混合辅助角色组中删除过时的计算机，请使用可选的 `machineName` 参数。

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> 这不会从计算机中删除 Microsoft 监视代理，而只会删除混合 Runbook 辅助角色的功能和配置。

## <a name="remove-hybrid-worker-groups"></a>删除混合辅助角色组

要删除某个组，首先需要使用前面所示的过程，从每台计算机中删除属于该组的混合 Runbook 辅助角色，然后执行以下步骤删除该组。

1. 在 Azure 门户中打开自动化帐户。
1. 在“流程自动化”下选择“混合辅助角色组”。 选择要删除的组。 在选择特定的组之后，会显示“混合辅助角色组”属性页面。

   ![混合 Runbook 辅助角色组页面](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. 在所选组的属性页面中，单击“删除”。 此时会显示一条消息请求确认此操作，如果确定要继续，请选择“是”。

   ![确认删除组对话框](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度。

## <a name="network-planning"></a>配置网络

### <a name="hybrid-worker-role"></a>混合辅助角色

要使混合 Runbook 辅助角色连接并注册到 Log Analytics，必须让其有权访问此部分所述的端口号和 URL。 除了这些端口和 URL 以外，还需要有权访问 [Microsoft Monitoring Agent 连接到 Log Analytics 时要使用的端口和 URL](../log-analytics/log-analytics-agent-windows.md)。

如果使用代理服务器在代理与 Log Analytics 服务之间通信，请确保能够访问相应的资源。 如果使用防火墙来限制对 Internet 的访问，则必须将防火墙配置为允许访问。

混合 Runbook 辅助角色与自动化通信时需要以下端口和 URL：

* 端口：只需使用 TCP 443 进行出站 Internet 访问。
* 全局 URL：*.azure-automation.net
* 美国弗吉尼亚州政府的全局 URL：*.azure-automation.us
* 代理服务：https://\<workspaceId\>.agentsvc.azure-automation.net

如果为特定的区域定义了自动化帐户，则可以限制与该区域数据中心之间的通信。 下表提供了每个区域的 DNS 记录。

| **区域** | **DNS 记录** |
| --- | --- |
| 美国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 美国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 美国东部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 加拿大中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 欧洲西部 |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北欧 |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 东南亚 |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 印度中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 日本东部 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 澳大利亚东南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| 美国政府弗吉尼亚州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

有关区域 IP 地址列表（非区域名称列表），请从 Microsoft 下载中心下载 [Azure 数据中心 IP 地址](https://www.microsoft.com/download/details.aspx?id=41653) XML 文件。

> [!NOTE]
> Azure 数据中心 IP 地址 XML 文件列出了 Microsoft Azure 数据中心使用的 IP 地址范围。 文件中包含计算、SQL 和存储范围。
>
>每周都将发布更新的文件。 该文件反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。
>
> 建议每周下载新的 XML 文件。 然后，更新网站以正确地标识 Azure 中运行的服务。 Azure ExpressRoute 用户应注意，此文件过去经常在每个月的第一周更新 Azure 空间的边界网关协议 (BGP) 播发。

### <a name="update-management"></a>更新管理

除了混合 Runbook 辅助角色所需的标准地址和端口以外，更新管理还特别需要以下地址。 与这些地址的通信通过端口 443 完成。

|Azure Public  |Azure Government   |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|* .blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>故障排除

混合 Runbook 辅助角色依靠代理与自动化帐户通信，以注册辅助角色、接收 Runbook 作业和报告状态。 对于 Windows，此代理为 Microsoft Monitoring Agent。 对于 Linux，此代理为适用于 Linux 的 OMS 代理。 如果辅助角色注册失败，以下是一些可能导致此错误的原因：

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>混合辅助角色在代理或防火墙后面

确保计算机在端口 443 上对 *.azure-automation.net 有出站访问权限。

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>运行混合辅助角色的计算机不满足最低硬件要求

运行混合 Runbook 辅助角色的计算机应满足最低硬件要求，才能指定它托管此功能。 否则，在执行过程中，根据其他后台进程的资源使用率和 runbook 所导致的争用，该计算机将变为过度使用，从而导致 runbook 作业延迟或超时。

确认指定为运行混合 Runbook 辅助角色功能的计算机满足最低硬件要求。 如果满足，请监视 CPU 和内存利用率，以确定混合 Runbook 辅助角色进程的性能和 Windows 之间的任何关联。 如果存在内存或 CPU 压力，这可能指示需要升级或添加额外的处理器或增加内存来解决资源瓶颈问题，从而解决此错误。 或者，选择其他可支持最低要求的计算资源，并在工作负荷需求指示需要增加时进行扩展。

有关对特定 OS 进行故障排除的其他信息，请参阅 [Linux 混合 Runbook 辅助角色](automation-linux-hrw-install.md#troubleshooting)或 [Windows 混合 Runbook 辅助角色](automation-windows-hrw-install.md#troubleshooting)

## <a name="next-steps"></a>后续步骤

查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。
