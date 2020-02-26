---
title: 将检查点数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将检查点数据连接到 Azure Sentinel。
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588410"
---
# <a name="connect-check-point-to-azure-sentinel"></a>将检查点连接到 Azure Sentinel



本文介绍如何将你的检查点设备连接到 Azure Sentinel。 勾选点数据连接器可让你轻松地将你的检查点日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报和改进调查。 使用 Azure Sentinel 上的检查点可以更深入地了解组织的 Internet 使用情况，并增强其安全操作功能。 

## <a name="forward-check-point-logs-to-the-syslog-agent"></a>将检查点日志转发到 Syslog 代理

将你的检查点设备配置为通过 Syslog 代理将 Syslog 消息以 CEF 格式转发到 Azure 工作区。

1. 请参阅[检查点日志导出](https://aka.ms/asi-syslog-checkpoint-forwarding)。
1. 向下滚动到 "基本" "**部署**"，按照说明使用以下准则设置连接：
   - 将**Syslog 端口**设置为**514**或在代理上设置的端口。
     - 将 CLI 中的**名称**和**目标服务器 IP 地址**替换为 SYSLOG 代理名称和 IP 地址。
     - 将格式设置为**CEF**。
1. 如果使用的是版本 R 77.30 或 R 80.10，请向上滚动到 "**安装**"，并按照说明安装版本的日志导出程序。
1. 继续执行[步骤3：验证连接性](connect-cef-verify.md)。
 

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将检查点设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- [验证连接](connect-cef-verify.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


