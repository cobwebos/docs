---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 44f9400a1ecebd3c204ec0b891c5d7fe01bdfd25
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2018
ms.locfileid: "51628095"
---
| 资源 | 默认限制 | 注意 |
| --- | --- | --- |
| 应用程序网关 |每个订阅 1000 个 | |
| 前端 IP 配置数 |2 |1 个公共，1 个专用 |
| 前端端口数 |40 | |
| 后端地址池数 |40 | |
| 每个池的后端服务器数 |1200 | |
| HTTP 侦听器数 |40 | |
| HTTP 负载均衡规则数 |400 |HTTP 侦听器数量 * n |
| 后端 HTTP 设置数 |40 | |
| 每个网关的实例数 |75 | |
| SSL 证书数 |40 |每个 HTTP 侦听器 1 个 |
| 身份验证证书 |40 | |
| 最短请求超时 |1 秒 | |
| 最长请求超时 |24 小时 | |
| 站点数 |20 |每个 HTTP 侦听器 1 个 |
| 每个侦听器的 URL 映射数 |1 | |
| 每个 URL 映射基于路径的最大规则数|100|
| 重定向配置数 |40| |
| 并发的 WebSocket 连接数 |5000| |
|最大 URL 长度|8000|
| 最大文件上传大小标准 |2 GB | |
| 最大文件上传大小 WAF |中型 WAF 网关 - 100 MB<br>大型 WAF 网关 - 500 MB| |
|WAF 正文大小限制（不带文件）|128 KB|
