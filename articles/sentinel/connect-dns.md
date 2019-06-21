---
title: 连接 Azure Sentinel 预览版中的 DNS 数据 |Microsoft Docs
description: 了解如何连接 Azure Sentinel 中的 DNS 数据。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 4e6ed18a49a77f8061c975bdf3ecb085ebf71317
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190764"
---
# <a name="connect-your-domain-name-server"></a>连接连接到域的名称服务器

> [!IMPORTANT]
> Azure Sentinel 当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以连接到 Azure Sentinel 在 Windows 上运行任何域名称服务器 (DNS)。 这是 DNS 计算机上安装代理。 使用 DNS 记录，您可以降低安全、 性能和你的组织的 DNS 基础结构的操作见解通过收集，分析，并关联分析和审核日志和其他相关数据从 DNS 服务器。

启用 DNS 日志连接时，你可以：
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


## <a name="connect-your-dns-appliance"></a>连接你的 DNS 设备

1. 在 Azure Sentinel 门户中，选择**数据连接器**，然后选择**DNS**磁贴。
1. 如果你的 DNS 计算机为在 Azure 中：
    1. 单击**Azure Windows 虚拟机上的安装代理**。
    1. 在中**虚拟机**列表中，选择你想要流式传输到 Azure Sentinel DNS 机。 请确保这是 Windows VM。
    1. 在窗口中打开该 VM，单击**Connect**。  
    1. 单击**启用**中**DNS 连接器**窗口。 

2. 如果你的 DNS 机不是 Azure VM:
    1. 单击**非 Azure 计算机上的安装代理**。
    1. 在中**直接代理**窗口中，选择**下载 Windows 代理 （64 位）** 或**下载 Windows 代理 （32 位）** 。
    1. 在 DNS 计算机上安装代理。 复制**工作区 ID**，**主键**，并**辅助密钥**并在安装过程中出现提示时使用。

3. 若要使用 Log Analytics 中的 DNS 日志相关的架构，搜索**DnsEvents**。

## <a name="validate"></a>验证 

在 Log Analytics 搜索架构**DnsEvents**并确保事件。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何连接到 Azure Sentinel DNS 的本地设备。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[来了解一下你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[检测威胁 Azure Sentinel](tutorial-detect-threats.md)。
