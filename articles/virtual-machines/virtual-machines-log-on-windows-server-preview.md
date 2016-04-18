<!-- not suitable for Mooncake -->

<properties
	pageTitle="登录到 Windows Server VM | Azure"
	description="了解如何使用 Azure 门户和 Resource Manager 部署模型登录到 Windows Server VM。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="01/21/2016"
	wacn.date=""/>

# 如何登录到运行 Windows Server 的虚拟机 

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](/documentation/articles/virtual-machines-log-on-windows-server)。

你将在 Azure 门户中使用“连接”按钮来启动远程桌面会话。首先，你将连接到虚拟机，然后将登录。

## 连接到虚拟机

1. 如果你尚未登录 [Azure 门户](https://portal.azure.cn/)，请先登录。

2.	在“中心”菜单中，单击“虚拟机”。

3.	从列表中选择虚拟机。

4. 在虚拟机边栏选项卡上，单击“连接”。

	![连接到虚拟机](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## 登录到虚拟机

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## 故障排除

如果这些登录提示没有帮助或不是你所需的内容，请参阅[对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](/documentation/articles/virtual-machines-troubleshoot-remote-desktop-connections)。此文将指导你完成诊断和解决常见问题。

<!---HONumber=Mooncake_0411_2016-->