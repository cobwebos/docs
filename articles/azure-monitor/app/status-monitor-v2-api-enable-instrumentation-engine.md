---
title: Azure 应用程序 Insights 代理 API 参考：启用检测引擎 |Microsoft Docs
description: Application Insights 代理 API 参考。 InstrumentationEngine。 监视网站性能而不重新部署网站。 适用于托管在本地、Vm 或 Azure 上的 ASP.NET web 应用。
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 9958121c38b96dc9cfa4dda2812fa9ce2b18d785
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388286"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Application Insights 代理 API： InstrumentationEngine

本文介绍了一个 cmdlet，它是[ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的成员。

## <a name="description"></a>描述

通过设置某些注册表项来启用检测引擎。
重新启动 IIS 以使更改生效。

检测引擎可以补充 .NET Sdk 收集的数据。
它收集用于描述托管进程执行情况的事件和消息。 这些事件和消息包括依赖项结果代码、HTTP 谓词和[SQL 命令文本](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)。

启用检测引擎，如果：
- 已使用 Enable cmdlet 启用监视，但未启用检测引擎。
- 已通过 .NET Sdk 手动检测你的应用，并想要收集更多遥测数据。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。

> [!NOTE] 
> - 此 cmdlet 需要你查看并接受我们的许可和隐私声明。
> - 检测引擎增加了额外的开销，并在默认情况下处于关闭状态。

## <a name="examples"></a>示例

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>parameters

### <a name="-acceptlicense"></a>-AcceptLicense
**可选。** 使用此开关在无外设安装中接受许可和隐私声明。

### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关输出详细日志。

## <a name="output"></a>输出


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>成功启用检测引擎的示例输出

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>后续步骤

  查看遥测：
 - [探索指标](../../azure-monitor/app/metrics-explorer.md)来监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
- 将[分析](../../azure-monitor/app/analytics.md)用于更高级的查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。
 
 添加更多遥测：
 - [创建 web 测试](monitor-web-app-availability.md)，确保网站保持活动。
- [添加 web 客户端遥测](../../azure-monitor/app/javascript.md)，查看网页代码中的异常并启用跟踪调用。
- [将 APPLICATION INSIGHTS SDK 添加到你的代码](../../azure-monitor/app/asp-net.md)中，以便可以插入跟踪和日志调用。
 
 Application Insights 代理中执行更多操作：
 - 使用本指南来对 Application Insights 代理[进行故障排除](status-monitor-v2-troubleshoot.md)。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认正确地记录了设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
