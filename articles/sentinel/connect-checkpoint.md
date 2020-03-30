---
title: 将检查点数据连接到 Azure 哨兵*微软文档
description: 了解如何将检查点数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 70836ec557eff1be035d92e8e7db30a882e05fc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588410"
---
# <a name="connect-check-point-to-azure-sentinel"></a>将检查点连接到 Azure 哨兵



本文介绍如何将检查点设备连接到 Azure Sentinel。 通过"检查点"数据连接器，可以轻松地将检查点日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报并改进调查。 在 Azure Sentinel 上使用检查点将为您提供更多有关组织 Internet 使用情况的见解，并将增强其安全操作功能。 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>将检查点日志转发到 Syslog 代理

将检查点设备配置为通过 Syslog 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区。

1. 转到[检查点日志导出](https://aka.ms/asi-syslog-checkpoint-forwarding)。
1. 向下滚动到**基本部署**，然后按照说明设置连接，使用以下准则：
   - 将**Syslog 端口**设置为**514**或您在代理上设置的端口。
     - 将 CLI 中**的名称****和目标服务器 IP 地址**替换为 Syslog 代理名称和 IP 地址。
     - 将格式设置为**CEF**。
1. 如果您使用的是 R77.30 或 R80.10 版本，请向上滚动到**安装**，然后按照说明为您的版本安装日志导出器。
1. 继续[执行步骤 3：验证连接](connect-cef-verify.md)。
 

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将检查点设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- [验证连接](connect-cef-verify.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


