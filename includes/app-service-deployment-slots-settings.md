---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/18/2019
ms.author: cephalin
ms.openlocfilehash: 0691b1a531ffebbb2c368bdb37dd4d8025fb4a4e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623702"
---
从另一个部署槽克隆配置时，可以编辑克隆的配置。 某些配置元素在交换时遵循内容（不特定于槽），而其他配置元素会在交换之后保留在同一个槽（特定于槽）。 以下列表显示交换槽时会更改的设置。

**已交换的设置**：

* 常规设置 - 例如 Framework 版本、32/64 位、Web 套接字
* 应用设置（可以配置为停在槽中）
* 连接字符串（可以配置为停在槽中）
* 处理程序映射
* 监视和诊断设置
* 公用证书
* WebJobs 内容
* 混合连接 *
* 虚拟网络集成 *
* 服务终结点 *
* Azure 内容分发网络 *

标有星号 (*) 的功能已计划粘滞到槽。 

**不交换的设置**：

* 发布终结点
* 自定义域名
* 私有证书和 SSL 绑定
* 缩放设置
* Web 作业计划程序
* IP 限制
* 始终可用
* 协议设置（HTTPS、TLS 版本、客户端证书）
* 诊断日志设置
* 跨域资源共享 (CORS)

<!-- VNET and hybrid connections not yet sticky to slot -->