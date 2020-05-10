---
title: 连接 Windows 虚拟桌面 web 客户端-Azure
description: 如何使用 web 客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: f17d04d94546f31c5613fa9944c7399ea7db10ae
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006051"
---
# <a name="connect-with-the-web-client"></a>使用 Web 客户端进行连接

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/connect-web-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Web 客户端允许从 web 浏览器访问 Windows 虚拟桌面资源，而无需漫长的安装过程。

>[!NOTE]
>Web 客户端当前不支持移动操作系统。

## <a name="supported-operating-systems-and-browsers"></a>支持的操作系统和浏览器

尽管任何支持 HTML5 的浏览器都应该运行，但我们正式支持以下操作系统和浏览器。

| 浏览器           | 支持的 OS                     | 注释               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | 版本55或更高版本 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>访问远程资源源

在浏览器中，导航到[Windows 虚拟桌面 web 客户端](https://rdweb.wvd.microsoft.com/arm/webclient)，并使用用户帐户登录。

>[!NOTE]
>如果你使用的是 Windows 虚拟桌面秋季2019版，请使用[本文](./virtual-desktop-fall-2019/connect-web-2019.md)中的 web 客户端链接连接到你的资源。

>[!NOTE]
>如果你已使用与要用于 Windows 虚拟桌面的帐户 Azure Active Directory 不同的帐户登录，则应注销或使用专用浏览器窗口。

登录后，应会看到资源列表。 您可以通过在 "**所有资源**" 选项卡中选择普通应用程序来启动资源。

## <a name="next-steps"></a>后续步骤

若要了解有关如何使用 web 客户端的详细信息，请查看[web 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
