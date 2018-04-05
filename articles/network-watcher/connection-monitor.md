---
title: 使用 Azure 网络观察程序监视网络连接 - Azure 门户 | Microsoft Docs
description: 了解如何使用 Azure 门户通过 Azure 网络观察程序监视网络连接。
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: c7d98350dc8f66ebd4097f22b44dcbbe2653b25d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>使用 Azure 门户通过 Azure 网络观察程序监视网络连接

了解如何使用连接监视器来监视 Azure 虚拟机和 IP 地址之间的网络连接。 连接监视器提供连接级别的监视。 将连接定义为源和目标 IP 地址以及端口的组合。 通过端口 1433，连接监视器支持以下方案，比如监视从虚拟网络中的虚拟机到同一或不同虚拟网络中运行 SQL Server 的虚拟机间的连接。 连接监视器提供连接延迟作为 Azure Monitor 指标，每 60 秒记录一次。 它还提供逐跳拓扑，并确定影响连接的配置问题。


## <a name="prerequisites"></a>先决条件

必须满足以下必备项，才可完成本文中的步骤：

* 要监视其连接的区域中的网络观察程序的实例。 如果尚不具备实例，可通过完成[创建 Azure 网络观察程序实例](network-watcher-create.md)中的步骤来创建一个。
* 要执行监视的虚拟机。
* 要用于监视连接的虚拟机中安装有 `AzureNetworkWatcherExtension`。 若要在 Windows 虚拟机中安装扩展项，请参阅 [适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)；若在 Linux 虚拟机中安装扩展项，请参阅[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。

## <a name="sign-in-to-azure"></a>登录 Azure 

登录到 [Azure 门户](http://portal.azure.com)。

## <a name="create-a-connection-monitor"></a>创建连接监视器

以下步骤可通过端口 80 和 1433 启用对目标虚拟机的连接监视：

1. 在门户左侧选择“更多服务”。
2. 首先键入“网络观察程序”。 搜索结果中出现“网络观察程序”后，将其选中。
3. 在“监视”下选择“连接监视器(预览版)”。 预览版中功能的可靠性和区域适用情况与正式版不同。
4. 选择“+ 添加”。
5. 输入或选择要监视的连接信息，然后选择“添加”。 在下图所示的示例中，监视的连接位于 MultiTierApp0 和 Database0 虚拟机间：

    ![添加连接监视器](./media/connection-monitor/add-connection-monitor.png)

    开始监视。 连接监视器每 60 秒探测一次。

## <a name="view-connection-monitoring"></a>查看连接监视

1. 完成[创建连接监视器](#create-a-connection-monitor)中的步骤 1-3 以查看连接监视。
2. 下图显示 AppToDB(80) 连接的详细信息。 可访问“状态”。 “图形视图”显示“平均往返时间”和“失败的探测百分比”。 该图提供了逐跳信息，并显示没有问题影响着目标可访问性。

    ![查看连接监视器](./media/connection-monitor/view-connection-monitor.png)

3. 查看下图中显示的 AppToDB(1433) 监视器，你将发现，对于相同的源虚拟机和目标虚拟机，无法通过端口 1433 访问状态。 本方案中的“网格视图”提供了逐跳信息和影响可访问性的问题。 在此情况下，NSG 规则阻止第二跃点上端口 1433 上的所有流量。

    ![查看连接监视器](./media/connection-monitor/view-connection-monitor-2.png)

## <a name="next-steps"></a>后续步骤

- 了解如何通过[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)利用虚拟机来自动捕获数据包。
- 通过 [IP 流验证](network-watcher-check-ip-flow-verify-portal.md)确定某个流量是传入还是传出虚拟机。