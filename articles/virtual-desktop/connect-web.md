---
title: 通过 web 客户端连接到 Windows 虚拟桌面-Azure
description: 如何使用 Web 客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fdbd0ddb33785655cada2600acdecdced4aeb2f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283429"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>通过 web 客户端连接到 Windows 虚拟桌面

>[!IMPORTANT]
>此内容适用于 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果使用的是没有 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/connect-web-2019.md)。

Web 客户端允许从 Web 浏览器访问 Windows 虚拟桌面资源，而无需漫长的安装过程。

>[!NOTE]
>Web 客户端当前不支持移动操作系统。

## <a name="supported-operating-systems-and-browsers"></a>支持的操作系统和浏览器

尽管任何支持 HTML5 的浏览器都应该运行，但我们正式支持以下操作系统和浏览器。

| 浏览者           | 支持的 OS                     | 说明               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | 版本11或更高版本 |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | 版本 55 或更高版本 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>访问远程资源源

在浏览器中，导航到 Windows Virtual Desktop Web 客户端的 Azure 资源管理器集成版本（在 <https://rdweb.wvd.microsoft.com/arm/webclient> 上），然后使用用户帐户登录。

>[!NOTE]
>如果在没有 Azure 资源管理器集成的情况下使用 Windows 虚拟桌面（经典），请改为连接到资源 <https://rdweb.wvd.microsoft.com/webclient> 。

>[!NOTE]
>如果已使用与要用于 Windows 虚拟桌面的帐户不同的 Azure Active Directory 帐户登录，则应注销或使用专用浏览器窗口。

登录后，应可立即看到资源列表。 可以像在“所有资源”选项卡中选择普通应用一样，通过选择资源来启动它们。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Web 客户端，请查看 [Web 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
