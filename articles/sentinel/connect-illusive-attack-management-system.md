---
title: 将 Illusive 攻击管理系统数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Illusive 攻击管理系统数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 7d2d3871dd9836e2c68155aa82ce01dced128bf3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531075"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>将 Illusive 攻击管理系统连接到 Azure Sentinel

本文介绍了如何将[Illusive 攻击管理系统](https://www.illusivenetworks.com/technology/platform/attack-detection-system)连接到 Azure Sentinel。 Illusive 攻击管理系统数据连接器允许你在 Azure Sentinel 中共享 Illusive 的攻击面分析数据和事件日志，并在专用仪表板中查看此信息，该仪表板提供对组织的攻击面风险（ASM 仪表板）的深入了解，并跟踪组织网络中未经授权的横向移动（ADS 仪表板）。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>将 Illusive 攻击管理系统日志转发到 Syslog 代理  

将攻击管理系统配置为通过 Syslog 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区。

1. 登录到 Illusive 控制台，导航到 "设置->报告"。

1. 查找 Syslog Serversץ

1. 提供以下信息：
   - 主机名： Linux Syslog 代理 IP 地址或 FQDN 主机名
   - 端口：514
   - 协议：TCP
   - 审核消息：将审核消息发送到服务器

1. 若要添加 syslog 服务器，请单击 "添加"。

1. 若要在 Illusive 攻击管理系统 Log Analytics 中使用相关架构，请搜索 "CommonSecurityLog"。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Illusive 攻击管理系统连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
