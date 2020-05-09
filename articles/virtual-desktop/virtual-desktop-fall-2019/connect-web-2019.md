---
title: 连接 Windows 虚拟桌面 web 客户端-Azure
description: 如何使用 web 客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 357687a80325554c7dbb28eae6e42d946bb012dc
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614858"
---
# <a name="connect-with-the-web-client"></a>使用 Web 客户端进行连接

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../connect-web.md)。

Web 客户端允许从 web 浏览器访问 Windows 虚拟桌面资源，而无需漫长的安装过程。

>[!NOTE]
>Web 客户端当前不支持移动操作系统。

## <a name="supported-operating-systems-and-browsers"></a>支持的操作系统和浏览器

尽管任何支持 HTML5 的浏览器都应该运行，但我们正式支持以下操作系统和浏览器。

| 浏览者           | 支持的 OS                     | 说明               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | 版本55或更高版本 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>访问远程资源源

在浏览器中，导航到[Windows 虚拟桌面 web 客户端](https://rdweb.wvd.microsoft.com/webclient)，并使用用户帐户登录。

>[!NOTE]
>如果你已使用与要用于 Windows 虚拟桌面的帐户 Azure Active Directory 不同的帐户登录，则应注销或使用专用浏览器窗口。

登录后，应会看到资源列表。 您可以通过在 "**所有资源**" 选项卡中选择普通应用程序来启动资源。

## <a name="next-steps"></a>后续步骤

若要了解有关如何使用 web 客户端的详细信息，请查看[web 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。