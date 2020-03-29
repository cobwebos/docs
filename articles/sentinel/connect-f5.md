---
title: 将 F5 数据连接到 Azure 哨兵*微软文档
description: 了解如何将 F5 数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 6f33cecca1c67f91d0f2be64ab156f45ee500521
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588257"
---
# <a name="connect-f5-to-azure-sentinel"></a>将 F5 连接到 Azure 哨兵

本文介绍如何将 F5 设备连接到 Azure Sentinel。 F5 数据连接器允许您轻松地将 F5 日志与 Azure Sentinel 连接、查看仪表板、创建自定义警报以及改进调查。 在 Azure Sentinel 上使用 F5 将为您提供更多了解组织的 Internet 使用情况的信息，并将增强其安全操作功能。 

## <a name="configure-your-f5-to-send-cef-messages"></a>配置 F5 以发送 CEF 消息

1. 转到[F5 配置应用程序安全事件日志记录](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)，然后按照说明使用以下准则设置远程日志记录：
   - 将**远程存储类型**设置为**CEF**。
   - 将**协议**设置为**TCP**。
   - 将**IP 地址**设置为 Syslog 服务器 IP 地址。
   - 将**端口号**设置为**514**，或将代理设置为要使用的端口。
   - 您可以将**最大查询字符串大小**设置为在代理中设置的大小。

1. 要在日志分析中为 CEF 事件使用相关架构，请`CommonSecurityLog`搜索 。

1. 继续[执行步骤 3：验证连接](connect-cef-verify.md)。


## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 F5 连接到 Azure 哨兵。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

