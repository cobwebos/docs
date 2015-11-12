<properties
	pageTitle="登录到 Windows Server VM | Microsoft Azure"
	description="了解如何使用 Azure 预览门户和资源管理器部署模型登录到 Windows Server VM。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="09/15/2015"
	wacn.date=""/>

# 如何登录到运行 Windows Server 的虚拟机 

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-include.md)]本文介绍如何使用资源管理器部署模型登录到 Windows VM。你还可以使用[经典部署模型](/documentation/articles/virtual-machines-log-on-windows-server)创建资源。

你将在 Azure 预览门户中使用“连接”按钮来启动远程桌面会话。首先，你将连接到虚拟机，然后将登录。

## 连接到虚拟机

1. 如果你尚未登录 [Azure 预览门户](https://manage.windowsazure.cn)，请先登录。

2.	在“中心”菜单上，单击“浏览”。

3.	在搜索边栏选项卡上，向下滚动并单击“虚拟机”。

	![搜索虚拟机](./media/virtual-machines-log-on-windows-server-preview/search-blade-preview-portal.png)

4.	从列表中选择虚拟机。

5. 在虚拟机边栏选项卡上，单击“连接”。

	![连接到虚拟机](./media/virtual-machines-log-on-windows-server-preview/preview-portal-connect.png)

## 登录到虚拟机

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../includes/virtual-machines-log-on-win-server.md)]

## 故障排除

如果这些登录提示没有帮助或不是你所需的内容，请参阅[对与基于 Windows 的 Azure 虚拟机的远程桌面连接进行故障排除](/documentation/articles/virtual-machines-troubleshoot-remote-desktop-connections)。此文将指导你完成诊断和解决常见问题。

<!---HONumber=79-->