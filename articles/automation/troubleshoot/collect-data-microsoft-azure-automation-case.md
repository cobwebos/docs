---
title: 为 Microsoft Azure 自动化打开案例时要收集的数据 |Microsoft Docs
description: 本文介绍了在使用 Microsoft Azure 支持为 Azure 自动化打开案例之前应收集的一些信息。
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
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679398"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>为 Microsoft Azure 自动化建立案例时要收集的数据

本文介绍了在使用 Microsoft Azure 支持为 Azure 自动化打开案例之前应收集的一些信息。 此信息不是打开事例所必需的。 不过，它可以帮助 Microsoft 更快速地解决问题。 此外，在打开案例后，支持工程师可能会要求提供此数据。

## <a name="basic-data"></a>基本数据

收集知识库文章4034605中所述的基本数据[-如何捕获 Azure 自动化-脚本诊断](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)。

## <a name="data-for-update-management-issues-on-linux"></a>Linux 上的更新管理问题的数据

1. 除了 KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项外，还可运行以下日志收集工具：

   [OMS Linux 代理日志收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 压缩 **/var/opt/microsoft/omsagent/run/automationworker/** 文件夹的内容，然后将压缩文件发送到 Azure 支持。
 
3. 验证用于 Linux 的 Log Analytics 代理报表的工作区的 ID 与要监视其更新的工作区的 ID 相同。

## <a name="data-for-update-management-issues-on-windows"></a>Windows 上的更新管理问题的数据

1. 收集[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项的数据。

2. 将以下事件日志导出为 .EVTX 格式：

   * System (系统)
   * 应用程序
   * 安全性
   * Operations Manager
   * Microsoft-SMA/操作

3. 验证代理所报告的工作区的 ID 与 Windows 更新所监视的工作区的 ID 相同。

## <a name="data-for-job-issues"></a>作业问题的数据

1. 收集[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项的数据。

2. 收集有问题的作业的作业 ID：

   1. 在 Azure 门户中，请参阅**Automation 帐户**。
   2. 选择要进行故障排除的自动化帐户，并记下该名称。
   3. 选择 "**作业**"。
   4. 选择要进行故障排除的作业。
   5. 在 "作业摘要" 窗格中，查找 "**作业 ID**" 中的 GUID 值。

   ![作业摘要窗格内的作业 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 收集正在运行的脚本的示例。

4. 收集日志文件：

   1. 在 Azure 门户中，请参阅**Automation 帐户**。
   2. 选择要进行故障排除的自动化帐户。
   3. 选择 "**作业**"。
   4. 选择要进行故障排除的作业。
   5. 选择 "**所有日志**"。
   6. 在结果窗格中，收集数据。

   !["所有日志" 下列出的数据](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>模块问题的数据

除了[基本数据项](#basic-data)外，还需要收集以下信息：

* 已遵循的步骤，以便可以重现该问题。
* 任何错误消息的屏幕截图。
* 当前模块的屏幕截图及其版本号。

## <a name="next-steps"></a>后续步骤

如果需要更多帮助：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。
