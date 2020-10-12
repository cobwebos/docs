---
title: 将 Azure 防火墙数据连接到 Azure Sentinel
description: 了解如何将 Azure 防火墙数据连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 05a2869152def0fc9a44defaa21f0643d9fdbb08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504870"
---
# <a name="connect-data-from-azure-firewall"></a>从 Azure 防火墙连接数据

> [!IMPORTANT]
> Azure Sentinel 中的 Azure 防火墙数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 防火墙是托管的基于云的网络安全服务，可保护 Azure 虚拟网络资源。 它是一个服务形式的完全有状态防火墙，具有内置的高可用性和不受限制的云可伸缩性。 

你可以将 Azure 防火墙日志连接到 Azure Sentinel，使你能够在工作簿中查看日志数据，使用它创建自定义警报，并将其合并以改善调查。

详细了解如何 [监视 Azure 防火墙日志](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)。

## <a name="prerequisites"></a>必备条件

- 你必须具有 Azure Sentinel 工作区的读取和写入权限。

## <a name="connect-to-azure-firewall"></a>连接到 Azure 防火墙
    
1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从数据连接器库中选择 " **Azure 防火墙** "，然后选择预览窗格中的 " **打开连接器" 页面**  。

1. 在要连接其日志的所有防火墙上启用 **诊断日志** ：

    1. 选择 " **打开 Azure 防火墙资源 >** " 链接。

    1. 从 **防火墙** 导航菜单中，选择 " **诊断设置**"。

    1. 选择列表底部的 " **+ 添加诊断设置** "。

    1. 在 " **诊断设置** " 屏幕上的 "  **诊断设置名称** " 字段中输入一个名称。
    
    1. 选中 " **发送到 Log Analytics** " 复选框。 其中将显示两个新字段。 选择相关 **订阅** ，并 **Log Analytics 工作区** (Azure Sentinel 所在的位置) 。

    1. 标记要引入其日志的规则类型的复选框。 建议 **AzureFirewallApplicationRule** 和 **AzureFirewallNetworkRule**。

    1. 选择屏幕顶部的 " **保存** "。

1. 若要在 Azure 防火墙警报 Log Analytics 中使用相关架构，请搜索 **AzureDiagnostics**。

> [!NOTE]
>
> 使用这个特定的数据连接器，在过去两周内引入数据时，数据连接器库中的连接状态 (指示器和数据类型) 名称旁的连接图标将显示为 " *已连接* (绿色) 。 两周后，如果没有数据引入，连接器将显示为 "已断开连接"。 数据经历的时间越多， *连接* 状态就返回。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure 防火墙日志连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
