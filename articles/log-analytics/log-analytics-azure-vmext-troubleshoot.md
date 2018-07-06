---
title: Azure Log Analytics VM 扩展故障排除 | Microsoft Docs
description: 针对 Windows 和 Linux Azure VM 的 Log Analytics VM 扩展的最常见问题，描述症状、原因和解决方法。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 700d6b2c3bcd39aed38bf75556bcdcb59d1ab78b
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128803"
---
# <a name="troubleshooting-the-log-analytics-vm-extension"></a>Log Analytics VM 扩展故障排除
本文可帮助排查使用 Microsoft Azure 上运行的 Windows 和 Linux 虚拟机的 Log Analytics VM 扩展时可能遇到的错误，并建议解决这些问题可能的解决方案。

若要验证扩展的状态，请从 Azure 门户执行以下步骤。

1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入“虚拟机”。 开始键入时，会根据输入筛选该列表。 选择“虚拟机”。
3. 在虚拟机列表中，找到并选择该虚拟机。
3. 在虚拟机上，单击“扩展”。
4. 从列表中，查看是否已启用 Log Analytics 扩展。  对于 Linux，代理列出为 **OMSAgentforLinux**；对于 Windows，代理列出为 **MicrosoftMonitoringAgent**。

   ![VM 扩展视图](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. 单击要查看详细信息的扩展。 

   ![VM 扩展详细信息](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM 扩展故障排除

如果 *Microsoft Monitoring Agent* VM 扩展未安装或未报告，可以执行以下步骤来解决此问题。

1. 使用 [KB 2965986](https://support.microsoft.com/kb/2965986#mt1) 中的步骤检查是否已安装 Azure VM 代理或者其是否正常工作。
   * 还可以查看 VM 代理日志文件 `C:\WindowsAzure\logs\WaAppAgent.log`
   * 如果此日志不存在，则未安装 VM 代理。
   * [安装 Azure VM 代理](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 使用以下步骤确认 Microsoft Monitoring Agent 扩展检测信号任务正在运行：
   * 登录到虚拟机
   * 打开任务计划程序并找到 `update_azureoperationalinsight_agent_heartbeat` 任务
   * 确认任务已启用并且一直在运行
   * 查看 `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log` 中的检测信号日志文件
3. 查看 `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent` 中的 Microsoft Monitoring Agent VM 扩展日志文件
4. 确保虚拟机可以运行 PowerShell 脚本
5. 确保 C:\Windows\temp 上的权限未被更改
6. 通过在虚拟机上的 PowerShell 特权窗口中键入以下内容来查看 Microsoft Monitoring Agent 的状态：`  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. 查看 `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs` 中的 Microsoft Monitoring Agent 安装日志文件

有关详细信息，请参阅 [Windows 扩展故障排除](../virtual-machines/windows/extensions-oms.md)。

## <a name="troubleshooting-linux-vm-extension"></a>Linux VM 扩展故障排除
如果*适用于 Linux 的 OMS 代理* VM 扩展未安装或未报告，可以执行以下步骤来解决此问题。

1. 如果扩展状态为“未知”，则查看 VM 代理日志文件 `/var/log/waagent.log`，检查 Azure VM 代理是否已安装且可正常工作
   * 如果此日志不存在，则未安装 VM 代理。
   * [在 Linux VM 上安装 Azure VM 代理](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. 对于其他不正常状态，请查看 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` 和 `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log` 中的 OMS Agent for Linux VM 扩展日志文件
3. 如果扩展状态正常，但是未上载数据，则查看 `/var/opt/microsoft/omsagent/log/omsagent.log` 中的 OMS Agent for Linux 日志文件

有关详细信息，请参阅 [Linux 扩展故障排除](../virtual-machines/linux/extensions-oms.md)。

## <a name="next-steps"></a>后续步骤

有关与适用于托管在 Azure 外部计算机上 Linux 的 OMS 代理相关的其他故障排除指南，请参阅 [Azure Log Analytics Linux 代理故障排除](log-analytics-agent-linux-support.md)。  
