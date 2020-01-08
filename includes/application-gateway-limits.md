---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 0ba21fe3789fba03cd4814d19fb103c3a2559c13
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75662930"
---
| 资源 | 默认值/最大限制 | 说明 |
| --- | --- | --- |
| Azure 应用程序网关 |每个订阅1000 | |
| 前端 IP 配置 |2 |1 个公共，1 个专用 |
| 前端端口 |100<sup>1</sup> | |
| 后端地址池 |100<sup>1</sup> | |
| 每个池的后端服务器数 |1,200 | |
| HTTP 侦听器 |100<sup>1</sup> | |
| HTTP 负载均衡规则 |100<sup>1</sup> | |
| 后端 HTTP 设置 |100<sup>1</sup> | |
| 每个网关的实例数 |32 | |
| SSL 证书 |100<sup>1</sup> |每个 HTTP 侦听器1个 |
| 最大 SSL 证书大小 |V1 SKU-10 KB<br>V2 SKU-16 KB| |
| 身份验证证书 |100 | |
| 受信任的根证书 |100 | |
| 请求超时最小值 |1 秒 | |
| 请求超时最大值 |24 小时 | |
| 站点数 |100<sup>1</sup> |每个 HTTP 侦听器1个 |
| 每个侦听器的 URL 映射数 |第 | |
| 每个 URL 映射基于路径的最大规则数|100||
| 重定向配置数 |100<sup>1</sup>| |
| 并发的 WebSocket 连接数 |中型网关20k<br> 大型网关50k| |
| 最大 URL 长度|32KB| |
| HTTP/2 的最大标头大小 |4KB| |
| 最大文件上传大小，标准 |2GB | |
| 最大文件上传大小 WAF |v1 中型 WAF 关，100 MB<br>v1 大型 WAF 网关，500 MB<br>v2 WAF，750 MB| |
| WAF 正文大小限制，不包含文件|128 KB||
| 最 WAF 自定义规则|100||
| 最 WAF 排除项|100||

<sup>1</sup>对于启用了 WAF 的 sku，建议将资源数限制为40以获得最佳性能。
