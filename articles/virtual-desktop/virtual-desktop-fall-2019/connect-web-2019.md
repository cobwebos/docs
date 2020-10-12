---
title: 将 Windows 虚拟桌面 (经典) web 客户端连接到 Azure
description: 如何使用 web 客户端连接到 Windows 虚拟桌面 (经典) 。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a55e35ba89caefc362024fc871ac5eb800447b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008417"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>通过 web 客户端连接到 Windows 虚拟桌面 (经典) 

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../connect-web.md)。

Web 客户端允许从 Web 浏览器访问 Windows 虚拟桌面资源，而无需漫长的安装过程。

>[!NOTE]
>Web 客户端当前不支持移动操作系统。

## <a name="supported-operating-systems-and-browsers"></a>支持的操作系统和浏览器

尽管任何支持 HTML5 的浏览器都应该运行，但我们正式支持以下操作系统和浏览器。

| 浏览者           | 支持的 OS                     | 说明               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | 版本 55 或更高版本 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>访问远程资源源

在浏览器中，导航到 <https://rdweb.wvd.microsoft.com/webclient> 上的 Windows Virtual Desktop Web 客户端，然后使用用户帐户登录。

>[!NOTE]
>如果使用 Azure 资源管理器集成的 Windows 虚拟桌面，请改为连接到资源 <https://rdweb.wvd.microsoft.com/arm/webclient> 。

>[!NOTE]
>如果已使用与要用于 Windows 虚拟桌面的帐户不同的 Azure Active Directory 帐户登录，则应注销或使用专用浏览器窗口。

登录后，应可立即看到资源列表。 可以像在“所有资源”选项卡中选择普通应用一样，通过选择资源来启动它们。

## <a name="next-steps"></a>后续步骤

若要了解有关如何使用 Web 客户端的详细信息，请查看 [Web 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
