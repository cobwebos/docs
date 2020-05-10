---
title: 排查 Azure Automation 中的 Linux update 代理问题更新管理
description: 了解如何使用更新管理解决方案排查和解决 Linux Windows update 代理的问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: a4082ddfd8c092a6f9223a0894f21bc734b6efb6
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997016"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>排查 Linux 更新代理问题

在更新管理中，计算机未显示为 "就绪" （正常）的原因有很多。 可以检查 Linux 混合 Runbook 辅助角色代理的运行状况，以确定基本问题。 下面是计算机的三个就绪状态：

* 准备就绪：混合 Runbook 辅助角色已部署，最后发现不到1小时前。
* 已断开连接：混合 Runbook 辅助角色已部署，上次在一小时前查看。
* 未配置：混合 Runbook 辅助角色找不到或未完成加入。

> [!NOTE]
> 在 Azure 门户显示的内容和计算机的当前状态之间可能会有轻微的延迟。

本文介绍如何在[脱机方案](#troubleshoot-offline)中从 Azure 门户和非 Azure 计算机运行 Azure 计算机的疑难解答。 

> [!NOTE]
> 如果配置了一个代理服务器，则疑难解答脚本当前不会通过代理服务器路由流量。

## <a name="start-the-troubleshooter"></a>启动“故障排除”

对于 Azure 计算机，请在门户中的 "**更新代理就绪**" 列下选择 "**故障排除**" 链接，以打开 "更新代理故障排除" 页。 对于非 Azure 计算机，此链接会将你带入本文。 若要对非 Azure 计算机进行故障排除，请参阅 "脱机疑难解答" 部分中的说明。

![VM 列表页](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> 检查要求 VM 处于运行状态。 如果 VM 未运行，将显示 **"启动 vm"** 。

在 "更新代理故障排除" 页上，选择 "**运行检查**" 以启动疑难解答。 疑难解答使用[运行命令](../../virtual-machines/linux/run-command.md)在计算机上运行脚本，以验证依赖关系。 完成故障排除时，它会返回检查的结果。

![故障排除页](../media/update-agent-issues-linux/troubleshoot-page.png)

完成检查后，结果将返回到窗口中。 检查部分提供了每项检查所要查找的内容相关信息。

![更新代理检查页](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>先决条件检查

### <a name="operating-system"></a>操作系统

操作系统检查会验证混合 Runbook 辅助角色是否正在运行以下操作系统之一。

|操作系统  |注释  |
|---------|---------|
|CentOS 6 (x86/x64) 和 7 (x64)      | Linux 代理必须具有访问更新存储库的权限。 基于分类的修补需要使用 "yum" 来返回安全数据，CentOS 不能使用这些数据。         |
|Red Hat Enterprise 6 (x86/x64) 和 7 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|SUSE Linux Enterprise Server 11 (x86/x64) 和 12 (x64)     | Linux 代理必须具有访问更新存储库的权限。        |
|Ubuntu 14.04 LTS、16.04 LTS 和 18.04 LTS (x86/x64)      |Linux 代理必须具有访问更新存储库的权限。         |

## <a name="monitoring-agent-service-health-checks"></a>监视代理服务运行状况检查

### <a name="log-analytics-agent"></a>Log Analytics 代理

此检查可确保安装适用于 Linux 的 Log Analytics 代理。 有关如何安装的说明，请参阅[安装适用于 Linux 的代理](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux)。

### <a name="log-analytics-agent-status"></a>代理状态 Log Analytics

此检查可确保适用于 Linux 的 Log Analytics 代理正在运行。 如果该代理未在运行，则可以运行以下命令尝试重启该代理。 有关对代理进行故障排除的详细信息，请参阅[Linux 混合 Runbook 辅助角色故障排除](hybrid-runbook-worker.md#linux)。

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>多宿主

此检查可确定代理是否向多个工作区报告。 更新管理不支持多宿主。

### <a name="hybrid-runbook-worker"></a>混合 Runbook 辅助角色

此检查将验证适用于 Linux 的 Log Analytics 代理是否具有混合 Runbook 辅助角色包。 更新管理需要此包才能工作。 若要了解详细信息，请参阅[适用于 Linux 的 Log Analytics 代理未运行](hybrid-runbook-worker.md#oms-agent-not-running)。

更新管理从操作终结点下载混合 Runbook 辅助角色包。 因此，如果混合 Runbook 辅助角色未运行并且[操作终结点](#operations-endpoint)失败，则更新可能会失败。

### <a name="hybrid-runbook-worker-status"></a>混合 Runbook 辅助角色状态

此检查可确保混合 Runbook 辅助角色在计算机上运行。 如果混合 Runbook 辅助角色运行正常，则应出现以下示例中的进程。


```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>连接性检查

### <a name="general-internet-connectivity"></a>一般 Internet 连接

此检查可确保计算机拥有 Internet 访问权限。

### <a name="registration-endpoint"></a>注册终结点

此检查确定混合 Runbook 辅助角色能否与 Log Analytics 工作区中的 Azure 自动化进行正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与注册终结点通信。 有关要打开的地址和端口的列表，请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="operations-endpoint"></a>操作终结点

此检查确定 Log Analytics 代理能否与作业运行时数据服务正确通信。

代理和防火墙配置必须允许混合 Runbook 辅助角色代理与作业运行时数据服务通信。 有关要打开的地址和端口的列表，请参阅[网络规划](../automation-hybrid-runbook-worker.md#network-planning)。

### <a name="log-analytics-endpoint-1"></a>Log Analytics 终结点 1

此检查会验证计算机是否可以访问 Log Analytics 代理所需的终结点。

### <a name="log-analytics-endpoint-2"></a>Log Analytics 终结点 2

此检查会验证计算机是否可以访问 Log Analytics 代理所需的终结点。

### <a name="log-analytics-endpoint-3"></a>Log Analytics 终结点 3

此检查会验证计算机是否可以访问 Log Analytics 代理所需的终结点。

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>脱机使用故障排除

可以通过在本地运行脚本，在混合 Runbook 辅助角色上脱机使用故障排除。 可以在脚本中心中找到 Python 脚本（ [update_mgmt_health_check py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6)）。 以下示例显示了此脚本的输出示例：

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>后续步骤

若要排查混合 Runbook 辅助角色的其他问题，请参阅[对混合 Runbook 辅助角色进行故障排除](hybrid-runbook-worker.md)。
