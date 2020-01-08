---
title: 将 F5 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 F5 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: cf6dc6977ff066b646beac4db5562ae8d97ab066
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610550"
---
# <a name="connect-f5-to-azure-sentinel"></a>将 F5 连接到 Azure Sentinel

本文介绍如何将 F5 设备连接到 Azure Sentinel。 F5 数据连接器可让你轻松地将 F5 日志连接到 Azure Sentinel，查看仪表板、创建自定义警报和改进调查。 通过在 Azure 上使用 F5，可以更深入地了解组织的 Internet 使用情况，并增强其安全操作功能。 

## <a name="configure-your-f5-to-send-cef-messages"></a>配置 F5 以发送 CEF 消息

1. 请访问[F5，配置应用程序安全事件日志记录](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)，然后按照说明设置远程日志记录，使用以下准则：
   - 将**远程存储类型**设置为**CEF**。
   - 将**协议**设置为**TCP**。
   - 将**ip 地址**设置为 SYSLOG 服务器 ip 地址。
   - 将**端口号**设置为**514**，或将代理设置为使用的端口。
   - 可以将**最大查询字符串大小**设置为在代理中设置的大小。

1. 若要在 CEF 事件的 Log Analytics 中使用相关架构，请搜索 `CommonSecurityLog`。

1. 继续执行[步骤3：验证连接性](connect-cef-verify.md)。


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 F5 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

