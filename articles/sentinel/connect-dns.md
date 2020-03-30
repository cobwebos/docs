---
title: 在 Azure 哨兵中连接 DNS 数据*微软文档
description: 了解如何在 Azure Sentinel 中连接 DNS 数据。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 6d43b82ecd13ac5e082d270ee44ce61fef763d2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588308"
---
# <a name="connect-your-domain-name-server"></a>连接域名服务器

> [!IMPORTANT]
> Azure Sentinel 中的 DNS 数据连接器当前处于公共预览状态。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您可以将在 Windows 上运行的任何域名服务器 （DNS） 连接到 Azure 哨兵。 这是通过在 DNS 计算机上安装代理来完成的。 使用 DNS 日志，您可以通过从 DNS 服务器收集、分析和关联分析和审核日志和其他相关数据，获得组织 DNS 基础结构的安全、性能和操作相关见解。

启用 DNS 日志连接时，您可以：
- 确定尝试解决恶意域名的客户端
- 确定过时的资源记录
- 确定经常查询的域名和请求频繁的 DNS 客户端
- 查看 DNS 服务器上的请求负载
- 查看动态 DNS 注册错误

## <a name="connected-sources"></a>连接的源

下表介绍了该解决方案支持的连接的源：

| **连接源** | **支持** | **说明** |
| --- | --- | --- |
| [Windows 代理](../azure-monitor/platform/agent-windows.md) | 是 | 解决方案会从 Windows 代理收集 DNS 信息。 |
| [Linux 代理](../azure-monitor/learn/quick-collect-linux-computer.md) | 否 | 解决方案不会从直接 Linux 代理收集 DNS 信息。 |
| [System Center Operations Manager 管理组](../azure-monitor/platform/om-agents.md) | 是 | 解决方案会从连接的 Operations Manager 管理组中的代理收集 DNS 信息。 从 Operations Manager 代理到 Azure Monitor 的直接连接不是必需的。 数据将从管理组转发到 Log Analytics 工作区。 |
| [Azure 存储帐户](../azure-monitor/platform/collect-azure-metrics-logs.md) | 否 | 解决方案不会使用 Azure 存储。 |

### <a name="data-collection-details"></a>数据收集详细信息

解决方案从安装有 Log Analytics 代理的 DNS 服务器收集 DNS 清单以及与 DNS 事件相关的数据。 通过运行 DNS PowerShell cmdlet 收集与清单相关的数据，如 DNS 服务器的数量、区域和资源记录。 该数据每两天更新一次。 与事件相关的数据几乎是从由 Windows Server 2012 R2 中增强的 DNS 日志记录和诊断提供的[分析和审核日志](https://technet.microsoft.com/library/dn800669.aspx#enhanc)中实时收集的。


## <a name="connect-your-dns-appliance"></a>连接 DNS 设备

1. 在 Azure Sentinel 门户中，选择 **"数据"连接符**并选择**DNS（预览）** 磁贴。
1. 如果您的 DNS 计算机位于 Azure 中：
    1. 单击**Azure Windows 虚拟机上的安装代理**。
    1. 在 **"虚拟机**"列表中，选择要流式传输到 Azure Sentinel 的 DNS 计算机。 请确保这是 Windows VM。
    1. 在为该 VM 打开的窗口中，单击"**连接**"。  
    1. 在 DNS**连接器**窗口中单击 **"启用**"。 

2. 如果您的 DNS 计算机不是 Azure VM：
    1. 单击**非 Azure 计算机上安装代理**。
    1. 在 **"直接代理"** 窗口中，选择 **"下载 Windows 代理（64 位）"** 或 **"下载 Windows 代理"（32 位）。**
    1. 在 DNS 计算机上安装代理。 复制**工作区 ID、****主键**和**辅助键**，并在安装过程中提示时使用它们。

3. 要在日志分析中使用 DNS 日志中的相关架构，请搜索**DnsEvents**。

## <a name="validate"></a>验证 

在日志分析中，搜索架构**DnsEvents**并确保存在事件。

## <a name="troubleshooting"></a>疑难解答

如果"查找查询"未显示在 Azure Sentinel 中，请按照以下步骤操作，以便正确显示查询：
1. 打开服务器上的[DNS 分析日志](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))。
2. 确保 DNS 事件显示在日志分析集合列表中。
3. 打开[Azure DNS 分析](../azure-monitor/insights/dns-analytics.md)。
4. 在 Azure DNS 分析中，在**配置**下 ，更改任何设置，将其保存，然后在需要时将其更改回，然后再次保存它。
5. 检查 Azure DNS 分析以确保当前显示查询。

## <a name="next-steps"></a>后续步骤

在本文档中，您学习了如何将 DNS 本地设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
