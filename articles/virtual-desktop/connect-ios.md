---
title: 从 iOS 连接到 Windows 虚拟桌面-Azure
description: 如何从 iOS 连接到 Windows 虚拟桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
ms.openlocfilehash: 3e77a77dfa2de003722419f861e957d4cb011b71
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310316"
---
# <a name="connect-from-ios"></a>从 iOS 连接

> 适用于： iOS 8.0 或更高版本。 与 iPhone、iPad 和 iPod touch 兼容。

>[!NOTE]
> IOS 客户端当前仍处于预览阶段。

可以使用可下载的客户端从 iOS 设备访问 Windows 虚拟桌面资源。 本指南将说明如何设置 iOS 客户端。

## <a name="install-the-ios-beta-client"></a>安装 iOS Beta 客户端
安装 iOS Beta 客户端：

1. 在 iOS 设备上安装[Apple TestFlight](https://apps.apple.com/us/app/testflight/id899247664)应用。
2. 在 iOS 设备上，打开浏览器并导航到[aka.ms/rdiosbeta](https://aka.ms/rdiosbeta)。
3. 在 " **步骤2：加入 Beta**，选择 "**启动测试**"。
4. 重定向到 TestFlight 应用后，选择 "**接受**"，并选择 "**安装**"。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅管理员提供的源，获取可在 iOS 设备上访问的托管资源的列表。

订阅源：

1. 在连接中心中，点击 **+** ""，然后点击 "**添加工作区**"。
2. 在 "**源 url** " 字段中输入源 url。 源 URL 可以是 URL，也可以是电子邮件地址。
   - 如果使用 URL，请使用管理员提供的 URL。 通常，该 URL 是<https://rdweb.wvd.microsoft.com>。
   - 若要使用电子邮件，请输入你的电子邮件地址。 如果管理员已将服务器配置为，则此方法将通知客户端搜索与你的电子邮件地址关联的 URL。
3. 点击 "**下一步**"。
4. 在系统提示时提供凭据。
   - 对于 "**用户名**"，为用户名提供访问资源的权限。
   - 对于 "**密码**"，请提供与用户名关联的密码。
   - 如果管理员以这种方式配置了身份验证，则还可能会提示你提供其他因素。
5. 点击 "**保存**"。

此后，连接中心应显示远程资源。

订阅源后，该源的内容会定期自动更新。 可以根据管理员所做的更改来添加、更改或删除资源。

## <a name="client-documentation"></a>客户端文档

若要了解有关如何使用 iOS Beta 客户端的详细信息，请查看[ios 客户端入门](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios)文档。
