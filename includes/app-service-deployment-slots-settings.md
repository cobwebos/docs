---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129696"
---
从另一个部署槽克隆配置时，可以编辑克隆的配置。 某些配置元素在交换时遵循内容（不特定于槽），而其他配置元素会在交换之后保留在同一个槽（特定于槽）。 以下列表显示交换槽时会更改的设置。

**已交换的设置**：

* 常规设置 - 例如 Framework 版本、32/64 位、Web 套接字
* 应用设置（可以配置为停在槽中）
* 连接字符串（可以配置为停在槽中）
* 处理程序映射
* 公用证书
* WebJobs 内容
* 混合连接 *
* 虚拟网络集成 *
* 服务终结点 *
* Azure 内容分发网络 *

标有星号（*）的功能计划为 unswapped。 

**不交换的设置**：

* 发布终结点
* 自定义域名
* 非公共证书和 TLS/SSL 设置
* 缩放设置
* Web 作业计划程序
* IP 限制
* 始终可用
* 诊断日志设置
* 跨域资源共享 (CORS)

> [!NOTE]
> 适用于 unswapped 设置的某些应用设置也不会进行交换。 例如，由于未交换诊断日志设置，因此也不会交换相关`WEBSITE_HTTPLOGGING_RETENTION_DAYS`的`DIAGNOSTICS_AZUREBLOBRETENTIONDAYS`应用设置（例如和），即使它们未显示为槽设置也是如此。
>
