---
title: "Azure Log Analytics 中的 IIS 日志 | Microsoft Docs"
description: "Internet 信息服务 (IIS) 会将用户活动存储在日志文件中，并可通过 Log Analytics 进行收集。  本文介绍如何配置 IIS 日志收集以及在 Log Analytics 工作区中创建的记录的详细信息。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2018
ms.author: bwren
ms.openlocfilehash: b8ce4e6fe6e12aa3edb81abad1589924e3e121e4
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2018
---
# <a name="iis-logs-in-log-analytics"></a>Log Analytics 中的 IIS 日志
Internet 信息服务 (IIS) 会将用户活动存储在日志文件中，并可通过 Log Analytics 进行收集。  

![IIS 日志](media/log-analytics-data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>配置 IIS 日志
Log Analytics 会从 IIS 创建的日志文件中收集条目，因此，必须[配置 IIS 以进行日志记录](https://technet.microsoft.com/library/hh831775.aspx)。

Log Analytics 仅支持以 W3C 格式存储的 IIS 日志文件，不支持自定义字段或 IIS 高级日志记录。  
Log Analytics 不会收集 NCSA 或 IIS 本机格式的日志。

从 [Log Analytics 设置中的“数据”菜单](log-analytics-data-sources.md#configuring-data-sources)配置 Log Analytics 中的 IIS 日志。  只需选择**收集 W3C 格式 IIS 日志文件**，即可完成配置。

启用 IIS 日志收集时，建议应在每台服务器上配置 IIS 日志变换更新设置。

## <a name="data-collection"></a>数据收集
Log Analytics 会从每个连接源收集 IIS 日志条目，时间间隔大约每 15 分钟。  代理会在将其收集到的每个事件日志的位置记录下来。  如果代理脱机，则 Log Analytics 会从上次离开的位置收集事件，即使这些事件是在代理脱机期间创建的。

## <a name="iis-log-record-properties"></a>IIS 日志记录属性
IIS 日志记录的类型为 **W3CIISLog**，并具有下表中的属性：

| 属性 | 说明 |
|:--- |:--- |
| Computer |从中收集事件的计算机的名称。 |
| cIP |客户端的 IP 地址。 |
| csMethod |请求的方法，如 GET 或 POST。 |
| csReferer |用户通过链接转到当前站点的来源站点。 |
| csUserAgent |客户端的浏览器类型。 |
| csUserName |访问服务器的经身份验证的用户名称。 匿名用户会以连字符表示。 |
| csUriStem |请求的目标，例如网页。 |
| csUriQuery |客户端正在尝试执行的查询（如果有的话）。 |
| ManagementGroupName |Operations Manager 代理的管理组的名称。  对于其他代理，这是 AOI-\<工作区 ID\> |
| RemoteIPCountry |客户端 IP 地址的国家/地区。 |
| RemoteIPLatitude |客户端 IP 地址的纬度。 |
| RemoteIPLongitude |客户端 IP 地址的经度。 |
| scStatus |HTTP 状态代码。 |
| scSubStatus |子状态错误代码。 |
| scWin32Status |Windows 状态代码。 |
| sIP |Web 服务器的 IP 地址。 |
| SourceSystem |OpsMgr |
| sPort |客户端连接到的服务器上的端口。 |
| sSiteName |IIS 站点的名称。 |
| TimeGenerated |记录条目的日期和时间。 |
| TimeTaken |处理请求的时间长度（以毫秒为单位）。 |

## <a name="log-searches-with-iis-logs"></a>使用 IIS 日志进行日志搜索
下表提供了检索 IIS 日志记录的日志查询的不同示例。

| 查询 | 说明 |
|:--- |:--- |
| W3CIISLog |所有 IIS 日志记录。 |
| W3CIISLog &#124; where scStatus==500 |返回状态为 500 的所有 IIS 日志记录。 |
| W3CIISLog &#124; summarize count() by cIP |按客户端 IP 地址的 IIS 日志条目计数。 |
| W3CIISLog &#124; where csHost=="www.contoso.com" &#124; summarize count() by csUriStem |按主机的 URL www.contoso.com 的 IIS 日志条目的计数。 |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |每台 IIS 计算机接收的总字节数。 |

## <a name="next-steps"></a>后续步骤
* 配置 Log Analytics 以收集其他[数据源](log-analytics-data-sources.md)进行分析。
* 了解有关[日志搜索](log-analytics-log-searches.md)的信息，以分析从数据源和解决方案收集的数据。
* 在 Log Analytics 中配置警报，以主动通知你在 IIS 日志中找到的重要情况。
