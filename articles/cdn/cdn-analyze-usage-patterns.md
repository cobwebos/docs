---
title: "分析 Azure CDN 使用模式 |Microsoft Docs"
description: "你可以通过下列报告查看 CDN 的使用模式：带宽报告、已传输数据报告、命中数报告、缓存状态报告、缓存命中率报告、已传输 IPV4/IPV6 数据报告。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: aadbe872dd3384c8d337b432fb3be69422ca322b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# 分析 Azure CDN 使用模式
<a id="analyze-azure-cdn-usage-patterns" class="xliff"></a>

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

以下指南介绍通过 Verizon 配置文件的管理门户查看核心报告的步骤。 还可[通过 Azure 门户](cdn-log-analysis.md)将核心分析数据导出到 Verizon 和 Akamai 配置文件的存储、事件中心或 Log Analytics (oms)。

可通过下列报告查看 CDN 的使用模式：

* 带宽
* 已传输数据
* 命中数
* 缓存状态
* 缓存命中率
* 已传输 IPV4/IPV6 数据

## 访问核心报告
<a id="accessing-core-reports" class="xliff"></a>
1. 从 CDN 配置文件的边栏选项卡，单击“**管理**”按钮。
   
    ![CDN 配置文件边栏选项卡管理按钮](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN 管理门户打开。
2. 将鼠标悬停在“**分析**”选项卡，然后悬停在“**核心报告**”浮出控件。  在菜单中单击所需的报告。
   
    ![CDN 管理门户 - 核心报告菜单](./media/cdn-reports/cdn-core-reports.png)

## 带宽
<a id="bandwidth" class="xliff"></a>
带宽报告包含一个图和一个数据表，说明了特定时段内 HTTP 和 HTTPS 的带宽使用量。 你可以查看所有 CDN POP 或特定 POP 的带宽使用量。 此功能允许你查看整个 CDN POP 的流量高峰和流量分布情况（以 Mbps 为单位）。

* 可以选择“所有 Edge 节点”以查看所有节点的流量，也可以从下拉列表中选择特定的区域/节点。
* 选择日期范围可以查看本日/本周/本月等的数据，也可以输入自定义日期，然后单击“执行”以确保你的选择得到更新。
* 你可以导出和下载数据，只需单击“执行”旁边的 Excel 工作表图标即可。

该报告每 5 分钟更新一次。

![带宽报告](./media/cdn-reports/cdn-bandwidth.png)

## 传输的数据
<a id="data-transferred" class="xliff"></a>
该报告包含一个图和一个数据表，说明了特定时段内 HTTP 和 HTTPS 的流量使用情况。 你可以查看所有 CDN POP 或特定 POP 的流量使用情况。 此功能允许你查看整个 CDN POP 的流量高峰和流量分布情况（以 GB 为单位）。

* 可以选择“所有 Edge 节点”以查看所有节点的流量，也可以从下拉列表中选择特定的区域/节点。
* 选择日期范围可以查看本日/本周/本月等的数据，也可以输入自定义日期，然后单击“执行”以确保你的选择得到更新。
* 你可以导出和下载数据，只需单击“执行”旁边的 Excel 工作表图标即可。

该报告每 5 分钟更新一次。

![已传输数据报告](./media/cdn-reports/cdn-data-transferred.png)

## 命中数（状态代码）
<a id="hits-status-codes" class="xliff"></a>
此报告描述内容的请求状态代码的分布。 对内容的每个请求都会生成一个 HTTP 状态代码。 状态代码描述边缘 POP 如何处理请求。 例如，2xx 状态代码指示已成功处理客户端的请求，而 4xx 状态代码则指示发生了错误。 有关 HTTP 状态代码的更多详细信息，请参阅 [状态代码](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)。

* 选择日期范围可以查看本日/本周/本月等的数据，也可以输入自定义日期，然后单击“执行”以确保你的选择得到更新。
* 你可以导出和下载数据，只需单击“执行”旁边的 Excel 工作表即可。

![命中数报告](./media/cdn-reports/cdn-hits.png)

## 缓存状态
<a id="cache-statuses" class="xliff"></a>
此报告描述客户端请求的缓存命中数和缓存未命中数的分布情况。 缓存命中数越高，性能最高，因此你可以通过尽量降低缓存未命中数和过期的缓存命中数来优化数据传递速度。 可以通过以下方式减少缓存未命中数：配置源服务器，避免分配“no-cache”响应标头；除非极其需要，否则应避免缓存查询字符串；避免不可缓存的响应代码。 可以通过以下方式避免过期的缓存命中数：尽量延长资产的最大年龄，以便尽量减少发送到源服务器的请求数。

![缓存状态报告](./media/cdn-reports/cdn-cache-statuses.png)

### 主要的缓存状态包括：
<a id="main-cache-statuses-include" class="xliff"></a>
* TCP_HIT：从 Edge 提供。 该对象已在缓存中且尚未超过其最大有效期。
* TCP_MISS：从源提供。 该对象不在缓存中，响应已发送回源。
* TCP_EXPIRED _MISS：通过源重新验证之后，从源提供。 该对象已在缓存中，但已超过其最大有效期。 通过源重新验证之后，缓存对象就会被新的来自源的响应替换。
* TCP_EXPIRED _HIT：通过源重新验证之后，从 Edge 提供 。 该对象已在缓存中，但已超过其最大有效期。 通过源服务器重新验证之后，就会取消对缓存对象的修改。
* 选择日期范围可以查看本日/本周/本月等的数据，也可以输入自定义日期，然后单击“执行”以确保你的选择得到更新。
* 你可以导出和下载数据，只需单击“执行”旁边的 Excel 工作表图标即可。

### 缓存状态的完整列表
<a id="full-list-of-cache-statuses" class="xliff"></a>
* TCP_HIT - 通过 POP 直接处理客户端的请求时，会报告此状态。 当某项资产缓存在最靠近客户端的 POP 中且具有有效的生存时间（即 TTL）时，就会立即从该 POP 提供该资产。 TTL 取决于以下响应标头：
  
  * Cache-Control: s-maxage
  * Cache-Control: max-age
  * Expires
* TCP_MISS - 此状态指示在最靠近客户端的 POP 上找不到所请求资产的缓存版本。 将从源服务器或源盾牌服务器请求该资产。 如果源服务器或源盾牌服务器返回一个资产，该资产将提供给客户端并缓存在客户端和边缘服务器上。 否则，将返回非 200 状态代码（例如“403 已禁止”、“404 找不到”等）。
* TCP_EXPIRED _HIT - 当客户端请求因所针对的资产的 TTL 已过期（例如资产的最大有效期已过期）而被系统直接交由 POP 处理时，会报告此状态。
  
    当某个请求过期时，通常会向源服务器发送重新验证请求。 只有在源服务器指示更新版资产不存在的情况下，才会报告 TCP_EXPIRED _HIT。 出现此类情况时，通常会更新该资产的 Cache-Control 和 Expires 标头。
* TCP_EXPIRED _MISS - 当更新版的已过期缓存资产是由 POP 提供给客户端时，会报告此状态。 当缓存资产的 TTL 已过期（例如，最大有效期已过期）且源服务器返回该资产的更新版本时，会发生这种情况。 将向客户端提供这个新版的资产而不是缓存版本的资产。 此外，它还会缓存在边缘服务器和客户端上。
* CONFIG_NOCACHE - 此状态指示，在边缘 POP 上存在的特定于客户的配置导致资产无法缓存。
* NONE - 此状态指示尚未执行缓存内容新鲜度检查。
* TCP_ CLIENT_REFRESH _MISS - 当某个 HTTP 客户端（例如浏览器）强制边缘 POP 从源服务器检索过时资产的新版本时，会报告此状态。
  
    默认情况下，我们的服务器会禁止 HTTP 客户端强制边缘服务器从源服务器检索新版资产。
* TCP_ PARTIAL_HIT - 当某个字节范围请求导致某个部分缓存的资产被命中时，会报告此状态。 请求的字节范围将立即从 POP 提供给客户端。
* UNCACHEABLE - 当资产的 Cache-Control 和 Expires 标头指示该资产不应缓存在 POP 上或不应由 HTTP 客户端缓存时，会报告此状态。 这些类型的请求均由源服务器处理。

## 缓存命中率
<a id="cache-hit-ratio" class="xliff"></a>
此报告指示直接从缓存提供的已缓存请求的百分比。

该报告提供以下详细信息：

* 所请求的内容缓存在最靠近请求者的 POP 上。
* 该请求直接由我们网络中的边缘服务器处理。
* 该请求没有要求通过源服务器重新验证。

报告不包括：

* 因国家/地区的筛选选项而被拒绝的请求。
* 其标头指示不应进行缓存的资产所对应的请求。 例如，Cache-Control: private、Cache-Control: no-cache 或 Pragma: no-cache 标头会阻止资产进行缓存。
* 针对部分缓存内容的字节范围请求。

公式为：(TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

* 选择日期范围可以查看本日/本周/本月等的数据，也可以输入自定义日期，然后单击“执行”以确保你的选择得到更新。
* 你可以导出和下载数据，只需单击“执行”旁边的 Excel 工作表图标即可。

![缓存命中率报告](./media/cdn-reports/cdn-cache-hit-ratio.png)

## 已传输 IPV4/IPV6 数据
<a id="ipv4ipv6-data-transferred" class="xliff"></a>
此报告显示 IPV4 和 IPV6 中流量使用的分布情况。

![已传输 IPV4/IPV6 数据](./media/cdn-reports/cdn-ipv4-ipv6.png)

* 选择日期范围可以查看本日/本周/本月等的数据，也可以输入自定义日期，
* 然后单击“执行”以确保你的选择得到更新。

## 注意事项
<a id="considerations" class="xliff"></a>
只能生成过去 18 个月的报告。


