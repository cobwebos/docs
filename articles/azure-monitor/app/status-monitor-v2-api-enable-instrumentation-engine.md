---
title: Azure Application Insights 代理 API 参考
description: Application Insights 代理 API 参考。 Enable-InstrumentationEngine。 无需重新部署网站即可监视网站性能。 使用托管在本地、VM 或 Azure 上的 ASP.NET Web 应用。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671345"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>应用程序见解代理 API：启用检测引擎

本文介绍属于 [Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)的 cmdlet。

## <a name="description"></a>描述

通过设置一些注册表项启用检测引擎。
重启 IIS 以使这些更改生效。

检测引擎可以补充 .NET SDK 收集的数据。
它收集描述托管进程执行的事件和消息。 这些事件和消息包括依赖项结果代码、HTTP 谓词和[SQL 命令文本](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query)。

在以下情况下启用检测引擎：
- 已使用 Enable cmdlet 启用了监视，但未启用检测引擎。
- 已使用 .NET SDK 手动检测应用，并希望收集其他遥测数据。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。

> [!NOTE] 
> - 此 cmdlet 要求查看并接受我们的许可条款和隐私声明。
> - 检测引擎会增加额外的开销，默认情况下处于关闭状态。

## <a name="examples"></a>示例

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>参数

### <a name="-acceptlicense"></a>-AcceptLicense
**选。** 使用此开关可在无外设安装中接受许可条款和隐私声明。

### <a name="-verbose"></a>-Verbose
**通用参数。** 使用此开关输出详细的日志。

## <a name="output"></a>输出


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>成功启用检测引擎的示例输出

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>后续步骤

  查看遥测：
 - [浏览指标](../../azure-monitor/app/metrics-explorer.md)以监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)以诊断问题。
- 使用[分析](../../azure-monitor/app/analytics.md)，以便进行更高级的查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。
 
 添加更多遥测：
 - [创建 Web 测试](monitor-web-app-availability.md)，确保站点保持活动状态。
- [添加 Web 客户端遥测](../../azure-monitor/app/javascript.md)，以查看网页代码中的异常并启用跟踪调用。
- [将 Application Insights SDK 添加到代码](../../azure-monitor/app/asp-net.md)，以便插入跟踪和日志调用。
 
 使用 Application Insights 代理执行更多操作：
 - 使用我们的指南对 Application Insights 代理进行[故障排除](status-monitor-v2-troubleshoot.md)。
 - [获取配置](status-monitor-v2-api-get-config.md)以确认是否正确记录了你的设置。
 - [获取状态](status-monitor-v2-api-get-status.md)以检查监视。
