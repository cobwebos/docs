---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: d1416e7a6cad079be94b0a9b48d7c7c71a598cc2
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53977328"
---
Fluentd 是一个用于统一日志记录的开放源代码数据收集器。 `Fluentd` 设置管理容器到 [Fluentd](https://www.fluentd.org) 服务器的连接。 容器包含一个 Fluentd 日志记录提供程序，让容器向 Fluentd 服务器写入日志和（可选）指标数据。

下表描述了 `Fluentd` 节支持的配置设置。

| 名称 | 数据类型 | Description |
|------|-----------|-------------|
| `Host` | String | Fluentd 服务器的 IP 地址或 DNS 主机名。 |
| `Port` | Integer | Fluentd 服务器的端口。<br/> 默认值为 24224。 |
| `HeartbeatMs` | Integer | 检测信号间隔，以毫秒为单位。 如果在此间隔过期之前未发送事件流量，则将检测信号发送到 Fluentd 服务器。 默认值为 60000 毫秒（1 分钟）。 |
| `SendBufferSize` | Integer | 为发送操作分配的网络缓冲空间，以字节为单位。 默认值为 32768 字节（32 KB）。 |
| `TlsConnectionEstablishmentTimeoutMs` | Integer | 与 Fluentd 服务器建立 SSL/TLS 连接的超时值，以毫秒为单位。 默认值为 10000 毫秒（10 秒）。<br/> 如果 `UseTLS` 设置为 false，则会忽略此值。 |
| `UseTLS` | Boolean | 指示容器是否应使用 SSL/TLS 来与 Fluentd 服务器通信。 默认值为 false。 |