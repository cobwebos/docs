---
title: 为 Microsoft Azure 自动化建立案例时要收集的数据 | Microsoft Docs
description: 本文介绍在使用 Microsoft Azure 支持为 Azure 自动化建立案例之前要收集的信息。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: d2f97d0b889186324aef9613847e3eddbfe1eb02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83684849"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>为 Microsoft Azure 自动化建立案例时要收集的数据

本文介绍在使用 Microsoft Azure 支持为 Azure 自动化建立案例之前应收集的一些信息。 此信息不是建立案例所必需的。 不过，它可以帮助 Microsoft 更快速地解决问题。 此外，在建立案例之后，支持工程师可能会要求你提供此数据。

## <a name="basic-data"></a>基本数据

收集知识库文章 [4034605 - 如何捕获 Azure 自动化脚本诊断](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中所述的基本数据。

## <a name="data-for-update-management-issues-on-linux"></a>有关 Linux 上的更新管理问题的数据

1. 除了知识库文章 [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) 中列出的各项之外，请运行以下日志收集工具：

   [OMS Linux 代理日志收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 压缩 /var/opt/microsoft/omsagent/run/automationworker/ 文件夹的内容，然后将压缩文件发送到 Azure 支持。
 
3. 验证 Linux 的 Log Analytics 代理对其进行报告的工作区的 ID 是否与要监视其更新的工作区的 ID 相同。

## <a name="data-for-update-management-issues-on-windows"></a>有关 Windows 上的更新管理问题的数据

1. 收集 [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) 中列出的各项的数据。

2. 将以下事件日志导出为 EVTX 格式：

   * 系统
   * 应用程序
   * 安全性
   * Operations Manager
   * Microsoft-SMA/Operational

3. 验证代理对其进行报告的工作区的 ID 是否与 Windows 更新所监视的工作区的 ID 相同。

## <a name="data-for-job-issues"></a>作业问题的数据

1. 收集 [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) 中列出的各项的数据。

2. 收集有问题的作业的作业 ID：

   1. 在 Azure 门户中，转到“自动化帐户”。
   2. 选择要进行故障排除的自动化帐户，然后记下名称。
   3. 选择“作业”。
   4. 选择要进行故障排除的作业。
   5. 在“作业摘要”窗格中，查找“作业 ID”中的 GUID 值。

   ![“作业摘要”窗格中的“作业 ID”](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 收集正在运行的脚本的示例。

4. 收集日志文件：

   1. 在 Azure 门户中，转到“自动化帐户”。
   2. 选择要进行故障排除的自动化帐户。
   3. 选择“作业”。
   4. 选择要进行故障排除的作业。
   5. 选择“所有日志”。
   6. 在生成的窗格中收集数据。

   ![“所有日志”下列出的数据](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>模块问题的数据

除了[基本数据项](#basic-data)外，还需要收集以下信息：

* 已执行的步骤，以便可以重现问题。
* 任何错误消息的屏幕截图。
* 当前模块及其版本号的屏幕截图。

## <a name="next-steps"></a>后续步骤

如果需要更多帮助：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
