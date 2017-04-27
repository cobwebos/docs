---
title: "Application Insights 使用的 IP 地址 | Microsoft 文档"
description: "Application Insights 所需的服务器防火墙例外"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 3f5419079baf0f557a14a355519e81f8ee7feb58
ms.lasthandoff: 03/31/2017


---
# <a name="ip-addresses-used-by-application-insights"></a>Application Insights 使用的 IP 地址
[Azure Application Insights](app-insights-overview.md) 服务使用许多 IP 地址。 如果要监视的应用托管在防火墙后面，你可能需要知道这些 IP 地址。

> [!NOTE]
> 尽管这些地址是静态的，但我们可能随时需要更改它们。
> 
> 

## <a name="outgoing-ports"></a>传出端口
需要在服务器防火墙中打开某些传出端口，允许 Application Insights SDK 和/或状态监视器将数据发送到门户：

| 目的 | 代码 | IP | 端口 |
| --- | --- | --- | --- |
| 遥测 |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221 |443 |
| 实时指标流 |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |

## <a name="status-monitor"></a>状态监视器
状态监视器配置 - 仅在进行更改时需要。

| 目的 | 代码 | IP | 端口 |
| --- | --- | --- | --- |
| 配置 |`management.core.windows.net` | |`443` |
| 配置 |`management.azure.com` | |`443` |
| 配置 |`login.windows.net` | |`443` |
| 配置 |`login.microsoftonline.com` | |`443` |
| 配置 |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| 配置 |`auth.gfx.ms` | |`443` |
| 配置 |`login.live.com` | |`443` |
| 安装 |`packages.nuget.org` | |`443` |

## <a name="hockeyapp"></a>HockeyApp
| 目的 | 代码 | IP | 端口 |
| --- | --- | --- | --- |
| 崩溃数据 |gate.hockeyapp.net |104.45.136.42 |80、443 |

## <a name="availability-tests"></a>可用性测试
这是用于运行[可用性 Web 测试](app-insights-monitor-web-app-availability.md)的地址列表。 如果想要对应用运行 Web 测试，但 Web 服务器局限于为特定的客户端提供服务，则必须允许来自可用性测试服务器的传入流量。

为来自这些地址的传入流量打开端口 80 (http) 和 443 (https)：

```
13.106.106.20
13.106.106.21
13.106.106.22
13.106.106.23
13.106.106.24
13.106.106.25
13.106.106.26
13.106.106.27
13.106.106.28
13.106.106.29
157.55.14.43
157.55.14.44
157.55.14.47
157.55.14.49
157.55.14.50
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.64
157.55.14.65
202.89.228.57
202.89.228.67
202.89.228.68
202.89.228.69
207.46.14.51
207.46.14.52
207.46.14.55
207.46.14.56
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.63
207.46.14.64
207.46.14.65
207.46.14.67
207.46.14.68
207.46.56.57
207.46.56.58
207.46.56.59
207.46.56.61
207.46.56.62
207.46.56.63
207.46.56.64
207.46.56.67
207.46.71.37
207.46.71.38
207.46.71.51
207.46.71.52
207.46.71.54
207.46.71.55
207.46.71.57
207.46.71.58
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.157
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.162
207.46.98.169
207.46.98.170
207.46.98.171
207.46.98.172
213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.61
213.199.178.63
213.199.178.64
52.187.29.7
52.187.179.17
52.187.76.248
52.187.43.24
52.163.57.91
52.187.30.120
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
65.54.78.57
65.54.78.58
65.54.78.59
65.54.78.60
65.55.82.77
65.55.82.78
65.55.82.81
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
94.245.72.52
94.245.72.53
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38


```  

## <a name="data-access-api"></a>数据访问 API
| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |13.82.26.252<br/>40.76.213.73 |80,443 |
| API 文档 |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |13.82.24.149<br/>40.114.82.10 |80,443 |

## <a name="profiler"></a>探查器

| 目的 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 代理 | agent.azureserviceprofiler.net | 动态 | 443
| 门户 | gateway.azureserviceprofiler.net | 动态 | 443

