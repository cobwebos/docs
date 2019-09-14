---
title: Application Insights 和 Log Analytics 使用的 IP 地址 | Microsoft Docs
description: Application Insights 所需的服务器防火墙例外
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: mbullwin
ms.openlocfilehash: d0b3c6124af30cb9ad870ad5f2600495ab872698
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983658"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Application Insights 和 Log Analytics 使用的 IP 地址
[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 服务使用许多 IP 地址。 如果要监视的应用托管在防火墙后面，可能需要知道这些 IP 地址。

> [!NOTE]
> 尽管这些地址是静态的，但我们可能随时需要更改它们。 除了需要入站防火墙规则的可用性监视和 Webhook 之外，所有 Application Insights 流量都表示出站流量。
> 
> 

> [!TIP]
> 通过将 https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom 添加到喜欢的 RSS/ATOM 阅读器来订阅此页面作为 RSS 源，以获取有关最新更改的通知。
> 
> 

## <a name="outgoing-ports"></a>传出端口
需要在服务器防火墙中打开某些传出端口，允许 Application Insights SDK 和/或状态监视器将数据发送到门户：

| 用途 | URL | IP | 端口 |
| --- | --- | --- | --- |
| 遥测 |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.162.117<br/>40.73.171.20<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248 | 443 |
| 实时指标流 |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |

## <a name="status-monitor"></a>状态监视器
状态监视器配置 - 仅在进行更改时需要。

| 用途 | URL | IP | 端口 |
| --- | --- | --- | --- |
| 配置 |`management.core.windows.net` | |`443` |
| 配置 |`management.azure.com` | |`443` |
| 配置 |`login.windows.net` | |`443` |
| 配置 |`login.microsoftonline.com` | |`443` |
| 配置 |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| 配置 |`auth.gfx.ms` | |`443` |
| 配置 |`login.live.com` | |`443` |
| 安装 | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`,`dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>可用性测试
这是用于运行[可用性 Web 测试](../../azure-monitor/app/monitor-web-app-availability.md)的地址列表。 如果想要对应用运行 Web 测试，但 Web 服务器局限于为特定的客户端提供服务，则必须允许来自可用性测试服务器的传入流量。

为来自这些地址的传入流量打开端口 80 (http) 和 443 (https)（IP 地址按位置进行分组）：

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

## <a name="application-insights-api"></a>Application Insights API
| 用途 | URI | IP | 端口 |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| API 文档 |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Azure Pipeline 批注扩展 |aigs1.aisvc.visualstudio.com |动态|443 |

## <a name="log-analytics-api"></a>Log Analytics API

| 用途 | URI | IP | 端口 |
| --- | --- | --- | --- |
| API |api.loganalytics.io<br/>*.api.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| API 文档 |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |

## <a name="application-insights-analytics"></a>Application Insights Analytics

| 用途 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 分析门户 | analytics.applicationinsights.io | 动态 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 动态 | 80,443 |
| 媒体 CDN | applicationanalyticsmedia.azureedge.net | 动态 | 80,443 |

注意：*.applicationinsights.io 域为 Application Insights 团队所有。

## <a name="log-analytics-portal"></a>Log Analytics 门户

| 用途 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 门户 | portal.loganalytics.io | 动态 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 动态 | 80,443 |

注意：*.loganalytics.io 域由 Log Analytics 团队拥有。

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure 门户扩展

| 用途 | URI | IP | 端口 |
| --- | --- | --- | --- |
| Application Insights 扩展 | stamp2.app.insightsportal.visualstudio.com | 动态 | 80,443 |
| Application Insights 扩展 CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | 动态 | 80,443 |

## <a name="application-insights-sdks"></a>Application Insights SDK

| 用途 | URI | IP | 端口 |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | 动态 | 80,443 |
| Application Insights Java SDK | aijavasdk.blob.core.windows.net | 动态 | 80,443 |

## <a name="alert-webhooks"></a>警报 Webhook

| 用途 | IP | 端口
| --- | --- | --- |
| 警报 | 23.96.11.4 | 443 |

## <a name="profiler"></a>探查器

| 用途 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 代理 | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73 | 443
| 门户 | gateway.azureserviceprofiler.net | 动态 | 443
| 存储 | *.core.windows.net | 动态 | 443

## <a name="snapshot-debugger"></a>快照调试器

> [!NOTE]
> 探查器和快照调试器共享同一组 IP 地址。

| 用途 | URI | IP | 端口 |
| --- | --- | --- | --- |
| 代理 | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73 | 443
| 门户 | ppe.gateway.azureserviceprofiler.net | 动态 | 443
| 存储 | *.core.windows.net | 动态 | 443
