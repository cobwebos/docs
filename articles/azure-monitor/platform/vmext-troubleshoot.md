---
title: 对 Azure Monitor 中的 Azure Log Analytics VM 扩展进行故障排除 |Microsoft Docs
description: 针对 Windows 和 Linux Azure VM 的 Log Analytics VM 扩展的最常见问题，描述症状、原因和解决方法。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: 40f0705cfa7f0e9bb45d300a629adebd0cc5be47
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883661"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Azure Monitor 中的 Log Analytics VM 扩展故障排除
本文可帮助排查使用 Microsoft Azure 上运行的 Windows 和 Linux 虚拟机的 Log Analytics VM 扩展时可能遇到的错误，并建议解决这些问题可能的解决方案。

若要验证扩展的状态，请从 Azure 门户执行以下步骤。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“虚拟机”。 开始键入时，会根据输入筛选该列表。 选择“虚拟机”。
3. 在虚拟机列表中，找到并选择该虚拟机。
3. 在虚拟机上，单击“扩展”。
4. 从列表中，查看是否已启用 Log Analytics 扩展。  对于 Linux，代理列出为 **OMSAgentforLinux**；对于 Windows，代理列出为 **MicrosoftMonitoringAgent**。

   ![VM 扩展视图](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. 单击要查看详细信息的扩展。 

   ![VM 扩展详细信息](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM 扩展故障排除

如果 *Microsoft Monitoring Agent* VM 扩展未安装或未报告，可以执行以下步骤来解决此问题。

1. 使用 [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) 中的步骤检查是否已安装 Azure VM 代理或者其是否正常工作。
   * 此外可以查看 VM 代理日志文件 `C:\WindowsAzure\logs\WaAppAgent.log`
   * 如果此日志不存在，则未安装 VM 代理。
   * [安装 Azure VM 代理](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 使用以下步骤确认 Microsoft Monitoring Agent 扩展检测信号任务正在运行：
   * 登录到虚拟机
   * 打开任务计划程序并找到 `update_azureoperationalinsight_agent_heartbeat` 任务
   * 确认任务已启用并且一直在运行
   * 检查检测信号日志文件 `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. 查看中的 Microsoft 监视代理 VM 扩展日志文件 `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. 确保虚拟机可以运行 PowerShell 脚本
5. 确保 C:\Windows\temp 上的权限未被更改
6. 通过键入以下命令在虚拟机上提升的 PowerShell 窗口中查看 Microsoft Monitoring Agent 的状态 `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. 查看 Microsoft Monitoring Agent 安装程序日志文件中 `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

有关详细信息，请参阅 [Windows 扩展故障排除](../../virtual-machines/extensions/oms-windows.md)。

## <a name="troubleshooting-linux-vm-extension"></a>Linux VM 扩展故障排除
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
如果 Log Analytics Linux 代理 VM 扩展未安装或未报告，可以执行以下步骤来解决此问题。

1. 如果扩展状态为*未知*检查是否已安装 Azure VM 代理且可通过查看 VM 代理日志文件正常工作 `/var/log/waagent.log`
   * 如果此日志不存在，则未安装 VM 代理。
   * [在 Linux Vm 上安装 Azure VM 代理](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 有关其他不正常状态，查看 Log Analytics 代理的 Linux VM 扩展日志文件`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log`和 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. 如果扩展状态正常运行，但未上传数据查看适用于 Linux 日志文件中的 Log Analytics 代理 `/var/opt/microsoft/omsagent/log/omsagent.log`

有关详细信息，请参阅 [Linux 扩展故障排除](../../virtual-machines/extensions/oms-linux.md)。

## <a name="next-steps"></a>后续步骤

有关与托管在 Azure 外部计算机上的 Log Analytics Linux 代理相关的其他故障排除指南，请参阅 [Azure Log Analytics Linux 代理故障排除](agent-linux-troubleshoot.md)。  
