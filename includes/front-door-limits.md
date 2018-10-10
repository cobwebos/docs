---
title: include 文件
description: include 文件
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 9/17/2018
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: f0c2d1501b9aa19dec8c4ad157e004a57e0e5070
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006470"
---
| 资源 | 默认限制 |
| --- | --- |
| 每个订阅的 Front Door资源数 | 100 |
| 每个资源的前端主机（包括自定义域）数 | 100 |
| 每个资源的路由规则数 | 100 |
| 每个资源的后端池数 | 50 |
| 每个后端池的后端数 | 100 |
| 要匹配路由规则的路径模式数 | 25 |
| 每个策略的自定义 Web 应用程序防火墙规则数 | 10 |
| 每个资源的 Web 应用程序防火墙策略数 | 100 |

### <a name="timeout-values"></a>超时值
#### <a name="client-to-front-door"></a>客户端到 Front Door
- Front Door 的空闲 TCP 连接超时为 61 秒。
#### <a name="front-door-to-application-backend"></a>Front Door 到应用程序后端
- 如果响应是分块响应，则在收到第一个区块时返回 200。
- 在 HTTP 请求转发到后端之后，Front Door 将等待后端的第一个数据包 30 秒，然后向客户端返回 503 错误。
- 在从后端收到第一个数据包之后，Front Door 等待 30秒（空闲超时），然后向客户端返回 503 错误。
- Front Door 到后端的 TCP 会话超时为 30 分钟。

### <a name="upload--download-data-limit"></a>上载/下载数据限制

|  | 使用分块传输编码 (CTE) | 不使用 HTTP 分块 |
| ---- | ------- | ------- |
| **下载** | 下载大小没有限制 | 下载大小没有限制 |
| **上载** |  只要每个 CTE 上载都小于 28.6 MB，就没有限制 | 大小不得大于 28.6 MB。 |
