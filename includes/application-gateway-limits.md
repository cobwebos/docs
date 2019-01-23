---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211806"
---
| 资源 | 默认限制 | 注意 |
| --- | --- | --- |
| 应用程序网关 |每个订阅 1000 个 | |
| 前端 IP 配置 |2 |1 个公共，1 个专用 |
| 前端端口 |100<sup>1</sup> | |
| 后端地址池 |100<sup>1</sup> | |
| 每个池的后端服务器数 |1200 | |
| HTTP 侦听器数 |100<sup>1</sup> | |
| HTTP 负载均衡规则数 |100<sup>1</sup> | |
| 后端 HTTP 设置 |100<sup>1</sup> | |
| 每个网关的实例数 |32 | |
| SSL 证书数 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| 身份验证证书 |100 | |
| 受信任的根证书 |100 | |
| 最短请求超时 |1 秒 | |
| 最长请求超时 |24 小时 | |
| 站点数 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| 每个侦听器的 URL 映射数 |1 | |
| 每个 URL 映射基于路径的最大规则数|100||
| 重定向配置数 |100<sup>1</sup>| |
| 并发的 WebSocket 连接数 |5000| |
| 最大 URL 长度|8000||
| 最大文件上传大小标准 |2 GB | |
| 最大文件上传大小 WAF |中型 WAF 网关 - 100 MB<br>大型 WAF 网关 - 500 MB| |
| WAF 正文大小限制（不带文件）|128 KB||

<sup>1</sup> 如果启用了 WAF 的 SKU，建议将资源数限制为 40 以获得最佳性能。
