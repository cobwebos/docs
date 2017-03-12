---
title: "连接到 Windows Server VM | Microsoft Docs"
description: "了解如何使用 Azure 门户和 Resource Manager 部署模型连接并登录到 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 00f6b2e60c20eb27771d9d54df63f930ee88a55a
ms.openlocfilehash: 7427c8126ab73a851bc696d4925366b3b714616d
ms.lasthandoff: 03/02/2017


---
# <a name="how-to-connect-and-log-on-to-an-azure-virtual-machine-running-windows"></a>如何连接并登录到运行 Windows 的 Azure 虚拟机
可以从 Windows 桌面使用 Azure 门户中的“连接”按钮来启动远程桌面 (RDP) 会话。 首先连接到虚拟机，然后登录。

如果尝试从 Mac 连接到 Windows VM，则需为 Mac 安装 RDP 客户端，例如 [Microsoft 远程桌面](https://itunes.apple.com/app/microsoft-remote-desktop/id715768417)。

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机
1. 如果你尚未登录 [Azure 门户](https://portal.azure.com/)，请先登录。
2. 在“中心”菜单中，单击“虚拟机” 。
3. 从列表中选择虚拟机。
4. 在虚拟机边栏选项卡上，单击“连接”。
   
    ![显示如何连接到 VM 的 Azure 门户屏幕截图。](./media/virtual-machines-windows-connect-logon/connect.png)
   
   > [!TIP]
   > 如果门户中的“连接”按钮灰显，并且你未通过 [Express Route](../expressroute/expressroute-introduction.md) 或[站点到站点 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 连接连接到 Azure，则必须先为 VM 创建并分配一个公共 IP 地址才能使用 RDP。 详细了解 [Azure 中的公共 IP 地址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。
   > 
   > 

## <a name="log-on-to-the-virtual-machine"></a>登录到虚拟机
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>后续步骤
如果在尝试连接时遇到故障，请参阅 [故障排除远程桌面连接](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 此文将指导你完成诊断和解决常见问题。


