---
title: 打开 Microsoft Azure 自动化案例时要收集的数据*微软文档
description: 本文介绍了在使用 Microsoft Azure 支持为 Azure 自动化打开案例之前应收集的一些基本信息。
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
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849371"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>为 Microsoft Azure 自动化建立案例时要收集的数据

本文介绍了在使用 Microsoft Azure 支持为 Azure 自动化打开案例之前应收集的一些基本信息。 打开案例不需要此信息。 但是，它可以帮助 Microsoft 更快地解决您的问题。 此外，在打开案例后，支持工程师可能会要求您提供此数据。

## <a name="collect-more-information"></a>收集更多信息

在为 Microsoft Azure 自动化支持打开案例之前，我们建议您收集以下信息。

### <a name="basic-data-collection"></a>基本数据收集

收集以下知识库文章中描述的数据：

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)如何捕获 Azure 自动化脚本诊断

## <a name="collect-data-depending-on-issue"></a>根据问题收集数据
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>对于影响 Linux 上更新管理的问题

1. 除了 KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项外，请运行以下日志收集工具：

   [OMS Linux 代理日志收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 压缩以下文件夹的内容，然后将压缩文件发送到 Azure 支持：

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. 验证 OMS Linux 代理报告的工作区 ID 是否与正在监视更新的工作区相同。

### <a name="for-issues-that-affect-update-management-on-windows"></a>对于影响 Windows 上更新管理的问题

除了[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项外，执行以下操作：

1. 将以下事件日志导出为 EVTX 格式：

   * System
   * 应用程序
   * 安全性
   * Operations Manager
   * 微软SMA/运营

2. 验证代理报告的工作区 ID 与 Windows 更新监视的工作区相同。

### <a name="for-issues-that-affect-a-job"></a>对于影响作业的问题

除了[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项目外，还收集以下信息：

1. 收集**JobID**编号（对于遇到问题的作业）：

   1. 在 Azure 门户，转到 **"自动化帐户"** 边栏选项卡。
   2. 选择要排除故障的**自动化帐户**。
   3. 选择**作业**。
   4. 选择要排除故障的作业。
   5. 在**作业摘要**下，查找**作业 ID** （GUID）。

   ![作业摘要窗格中的作业 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. 收集帐户名称：

   1. 在 Azure 门户，转到 **"自动化帐户"** 边栏选项卡。
   2. 获取正在故障排除的**自动化帐户**的名称。

3. 收集正在运行的脚本的示例。

4. 收集日志文件：

   1. 在 Azure 门户，转到 **"自动化帐户"** 边栏选项卡。
   2. 选择要排除故障的**自动化帐户**。
   3. 选择**作业**。
   4. 选择要排除故障的作业。
   5. 选择**所有日志**。
   6. 在生成的边栏选项卡上，收集数据。

   !["所有日志"下列出的数据](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>对于影响模块的问题

除了"基本数据收集"项下的项目外，还收集以下信息：

* 您所遵循的步骤，以便重现问题。
* 任何错误消息的屏幕截图。
* 当前模块及其版本号的屏幕截图。

## <a name="next-steps"></a>后续步骤

如果本文中的任何一点都需要更多帮助，请与[MSDN Azure 和堆栈溢出论坛上的](https://azure.microsoft.com/support/forums/)Azure 专家联系。

或者，提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
