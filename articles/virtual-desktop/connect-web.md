---
title: 连接 Windows 虚拟桌面 Web 客户端 - Azure
description: 如何使用 Web 客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 061805896203615d7673a006059080e9c4246863
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657203"
---
# <a name="connect-with-the-web-client"></a>使用 Web 客户端进行连接

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 2020 春季更新版。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面 2019 秋季版，请参阅[此文](./virtual-desktop-fall-2019/connect-web-2019.md)。
>
> Windows 虚拟桌面 2020 春季更新版目前为公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

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

在浏览器中，导航到 Windows Virtual Desktop Web 客户端的 Azure 资源管理器集成版本（在 <https://rdweb.wvd.microsoft.com/arm/webclient> 上），然后使用用户帐户登录。

>[!NOTE]
>如果使用没有 Azure 资源管理器集成的 Windows 虚拟桌面秋季 2019 版，请改为通过 <https://rdweb.wvd.microsoft.com/webclient> 连接到资源。

>[!NOTE]
>如果已使用与要用于 Windows 虚拟桌面的帐户不同的 Azure Active Directory 帐户登录，则应注销或使用专用浏览器窗口。

登录后，应可立即看到资源列表。 可以像在“所有资源”选项卡中选择普通应用一样，通过选择资源来启动它们。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Web 客户端，请查看 [Web 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
