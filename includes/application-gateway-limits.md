---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 592e1973344b231693077f8286a41dfd67a8d188
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66689103"
---
| Resource | 默认值/最大限制 | 注意 |
| --- | --- | --- |
| Azure 应用程序网关 |每个订阅 1,000 个 | |
| 前端 IP 配置 |2 |1 个公共，1 个专用 |
| 前端端口 |100<sup>1</sup> | |
| 后端地址池 |100<sup>1</sup> | |
| 每个池的后端服务器数 |1,200 | |
| HTTP 侦听器 |100<sup>1</sup> | |
| HTTP 负载均衡规则 |100<sup>1</sup> | |
| 后端 HTTP 设置 |100<sup>1</sup> | |
| 每个网关的实例数 |32 | |
| SSL 证书数 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| SSL 证书的最大大小 |V1 SKU-10 KB<br>V2 SKU-25 KB| |
| 身份验证证书 |100 | |
| 受信任的根证书 |100 | |
| 请求超时最小值 |1 秒 | |
| 请求超时最大值 |24 小时 | |
| 站点数 |100<sup>1</sup> |每个 HTTP 侦听器 1 个 |
| 每个侦听器的 URL 映射数 |第 | |
| 每个 URL 映射基于路径的最大规则数|100||
| 重定向配置数 |100<sup>1</sup>| |
| 并发的 WebSocket 连接数 |中型网关 20k<br> 大型网关 50k| |
| 最大 URL 长度|8,000||
| 最大文件上传大小：标准 |2 GB | |
| 最大文件上传大小 WAF |中型 WAF 网关，100 MB<br>大型 WAF 网关，500 MB| |
| WAF 正文大小限制，不带文件|128 KB||
|最大 WAF 自定义规则|100||

<sup>1</sup> 对于启用了 WAF 的 SKU，建议将资源数限制为 40 以实现最佳性能。
