---
title: 将 F5 ASM 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 F5 ASM 数据连接器将 F5 ASM 日志推送到 Azure Sentinel。 在工作簿中查看 F5 ASM 数据，创建警报，并改进调查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 63e20467750e9a4b0c85de33180d4fa51831e59b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555418"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>将 F5 ASM 连接到 Azure Sentinel

本文介绍如何使用 F5 ASM 数据连接器轻松地将 F5 ASM 日志推送到 Azure Sentinel。 这样你就可以在工作簿中查看 F5 ASM 数据，使用它创建自定义警报，还可以将其合并以改进调查。 将 F5 ASM 数据连接到 Azure Sentinel 可让你更深入地了解组织的 Web 应用程序安全性，并增强安全操作功能。 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>配置 F5 ASM 以发送 CEF 消息

1. 按照 [F5 配置应用程序安全事件日志记录](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html)中的说明，使用以下准则来设置远程日志记录：
   - 将“远程存储类型”设置为“CEF”。 
   - 将“协议”设置为“TCP”。 
   - 将“IP 地址”设置为 Syslog 服务器的 IP 地址。
   - 将“端口号”设置为“514”，或代理将使用的端口。 
   - 可以将“最大查询字符串大小”设置为在代理中设置的大小。

1. 若要将 Log Analytics 中的相关架构用于 CEF 事件，请搜索 `CommonSecurityLog`。

1. 继续执行[步骤 3：验证连接性](connect-cef-verify.md)。


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 F5 ASM 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

