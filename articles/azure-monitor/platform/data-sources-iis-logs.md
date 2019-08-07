---
title: Azure Monitor 中的 IIS 日志 | Microsoft Docs
description: Internet 信息服务 (IIS) 会将用户活动存储在日志文件中，并可通过 Azure Monitor 进行收集。  本文介绍了如何配置 IIS 日志收集以及在 Azure Monitor 中创建的记录的详细信息。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: cec5ff0a-01f5-4262-b2e8-e3db7b7467d2
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: cc0fcbb2005ce2aaa70c9e1d2a9993d341169209
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814219"
---
# <a name="collect-iis-logs-in-azure-monitor"></a>在 Azure Monitor 中收集 IIS 日志
Internet 信息服务 (IIS) 会将用户活动存储在日志文件中，并可通过 Azure Monitor 进行收集并将其存储为[日志数据](data-platform.md)。

![IIS 日志](media/data-sources-iis-logs/overview.png)

## <a name="configuring-iis-logs"></a>配置 IIS 日志
Azure Monitor 会从 IIS 创建的日志文件中收集条目，因此，必须[配置 IIS 以进行日志记录](https://technet.microsoft.com/library/hh831775.aspx)。

Azure Monitor 仅支持以 W3C 格式存储的 IIS 日志文件，不支持自定义字段或 IIS 高级日志记录。 Azure Monitor 不会收集 NCSA 或 IIS 本机格式的日志。

在 Azure Monitor 中通过[“高级设置”菜单](agent-data-sources.md#configuring-data-sources)配置 IIS 日志。  只需选择**收集 W3C 格式 IIS 日志文件**，即可完成配置。


## <a name="data-collection"></a>数据收集
每次日志时间戳更改时, Azure Monitor 从每个代理收集 IIS 日志条目。 日志每**5 分钟**读取一次。 如果出于任何原因, IIS 在创建新文件的滚动时间之前不会更新时间戳, 则会在创建新文件后收集条目。 创建新文件的频率由 IIS 站点的 "**日志文件滚动更新计划**" 设置控制, 默认情况下每天一次。 如果设置为 "**每小时**", 则 Azure Monitor 每小时收集一次日志。 如果设置为 "**每日**", Azure Monitor 每24小时收集一次日志。


## <a name="iis-log-record-properties"></a>IIS 日志记录属性
IIS 日志记录的类型为 **W3CIISLog**，并具有下表中的属性：

| 属性 | 描述 |
|:--- |:--- |
| 计算机 |从中收集事件的计算机的名称。 |
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

## <a name="log-queries-with-iis-logs"></a>使用 IIS 日志的日志查询
下表提供了检索 IIS 日志记录的日志查询的不同示例。

| 查询 | 描述 |
|:--- |:--- |
| W3CIISLog |所有 IIS 日志记录。 |
| W3CIISLog &#124; where scStatus==500 |返回状态为 500 的所有 IIS 日志记录。 |
| W3CIISLog &#124; summarize count() by cIP |按客户端 IP 地址的 IIS 日志条目计数。 |
| W3CIISLog &#124; where csHost=="www\.contoso.com" &#124; summarize count() by csUriStem |按主机的 URL www\.contoso.com 统计的 IIS 日志条目的计数。 |
| W3CIISLog &#124; summarize sum(csBytes) by Computer &#124; take 500000 |每台 IIS 计算机接收的总字节数。 |

## <a name="next-steps"></a>后续步骤
* 配置 Azure Monitor 以收集其他[数据源](agent-data-sources.md)进行分析。
* 了解[日志查询](../log-query/log-query-overview.md)以便分析从数据源和解决方案中收集的数据。
