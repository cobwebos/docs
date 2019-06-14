---
title: Verizon 中的自定义报表 | Microsoft Docs
description: 您可以通过使用以下报表来查看 CDN 使用模式：带宽、 传输的数据命中，缓存状态、 缓存命中率，IPV4/IPV6 传输的数据。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: magattus
ms.openlocfilehash: 75a1fb4beb4025dc3ec63d29944331d07be35d8a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60323548"
---
# <a name="custom-reports-from-verizon"></a>Verizon 中的自定义报表

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

通过 Verizon 配置文件的管理门户使用 Verizon 自定义报表，可定义要为边缘 CNAME 报表收集的数据类型。


## <a name="accessing-verizon-custom-reports"></a>访问 Verizon 自定义报表
1. 从 CDN 配置文件的边栏选项卡，单击“**管理**”按钮。
   
    ![CDN 配置文件管理按钮](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN 管理门户打开。
2. 将鼠标悬停在“分析”选项卡，然后悬停在“自定义报表”浮出控件   。 单击“边缘 CNAME”  。
   
    ![CDN 管理门户 - 自定义报表菜单](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>边缘 CNAME 自定义报表
边缘 CNAME 自定义报表对启用自定义报表记录功能的边缘 CNAME 提供命中数和数据传输统计信息。 边缘 CNAME 由 Azure CDN 终结点主机名和任意关联的自定义域主机名组成。 

自定义报表数据在启用边缘 CNAME 自定义报表功能的一小时后开始记录。 可通过生成特定平台或所有平台的边缘 CNAME 报表来查看报表数据。 此报表的覆盖范围仅限于在指定时间段内收集自定义报表数据的边缘 CNAME。 根据指标选项中定义的指标，边缘 CNAME 报表由前 10 个边缘 CNAME 的图和数据表组成。 

通过定义以下报表选项生成自定义报表：

- 指标：可以使用以下选项：

   - 命中数：指示定向到在其启用自定义报表功能的边缘 CNAME 的请求总数。 该指标不包括返回到客户端的状态代码。

   - 传输的数据：指示从边缘服务器传输到 HTTP 客户端 （例如，web 浏览器） 的数据总量为定向到在其启用自定义报表功能的边缘 CNAME 的请求。 通过将 HTTP 响应标头添加到响应正文来计算数据的传输量。 因此，向每个资产传输的数据量大于其实际文件大小。

- 分组：确定条形图如下所示的统计信息的类型。 可以使用以下选项：

   - HTTP 响应代码：通过 HTTP 响应代码来组织统计信息 （例如，200、 403 等） 返回到客户端。 

   - 缓存状态：按缓存状态组织统计信息。


若要为报表设置日期范围，可从下拉列表中选择预定义的日期范围，例如“今天”或“本周”，也可选择“自定义”，并通过单击日历图标手动输入日期范围    。 

选择日期范围后，单击“开始”生成报表  。

单击“开始”按钮右侧的 Excel 符号，可以 Excel 格式导出数据  。

![CNAME 报表](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>边缘 CNAME 自定义报表字段

| 字段                     | 描述   |
|---------------------------|---------------|
| 2xx                       | 为导致 2xx HTTP 状态代码（例如，200 正常）的边缘 CNAME 指示请求总数或数据传输总量 (MB)。 |
| 3xx                       | 为导致 3xx HTTP 状态代码（例如，302 已找到 或 304 未修改）的边缘 CNAME 指示请求总数或数据传输总量 (MB)。 |
| 4xx                       | 为导致 4xx HTTP 状态代码（例如，400 错误请求、403 禁止访问或 404 未找到）的边缘 CNAME 指示请求总数或数据传输总量 (MB)。 |
| 5xx                       | 为导致 5xx HTTP 状态代码（例如，500 内部服务器错误或 502 错误的网关）的边缘 CNAME 指示请求总数或数据传输总量 (MB)。 |
| 缓存命中率 %               | 指示直接从缓存提供到请求者的可缓存请求百分比。 |
| 缓存命中数                | 为导致缓存命中（例如，TCP_EXPIRED_HIT、TCP_HIT 或 TCP_PARTIAL_HIT）的边缘 CNAME 指示请求总数或数据传输总量 (MB)。 当发现请求内容的缓存版本时，将发生缓存命中。 |
| 已传输数据 (MB)     | 指示从边缘服务器向边缘 CNAME 的 HTTP 客户端（Web浏览器）传输的数据总量 (MB)。 通过将 HTTP 响应标头添加到响应正文来计算数据的传输量。 因此，向每个资产传输的数据量大于其实际文件大小。 |
| 描述               | 通过其主机名标识边缘 CNAME |
| 命中数                      | 指示边缘 CNAME 的请求总数 |
| 未命中数                    | 为导致缓存失误（例如，TCP_CLIENT_REFRESH_MISS、TCP_EXPIRED_MISS 或 TCP_MISS）的边缘 CNAME 指示请求总数或数据传输总量 (MB)。 当请求内容未在接受请求的边缘服务器上缓存时，将发生缓存失误。 | 
| 无缓存                  | 为导致 CONFIG_NOCACHE 缓存状态代码的边缘 CNAME 指示请求总数或数据传输总量 (MB)。  |
| 其他                     | 为已指示导致 HTTP 状态代码（超出 2xx - 5xx 范围）的边缘 CNAME 指示请求总数或数据传输总量 (MB)。 |
| 平台                  | 指示处理边缘 CNAME 流量的平台。 |
| 未分配               | 为未记录缓存状态代码或 HTTP 状态代码信息的边缘 CNAME 指示请求总数或数据传输总量 (MB)。  |
| 不可缓存               | 为导致 UNCACHEABLE 缓存状态代码的边缘 CNAME 指示请求总数或数据传输总量 (MB)。  |


## <a name="considerations"></a>注意事项
仅生成过去 18 个月的报表。

