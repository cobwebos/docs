---
title: Azure 网络观察程序中的数据包捕获简介 | Microsoft 文档
description: 此页概述了网络观察程序数据包捕获功能
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 3a81afaa-ecd9-4004-b68e-69ab56913356
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 152cc8fb61aa6115c7b5863e4d798db9e7aa5b7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23036832"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Azure 网络观察程序中的可变数据包捕获简介

使用网络观察程序可变数据包捕获，可以创建数据包捕获会话以跟踪进出虚拟机的流量。 数据包捕获有助于以主动和被动方式诊断网络异常。 其他用途包括收集网络统计信息，获得网络入侵信息，调试客户端与服务器之间的通信，等等。

数据包捕获是通过网络观察程序远程启动的虚拟机扩展。 此功能可减轻手动在所需虚拟机上运行数据包捕获的负担，从而可节省宝贵的时间。 可以通过门户、PowerShell、CLI 或 REST API 触发数据包捕获。 触发数据包捕获方式的一个示例是使用虚拟机警报。 为捕获会话提供了筛选器以确保捕获要监视的流量。 筛选器基于 5 元组（协议、本地 IP 地址、远程 IP 地址、本地端口和远程端口）信息。 捕获的数据存储在本地磁盘或存储 blob 中。 每个区域每个订阅限制为 10 个数据包捕获会话。 此限制仅适用于会话，不适用于本地 VM 或存储帐户中已保存的数据包捕获文件。

> [!IMPORTANT]
> 数据包捕获需要虚拟机扩展 `AzureNetworkWatcherExtension`。 有关在 Windows VM 上安装扩展的信息，请访问[适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md)；有关 Linux VM 的信息，请访问[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md)。

要将捕获的信息减少到仅为所需的信息，可以将以下选项用于数据包捕获会话：

**捕获配置**

|属性|说明|
|---|---|
|**每个数据包的最大字节数（字节）** | 从每个数据包捕获的字节数，如果留空，将捕获所有字节。 从每个数据包捕获的字节数，如果留空，将捕获所有字节。 如果仅需要 IPv4 标头 - 请在此处指示 34 |
|**每个会话的最大字节数（字节）** | 达到会话结束值后，该会话中捕获的字节总数。|
|**时间限制（秒）** | 为数据包捕获会话设置时间约束。 默认值为 18000 秒或 5 小时。|

**筛选（可选）**

|属性|说明|
|---|---|
|**协议** | 要筛选的数据包捕获协议。 可用值为 TCP、UDP 和 All。|
|**本地 IP 地址** | 此值将数据包捕获筛选为本地 IP 地址与此筛选器值匹配的数据包。|
|**本地端口** | 此值将数据包捕获筛选为本地端口与此筛选器值匹配的数据包。|
|**远程 IP 地址** | 此值将数据包捕获筛选为远程 IP 与此筛选器值匹配的数据包。|
|**远程端口** | 此值将数据包捕获筛选为远程端口与此筛选器值匹配的数据包。|

### <a name="next-steps"></a>后续步骤

通过访问[在 Azure 门户中管理数据包捕获](network-watcher-packet-capture-manage-portal.md)，了解如何通过门户管理数据包捕获，或者通过访问[使用 PowerShell 管理数据包捕获](network-watcher-packet-capture-manage-powershell.md)，了解如何使用 PowerShell 管理数据包捕获。

通过访问[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)，了解如何基于虚拟机警报创建主动数据包捕获

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png













