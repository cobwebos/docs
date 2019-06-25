---
title: Azure 状态监视器 v2 API 参考：获取配置 |Microsoft Docs
description: 状态监视器 v2 API 引用。 Get-ApplicationInsightsMonitoringConfig. 无需重新部署网站即可监视网站性能。 适用于 ASP.NET web 应用托管在本地，在虚拟机，或在 Azure 上。
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 1f7061c9823ddc8ff7f8f42976041f1c9ff68fc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514387"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>状态监视器 v2 API：Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

本文介绍的 cmdlet 时的成员[Az.ApplicationMonitor PowerShell 模块](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)。

> [!IMPORTANT]
> 状态监视器 v2 目前为公共预览版。
> 没有附带服务级别协议，提供此预览版本，我们不建议将其用于生产工作负荷。 可能不支持某些功能，以及一些可能会受约束的功能。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="description"></a>描述

获取配置文件，并将值输出到控制台。

> [!IMPORTANT] 
> 此 cmdlet 需要具有管理员权限的 PowerShell 会话。

## <a name="examples"></a>示例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>parameters

没有所需的参数。

## <a name="output"></a>输出


#### <a name="example-output-from-reading-the-config-file"></a>读取配置文件的示例输出

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>后续步骤

  查看遥测：
 - [浏览指标](../../azure-monitor/app/metrics-explorer.md)监视性能和使用情况。
- [搜索事件和日志](../../azure-monitor/app/diagnostic-search.md)来诊断问题。
- 使用[analytics](../../azure-monitor/app/analytics.md)获取更多高级查询。
- [创建仪表板](../../azure-monitor/app/overview-dashboard.md)。
 
 添加更多遥测：
 - [创建 Web 测试](monitor-web-app-availability.md)，以确保站点保持活动状态。
- [添加 web 客户端遥测](../../azure-monitor/app/javascript.md)，查看网页代码中的异常，并启用跟踪调用。
- [将 Application Insights SDK 添加到你的代码](../../azure-monitor/app/asp-net.md)以便插入跟踪和记录调用。
 
 使用状态监视器 v2 执行更多操作：
 - 使用指南，了解如何[进行故障排除](status-monitor-v2-troubleshoot.md)状态监视器 v2。
 - 通过使用对配置进行更改[设置配置](status-monitor-v2-api-set-config.md)cmdlet。
