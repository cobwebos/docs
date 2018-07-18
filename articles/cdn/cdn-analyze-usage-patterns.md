---
title: Verizon 中的核心报告 | Microsoft Docs
description: 可以通过下列报告查看 CDN 的使用模式：带宽报告、已传输数据报告、命中数报告、缓存状态报告、缓存命中率报告、已传输 IPV4/IPV6 数据报告。
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: fa828bfa736d677fb4881e5cc2628c0e03eb8749
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2017
ms.locfileid: "26345241"
---
# <a name="core-reports-from-verizon"></a>Verizon 中的核心报告

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

通过管理门户使用 Verizon 配置文件的 Verizon 核心报告，可以通过以下报告查看 CDN 的使用模式：

* 带宽
* 已传输数据
* 命中数
* 缓存状态
* 缓存命中率
* 已传输 IPV4/IPV6 数据

## <a name="accessing-verizon-core-reports"></a>访问 Verizon 核心报告
1. 从 CDN 配置文件的边栏选项卡，单击“**管理**”按钮。
   
    ![CDN 配置文件管理按钮](./media/cdn-reports/cdn-manage-btn.png)
   
    CDN 管理门户打开。
2. 将鼠标悬停在“**分析**”选项卡，然后悬停在“**核心报告**”浮出控件。 在菜单中单击某份报告。
   
    ![CDN 管理门户 - 核心报告菜单](./media/cdn-reports/cdn-core-reports.png)

3. 对于每份报告，请从“日期范围”列表中选择一个日期范围。 可选择预定义的日期范围，例如“今天”或“本周”，也可选择“自定义”，并通过单击日历图标手动输入日期范围。 

4. 选择日期范围后，单击“开始”生成报告。 

4. 若要以 Excel 格式导出数据，请单击“执行”按钮上方的“Excel”图标。

## <a name="bandwidth"></a>带宽
带宽报告包含一个图和一个数据表，说明了特定时段内 HTTP 和 HTTPS 的 CDN 带宽使用量，以 Mbps 为单位。 可以查看所有 POP 或特定 POP 的带宽使用量。 此报告允许查看整个 POP 的流量高峰和分布。

在“边缘节点”列表中，选择“所有边缘节点”以查看所有节点的流量，或选择特定的区域。

该报告每五分钟更新一次。

![带宽报告](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>传输的数据
该报告包含一个图和一个数据表，说明了特定时段内 HTTP 和 HTTPS 的 CDN 流量用量，以 GB 为单位。 可以查看所有 POP 或特定 POP 的流量用量。 此报告允许查看整个 POP 的流量高峰和分布情况。

在“边缘节点”列表中，选择“所有边缘节点”以查看所有节点的流量，或选择特定的区域。

该报告每五分钟更新一次。

![已传输数据报告](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>命中数（状态代码）
此报告描述内容的请求状态代码的分布。 对内容的每个请求都会生成一个 HTTP 状态代码。 状态代码描述边缘 POP 如何处理请求。 例如，2xx 状态代码指示已成功处理客户端的请求，而 4xx 状态代码则指示发生了错误。 有关 HTTP 状态代码的详细信息，请参阅 [HTTP 状态代码列表](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes)。

![命中数报告](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>缓存状态
此报告描述客户端请求的缓存命中数和缓存未命中数的分布情况。 缓存命中数越高，性能最高，因此可以通过尽量降低缓存未命中数和过期的缓存命中数来优化数据传递速度。 

若要减少缓存失误数，请配置源服务器，以尽量避免使用以下项： 
 * `no-cache` 响应标头
 * 查询字符串缓存，除非绝对必要  
 * 不可缓存的响应代码

若要减少过期的缓存命中数，请将资产的 `max-age` 设置为最长期限，以尽量减少发送到源服务器的请求数。

![缓存状态报告](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>主要的缓存状态包括：
* TCP_HIT：从边缘服务器提供。 该对象已在缓存中且尚未超过其最大有效期。
* TCP_MISS：从源服务器提供。 该对象不在缓存中，响应已发送回源。
* TCP_EXPIRED _MISS：通过源重新验证之后，从源服务器提供。 该对象已在缓存中，但已超过其最大有效期。 通过源重新验证之后，缓存对象就会被新的来自源的响应替换。
* TCP_EXPIRED _HIT：通过源重新验证之后，从 Edge 提供 。 该对象已在缓存中，但已超过其最大有效期。 通过源服务器重新验证之后，就会取消对缓存对象的修改。

### <a name="full-list-of-cache-statuses"></a>缓存状态的完整列表
* TCP_HIT - 通过 POP 直接处理客户端的请求时，会报告此状态。 当某项资产缓存在最靠近客户端的 POP 中且具有有效的生存时间 (TTL) 时，就会立即从该 POP 提供该资产。 TTL 取决于以下响应标头：
  
  * Cache-Control: s-maxage
  * Cache-Control: max-age
  * Expires
* TCP_MISS：此状态指示在最靠近客户端的 POP 上找不到所请求资产的缓存版本。 将从源服务器或源盾牌服务器请求该资产。 如果源服务器或源盾牌服务器返回一个资产，该资产将提供给客户端并缓存在客户端和边缘服务器上。 否则，返回非 200 状态代码（例如“403 已禁止”、“404 找不到”）。
* TCP_EXPIRED _HIT：当客户端请求因所针对的资产的 TTL 已过期而被系统直接交由 POP 处理时，会报告此状态。 例如，资产的最大有效期已过期。 
  
   当某个请求过期时，通常会向源服务器发送重新验证请求。 只有在源服务器指示更新版资产不存在的情况下，才会报告 TCP_EXPIRED _HIT 状态。 出现此类情况时，通常会更新该资产的 Cache-Control 和 Expires 标头。
* TCP_EXPIRED _MISS：当更新版的已过期缓存资产是由 POP 提供给客户端时，会报告此状态。 当缓存资产的 TTL 已过期（例如，最大有效期已过期）且源服务器返回该资产的更新版本时，会出现此状态。 将向客户端提供这个新版的资产而不是缓存版本的资产。 此外，它还会缓存在边缘服务器和客户端上。
* CONFIG_NOCACHE：此状态指示，在边缘 POP 上存在的特定于客户的配置导致资产无法缓存。
* NONE - 此状态指示尚未执行缓存内容新鲜度检查。
* TCP_ CLIENT_REFRESH _MISS：当某个 HTTP 客户端（例如浏览器）强制边缘 POP 从源服务器检索过时资产的新版本时，会报告此状态。 默认情况下，服务器会禁止 HTTP 客户端强制边缘服务器从源服务器检索新版资产。
* TCP_ PARTIAL_HIT：当某个字节范围请求导致某个部分缓存的资产被命中时，会报告此状态。 请求的字节范围将立即从 POP 提供给客户端。
* UNCACHEABLE：当资产的 `Cache-Control` 和 `Expires` 标头指示该资产不应缓存在 POP 上或不应由 HTTP 客户端缓存时，会报告此状态。 这些类型的请求均由源服务器处理。

## <a name="cache-hit-ratio"></a>缓存命中率
此报告指示直接从缓存提供的已缓存请求的百分比。

该报告提供以下详细信息：

* 所请求的内容缓存在最靠近请求者的 POP 上。
* 该请求直接由我们网络中的边缘服务器处理。
* 该请求没有要求通过源服务器重新验证。

报告不包括：

* 因国家/地区的筛选选项而被拒绝的请求。
* 其标头指示不应进行缓存的资产所对应的请求。 例如，`Cache-Control: private`、`Cache-Control: no-cache` 或 `Pragma: no-cache` 标头会阻止缓存资产。
* 针对部分缓存内容的字节范围请求。

公式为：(TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

![缓存命中率报告](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>已传输 IPV4/IPV6 数据
此报告显示 IPV4 和 IPV6 中流量使用的分布情况。

![已传输 IPV4/IPV6 数据](./media/cdn-reports/cdn-ipv4-ipv6.png)

## <a name="considerations"></a>注意事项
只能生成过去 18 个月的报告。

