---
title: "使用 Azure 网络观察程序监视网络连接 - Azure 门户 | Microsoft Docs"
description: "了解如何使用 Azure 门户通过 Azure 网络观察程序监视网络连接。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2018
ms.author: jdial
ms.openlocfilehash: beaa458d727a05eccf496933deb3c998828868cd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-network-connections-with-azure-network-watcher-using-the-azure-portal"></a>使用 Azure 门户通过 Azure 网络观察程序监视网络连接

了解如何使用连接监视器来监视 Azure 虚拟机和 IP 地址之间的网络连接。 IP 地址可分配给其他 Azure 资源、Internet 或本地资源。

## <a name="prerequisites"></a>先决条件

必须满足以下必备项，才可完成本文中的步骤：

* 要监视其连接的区域中的网络观察程序的实例。 如果尚不具备实例，可通过完成[创建 Azure 网络观察程序实例](network-watcher-create.md)中的步骤来创建一个。
* 要执行监视的虚拟机。
* 要用于监视连接的虚拟机中安装有 `AzureNetworkWatcherExtension`。 若要在 Windows 虚拟机中安装扩展项，请参阅 [适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)；若在 Linux 虚拟机中安装扩展项，请参阅[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)。

## <a name="sign-in-to-azure"></a>登录 Azure 

登录到 [Azure 门户](http://portal.azure.com)。

## <a name="create-a-connection-monitor"></a>创建连接监视器

1. 在门户左侧选择“更多服务”。
2. 首先键入“网络观察程序”。 搜索结果中出现“网络观察程序”后，将其选中。
3. 在“监视”下选择“连接监视器(预览版)”。 预览版中功能的可靠性和区域适用情况与正式版不同。
4. 选择“+添加”。
5. 输入或选择要监视的连接的相应信息，然后选择“添加”。 本例中，通过端口 80 监视 myVmSource 和 myVmDestination 虚拟机之间的连接。
    
    |  设置                                 |  值               |
    |  -------------------------------------   |  ------------------- |
    |  名称                                    |  myConnectionMonitor |
    |  源虚拟机                  |  myVmSource          |
    |  Source Port                             |                      |
    |  目标，请选择一个虚拟机   |  myVmDestination     |
    |  Destination Port                        |  80                  |

6. 开始监视。 连接监视器每 60 秒探测一次。
7. 连接监视器会显示平均往返时间和探测失败百分比。 可在网格或图表中查看监视数据。

## <a name="next-steps"></a>后续步骤

- 了解如何通过[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)利用虚拟机来自动捕获数据包。

- 通过 [IP 流验证](network-watcher-check-ip-flow-verify-portal.md)确定某个流量是传入还是传出虚拟机。