---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334920"
---
| 资源 | 限制 | 注意 |
| --- | --- | --- |
| Azure 应用程序网关 |每个订阅 1,000 个 | |
| 前端 IP 配置 |2 |1 个公共，1 个专用 |
| 前端端口 |100<sup>1</sup> | |
| 后端地址池 |100<sup>1</sup> | |
| 每个池的后端服务器数 |1,200 | |
| HTTP 侦听器 |200<sup>1</sup> |限制为路由流量的 100 个活动侦听器。 活动侦听器 = 侦听器总数 - 未活动的侦听器。<br>如果路由规则中的默认配置设置为路由流量（例如，它有一个侦听器、后端池和 HTTP 设置），则该配置也算作侦听器。|
| HTTP 负载均衡规则 |100<sup>1</sup> | |
| 后端 HTTP 设置 |100<sup>1</sup> | |
| 每个网关的实例数 |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL 证书数 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| 最大 SSL 证书大小 |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| 身份验证证书 |100 | |
| 受信任的根证书 |100 | |
| 请求超时最小值 |1 秒 | |
| 请求超时最大值 |24 小时 | |
| 站点数 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| 每个侦听器的 URL 映射数 |1 | |
| 每个 URL 映射基于路径的最大规则数|100||
| 重定向配置数 |100<sup>1</sup>| |
| 并发的 WebSocket 连接数 |中型网关 20k<br> 大型网关 50k| |
| 最大 URL 长度|32KB| |
| HTTP/2 的最大标头大小 |4KB| |
| 最大文件上传大小：标准 |2 GB | |
| 最大文件上传大小 WAF |V1 中型 WAF 网关，100 MB<br>V1 大型 WAF 网关，500 MB<br>V2 WAF，750 MB| |
| WAF 正文大小限制，不带文件|128 KB||
| 最大 WAF 自定义规则|100||
| 最大 WAF 排除项数|100||

<sup>1</sup> 对于启用了 WAF 的 SKU，建议将资源数限制为 40 以实现最佳性能。
