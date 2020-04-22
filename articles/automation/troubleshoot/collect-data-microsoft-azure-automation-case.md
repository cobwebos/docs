---
title: 打开 Microsoft Azure 自动化案例时要收集的数据*微软文档
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679398"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>为 Microsoft Azure 自动化建立案例时要收集的数据

本文介绍了在使用 Microsoft Azure 支持为 Azure 自动化打开案例之前应收集的一些信息。 打开案例不需要此信息。 但是，它可以帮助 Microsoft 更快地解决您的问题。 此外，在打开案例后，支持工程师可能会要求您提供此数据。

## <a name="basic-data"></a>基本数据

收集知识库文章[4034605 中描述的基本数据 - 如何捕获 Azure 自动化脚本诊断](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)。

## <a name="data-for-update-management-issues-on-linux"></a>Linux 上更新管理问题的数据

1. 除了 KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项外，请运行以下日志收集工具：

   [OMS Linux 代理日志收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 压缩 **/var/opt/microsoft/omsagent/运行/自动化辅助/** 文件夹的内容，然后将压缩文件发送到 Azure 支持。
 
3. 验证 Linux 日志分析代理报告的工作区的 ID 是否与受监视的工作区的 ID 相同。

## <a name="data-for-update-management-issues-on-windows"></a>用于 Windows 上更新管理问题的数据

1. 收集[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项目的数据。

2. 将以下事件日志导出为 EVTX 格式：

   * 系统
   * 应用程序
   * 安全性
   * Operations Manager
   * 微软SMA/运营

3. 验证代理报告的工作区的 ID 是否与 Windows 更新监视的工作区的 ID 相同。

## <a name="data-for-job-issues"></a>作业问题的数据

1. 收集[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项目的数据。

2. 收集出现问题的作业的作业 ID：

   1. 在 Azure 门户中，转到**自动化帐户**。
   2. 选择要排除故障的自动化帐户，并记下名称。
   3. 选择**作业**。
   4. 选择要排除故障的作业。
   5. 在"作业摘要"窗格中，在**作业 ID**中查找 GUID 值。

   ![作业摘要窗格中的作业 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. 收集正在运行的脚本的示例。

4. 收集日志文件：

   1. 在 Azure 门户中，转到**自动化帐户**。
   2. 选择要排除故障的自动化帐户。
   3. 选择**作业**。
   4. 选择要排除故障的作业。
   5. 选择**所有日志**。
   6. 在生成的窗格中，收集数据。

   !["所有日志"下列出的数据](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>模块问题的数据

除了[基本数据项](#basic-data)外，还收集以下信息：

* 您遵循的步骤，以便可以重现问题。
* 任何错误消息的屏幕截图。
* 当前模块及其版本号的屏幕截图。

## <a name="next-steps"></a>后续步骤

如果您需要更多帮助：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)连接 ，官方 Microsoft Azure 帐户通过将 Azure 社区连接到正确的资源（答案、支持和专家）来改善客户体验。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
