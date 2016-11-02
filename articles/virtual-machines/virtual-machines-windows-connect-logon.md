<properties
	pageTitle="连接到 Windows Server VM | Microsoft Azure"
	description="了解如何使用 Azure 门户和 Resource Manager 部署模型连接并登录到 Windows VM。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/28/2016"
	ms.author="cynthn"/>

# 如何连接并登录到运行 Windows 的 Azure 虚拟机 


可以使用 Azure 门户中的“连接”按钮来启动远程桌面 (RDP) 会话。首先连接到虚拟机，然后登录。

## 连接到虚拟机

1. 如果你尚未登录 [Azure 门户](https://portal.azure.com/)，请先登录。

2.	在“中心”菜单中，单击“虚拟机”。

3.	从列表中选择虚拟机。

4. 在虚拟机边栏选项卡上，单击“连接”。

	![显示如何连接到 VM 的 Azure 门户屏幕截图。](./media/virtual-machines-windows-connect-logon/connect.png)
	
 > [AZURE.TIP] 如果门户中的“连接”按钮灰显，并且你未通过 [Express Route](../expressroute/expressroute-introduction.md) 或[站点到站点 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 连接连接到 Azure，则必须首先为 VM 创建并分配一个公共 IP 地址，然后才能使用 RDP。详细了解 [Azure 中的公共 IP 地址](../virtual-network/virtual-network-ip-addresses-overview-arm.md)。

## 登录到虚拟机

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]


## 后续步骤

如果在尝试连接时遇到故障，请参阅[故障排除远程桌面连接](virtual-machines-windows-troubleshoot-rdp-connection.md)。此文将指导你完成诊断和解决常见问题。

<!---HONumber=AcomDC_0921_2016-->