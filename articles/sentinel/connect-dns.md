---
title: 在 Azure Sentinel 中连接 DNS 数据 |Microsoft Docs
description: 了解如何在 Azure Sentinel 中连接 DNS 数据。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: dd5442ff8c8d296dfa221a9ea7ed8d5833fd89c1
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240712"
---
# <a name="connect-your-domain-name-server---preview"></a>连接域名服务器-预览



可以将 Windows 上运行的任何域名服务器（DNS）连接到 Azure Sentinel。 这是通过在 DNS 计算机上安装代理来完成的。 使用 DNS 日志，可以通过收集、分析和关联分析和审核日志以及来自 DNS 服务器的其他相关数据，获取对组织的 DNS 基础结构的安全、性能和操作相关的见解。

启用 DNS 日志连接时，可以：
- 确定尝试解决恶意域名的客户端
- 确定过时的资源记录
- 确定经常查询的域名和请求频繁的 DNS 客户端
- 查看 DNS 服务器上的请求负载
- 查看动态 DNS 注册错误

## <a name="connected-sources"></a>连接的源

下表介绍了该解决方案支持的连接的源：

| **连接的源** | **支持** | **说明** |
| --- | --- | --- |
| [Windows 代理](../azure-monitor/platform/agent-windows.md) | 是 | 解决方案会从 Windows 代理收集 DNS 信息。 |
| [Linux 代理](../azure-monitor/learn/quick-collect-linux-computer.md) | 否 | 解决方案不会从直接 Linux 代理收集 DNS 信息。 |
| [System Center Operations Manager 管理组](../azure-monitor/platform/om-agents.md) | 是 | 解决方案会从连接的 Operations Manager 管理组中的代理收集 DNS 信息。 从 Operations Manager 代理到 Azure Monitor 的直接连接不是必需的。 数据将从管理组转发到 Log Analytics 工作区。 |
| [Azure 存储帐户](../azure-monitor/platform/collect-azure-metrics-logs.md) | 否 | 解决方案不会使用 Azure 存储。 |

### <a name="data-collection-details"></a>数据收集详细信息

解决方案从安装有 Log Analytics 代理的 DNS 服务器收集 DNS 清单以及与 DNS 事件相关的数据。 通过运行 DNS PowerShell cmdlet 收集与清单相关的数据，如 DNS 服务器的数量、区域和资源记录。 该数据每两天更新一次。 与事件相关的数据几乎是从由 Windows Server 2012 R2 中增强的 DNS 日志记录和诊断提供的[分析和审核日志](https://technet.microsoft.com/library/dn800669.aspx#enhanc)中实时收集的。


## <a name="connect-your-dns-appliance"></a>连接 DNS 设备

1. 在 Azure Sentinel 门户中，选择 "**数据连接器**"，然后选择 " **DNS** " 磁贴。
1. 如果你的 DNS 计算机在 Azure 中：
    1. 单击 "**在 Azure Windows 虚拟机上安装代理"** 。
    1. 在 "**虚拟机**" 列表中，选择要流式传输到 Azure SENTINEL 的 DNS 计算机。 请确保这是一个 Windows VM。
    1. 在为该 VM 打开的窗口中，单击 "**连接**"。  
    1. 在**DNS 连接器**窗口中单击 "**启用**"。 

2. 如果你的 DNS 计算机不是 Azure VM：
    1. 单击 "**在非 Azure 计算机上安装代理"** 。
    1. 在 "**直接代理**" 窗口中，选择 "**下载 windows 代理（64位）** " 或 "**下载 windows 代理（32位）** "。
    1. 在 DNS 计算机上安装代理。 复制**工作区 ID**、**主密钥**和**辅助密钥**，并在安装过程中出现提示时使用它们。

3. 若要在 DNS 日志 Log Analytics 中使用相关架构，请搜索**DnsEvents**。

## <a name="validate"></a>验证 

在 Log Analytics 中，搜索架构**DnsEvents** ，并确保有事件。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 DNS 本地设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
