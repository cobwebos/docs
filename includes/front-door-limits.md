---
title: include 文件
description: include 文件
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: deca0034996f6c8ddcac71cd4f191c1a0659b655
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "67333366"
---
| Resource | 默认/最大限制 |
| --- | --- |
| 每个订阅的 Azure Front Door 服务资源数 | 100 |
| 每个资源的前端主机数（包括自定义域） | 100 |
| 每个资源的路由规则数 | 100 |
| 每个资源的后端池数 | 50 |
| 每个后端池的后端数 | 100 |
| 要匹配路由规则的路径模式数 | 25 |
| 每个策略的自定义 Web 应用程序防火墙规则数 | 10 |
| 每个资源的 Web 应用程序防火墙策略数 | 100 |
| Web 应用程序防火墙根据每个自定义规则匹配条件数 | 10 |
| 每个匹配条件的 Web 应用防火墙 IP 地址范围数 | 600 |
| 每个匹配条件的 Web 应用程序防火墙字符串匹配值数 | 10 |
| Web 应用程序防火墙字符串匹配值长度 | 256 |
| Web 应用程序防火墙 POST 正文参数名称长度 | 256 |
| Web 应用程序防火墙 HTTP 标头名称长度 | 256 |
| Web 应用程序防火墙 Cookie 名称长度 | 256 |
| 已检查的 Web 应用程序防火墙 HTTP 请求正文大小 | 128 KB |
| Web 应用程序防火墙自定义响应正文长度 | 2 KB |

### <a name="timeout-values"></a>超时值
#### <a name="client-to-front-door"></a>客户端到 Front Door
- Front Door 的空闲 TCP 连接超时为 61 秒。

#### <a name="front-door-to-application-back-end"></a>Front Door 到应用程序后端
- 如果响应是分块响应，则在收到第一个区块时返回 200。
- HTTP 请求转发到后端后，Front Door 将等待来自后端的第一个数据包 30 秒。 然后，它会向客户端返回 503 错误。
- 从后端收到第一个数据包后，Front Door 将在空闲超时中等待 30 秒。 然后，它会向客户端返回 503 错误。
- Front Door 到后端的 TCP 会话超时为 30 分钟。

### <a name="upload-and-download-data-limit"></a>上传和下载数据限制

|  | 使用分块传输编码 (CTE) | 不使用 HTTP 分块 |
| ---- | ------- | ------- |
| **下载** | 下载大小没有限制。 | 下载大小没有限制。 |
| **上载** |  只要每个 CTE 上传都小于 2 GB，就没有限制。 | 大小不得大于 2 GB。 |

### <a name="other-limits"></a>其他限制
- 最大 URL 大小（8,192 字节）指定原始 URL（URL 的方案 + 主机名 + 端口 + 路径 + 查询字符串）的最大长度；最大查询字符串大小（4,096 字节）指定查询字符串的最大长度（以字节为单位）。