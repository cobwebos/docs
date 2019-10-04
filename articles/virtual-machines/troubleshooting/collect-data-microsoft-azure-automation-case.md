---
title: 为 Microsoft Azure 自动化打开案例时要收集的数据 |Microsoft Docs
description: 本文介绍了在使用 Microsoft Azure 支持打开 Azure 自动化的情况之前应收集的一些基本信息。
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
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846675"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>为 Microsoft Azure 自动化打开案例时要收集的数据

本文介绍了在使用 Microsoft Azure 支持打开 Azure 自动化的情况之前应收集的一些基本信息。 此信息不是打开事例所必需的。 不过，它可以帮助 Microsoft 更快速地解决问题。 此外，在打开案例后，支持工程师可能会要求提供此数据。

## <a name="collect-more-information"></a>收集详细信息

在为 Microsoft Azure 自动化支持开启案例之前，建议收集以下信息。

### <a name="basic-data-collection"></a>基本数据收集

收集以下知识库文章中描述的数据：

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)如何捕获 Azure 自动化-脚本诊断

## <a name="collect-data-depending-on-issue"></a>根据问题收集数据
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>对于影响 Linux 更新管理的问题

1. 除了 KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项外，还可运行以下日志收集工具：

   [OMS Linux 代理日志收集器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. 压缩以下文件夹的内容，然后将压缩文件发送到 Azure 支持：

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. 验证 OMS Linux 代理报告到的工作区 ID 是否与要监视其更新的工作区相同。

### <a name="for-issues-that-affect-update-management-on-windows"></a>对于影响 Windows 更新管理的问题

除了[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项，请执行以下操作：

1. 将以下事件日志导出为 .EVTX 格式：

   * 系统
   * 应用程序
   * 安全性
   * Operations Manager
   * Microsoft-SMA/操作

2. 验证代理报告到的工作区 ID 是否与 Windows 更新所监视的工作区相同。

### <a name="for-issues-that-affect-a-job"></a>对于影响作业的问题

除了[4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)中列出的项，收集以下信息：

1. 收集**JobID**编号（适用于遇到问题的作业）：

   1. 在 Azure 门户上，请参阅 "**自动化帐户**" 边栏选项卡。
   2. 选择要进行故障排除的**自动化帐户**。
   3. 选择 "**作业**"。
   4. 选择要进行故障排除的作业。
   5. 在 "**作业摘要**" 下，查找**作业 ID** （GUID）。

   ![作业摘要窗格内的作业 ID](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. 收集帐户名：

   1. 在 Azure 门户上，请参阅 "**自动化帐户**" 边栏选项卡。
   2. 获取要进行故障排除的**自动化帐户**的名称。

3. 收集正在运行的脚本的示例。

4. 收集日志文件：

   1. 在 Azure 门户上，请参阅 "**自动化帐户**" 边栏选项卡。
   2. 选择要进行故障排除的**自动化帐户**。
   3. 选择 "**作业**"。
   4. 选择要进行故障排除的作业。
   5. 选择 "**所有日志**"。
   6. 在生成的边栏选项卡上，收集数据。

   !["所有日志" 下列出的数据](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>对于影响模块的问题

除了 "基本数据收集" 下的项外，还需要收集以下信息：

* 你遵循的步骤，这样就可以重现该问题。
* 任何错误消息的屏幕截图。
* 当前模块及其版本号的屏幕截图。

## <a name="next-steps"></a>后续步骤

如果在本文的任何位置需要更多帮助，请联系 MSDN Azure 上的 Azure 专家[并 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)。

或者，提供 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。