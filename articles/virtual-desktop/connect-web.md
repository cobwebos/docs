---
title: 通过 web 客户端连接到 Windows 虚拟桌面-Azure
description: 如何使用 Web 客户端连接到 Windows 虚拟桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c8a4b22966c3d4db268e212bb3f2d1bbb78fee74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400630"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>通过 web 客户端连接到 Windows 虚拟桌面

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/connect-web-2019.md)。

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
>如果使用的是不带 Azure 资源管理器集成的 Windows 虚拟桌面 (经典) ，请改为连接到资源 <https://rdweb.wvd.microsoft.com/webclient> 。
>
> 如果使用 US Gov 门户，请使用 <https://rdweb.wvd.azure.us/arm/webclient/index.html> 。

>[!NOTE]
>如果已使用与要用于 Windows 虚拟桌面的帐户不同的 Azure Active Directory 帐户登录，则应注销或使用专用浏览器窗口。

登录后，应可立即看到资源列表。 可以像在“所有资源”选项卡中选择普通应用一样，通过选择资源来启动它们。

## <a name="using-an-input-method-editor"></a>使用输入法编辑器

Web 客户端支持在 **1.0.21.16 或更高**版本的远程会话中使用输入法编辑器 (IME) 。 要在远程会话中使用的键盘的语言包必须安装在主机虚拟机上。 若要了解有关在远程会话中设置语言包的详细信息，请参阅 [将语言包添加到 Windows 10 多会话映像](language-packs.md)。

使用 web 客户端启用 IME 输入：

1. 在连接到远程会话之前，请先前往 "web 客户端 **设置** " 面板。

2. 将 " **启用输入法编辑器** " 设置切换为 **"开**"。

3. 在下拉菜单中，选择要在远程会话中使用的键盘。

4. 连接到远程会话。

当你重点关注远程会话时，web 客户端将禁止显示本地 IME 窗口。 一旦您已经连接到远程会话，更改 IME 设置就不会有任何影响。

>[!NOTE]
>如果主机虚拟机上未安装语言包，则远程会话将默认为英语 (美国) 键盘。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Web 客户端，请查看 [Web 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
