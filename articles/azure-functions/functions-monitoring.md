---
title: "监视 Azure Functions | Microsoft Docs"
description: "了解如何监视 Azure Functions。"
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, Functions, 事件处理, webhook, 动态计算, 无服务体系结构"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2016
ms.author: wesmc
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 278fd4a811afac240b6ad3c2eb7f0a23cb686382
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---

# <a name="monitoring-azure-functions"></a>监视 Azure Functions

## <a name="overview"></a>概述 


每个函数的“监视器”选项卡允许查看函数的每次执行。

![Azure Functions 监视器选项卡](./media/functions-monitoring/monitor-tab.png) 

单击某个执行可查看持续时间、输入数据、错误和关联的日志文件。 这有助于对函数进行调试和性能优化。

> [!IMPORTANT]
> 对 Azure Functions 使用[消耗托管计划](functions-overview.md#pricing)时，Function App 中的“监视”磁贴不显示任何数据。 这是因为平台动态地缩放和管理计算实例。 这些指标在消耗计划上没有意义。 若要监视 Function App 的使用情况，应改为使用本文中的指南。
> 
> 以下屏幕截图显示了一个示例：
> 
> ![监视函数](./media/functions-monitoring/app-service-overview-monitoring.png)


## <a name="real-time-monitoring"></a>实时监视

实时监视可通过单击“实时事件流”实现，如下所示。 

![监视器选项卡的实时事件流选项](./media/functions-monitoring/monitor-tab-live-event-stream.png)

实时事件流在浏览器的新选项卡的图中显示，如以下示例： 

![实时事件流示例](./media/functions-monitoring/live-event-stream.png)


> [!NOTE]
> 存在一个已知问题，可能会导致数据无法进行填充。 如果遇到这种情况，可能需要关闭包含实时事件流的浏览器选项卡，并再次单击“实时事件流”，让它可以正确填充事件流数据。 

实时事件流将函数的以下统计信息绘入图表：

* 每秒启动的执行数
* 每秒完成的执行数
* 每秒失败的执行数
* 平均执行时间（以毫秒为单位）。

这些统计信息是实时的，但执行数据的实际作图可能会有约 10 秒钟的延迟。


## <a name="monitoring-log-files-from-a-command-line"></a>从命令行监视日志文件

可在本地工作站上使用 Azure 命令行接口 (CLI) 1.0 或 PowerShell 将日志文件流式传输到命令行会话。

### <a name="monitoring-function-app-log-files-with-the-azure-cli-10"></a>使用 Azure CLI 1.0 监视 Function App 日志文件

若要开始，请[安装 Azure CLI 1.0](../cli-install-nodejs.md)

使用以下命令或[从 Azure CLI 1.0 登录到 Azure](../xplat-cli-connect.md) 中涵盖的任何其他选项登录 Azure 帐户。

    azure login

使用以下命令在经典服务管理模式中启用 Azure CLI 1.0：

    azure config mode asm

如果拥有多个订阅，则使用以下命令列出订阅，并将当前订阅设置为包含 Function App 的订阅。

    azure account list
    azure account set <subscriptionNameOrId>

以下命令会将 Function App 的日志文件流式传输到命令行：

    azure site log tail -v <function app name>

### <a name="monitoring-function-app-log-files-with-powershell"></a>使用 PowerShell 监视 Function App 日志文件

若要开始，请[安装和配置 Azure PowerShell](/powershell/azure/overview)。

通过运行以下命令添加 Azure 帐户：

    PS C:\> Add-AzureAccount

如果拥有多个订阅，可通过以下命令按名称列出它们，查看当前基于 `IsCurrent` 属性选择的订阅是否是正确的订阅：

    PS C:\> Get-AzureSubscription

如果需要将活动订阅设置为包含 Function App 的订阅，则使用以下命令：

    PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription

使用以下命令将日志流式传输到 PowerShell 会话：

    PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail

有关详细信息，请参阅[如何：流式传输 Web 应用的日志](../app-service/web-sites-enable-diagnostic-log.md#streamlogs)。 

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅以下资源：

* [测试函数](functions-test-a-function.md)
* [扩展函数](functions-scale.md)


