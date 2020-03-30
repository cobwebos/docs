---
title: 从 Android 连接到 Windows 虚拟桌面 - Azure
description: 如何使用 Android 客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d2990c82efbcdac7d453f920301787b8c83db1e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295376"
---
# <a name="connect-with-the-android-client"></a>使用 Android 客户端进行连接

> 适用于：Android 4.1 及更高版本，Chromebook 配备 ChromeOS 53 及更高版本。

>[!NOTE]
> 从 Android 客户端访问 Windows 虚拟桌面资源的功能当前在预览版中可用。

您可以使用我们的可下载客户端从 Android 设备访问 Windows 虚拟桌面资源。 您还可以在支持 Google Play 商店的 Chromebook 设备上使用 Android 客户端。 本指南将告诉您如何设置 Android 客户端。

## <a name="install-the-android-client"></a>安装 Android 客户端

要开始，请在 Android 设备上[下载](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)并安装客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅管理员提供的源，获取您可以在 Android 设备上访问的托管资源列表。

要订阅源：

1. 在连接中心中，点**+** 按 ，然后点按 **"远程资源源**"。
2. 在源 URL 字段中输入源**URL。** 源 URL 可以是 URL 或电子邮件地址。
   - 如果您使用 URL，请通常<https://rdweb.wvd.microsoft.com>使用管理员为您提供的 URL。
   - 要使用电子邮件，请输入您的电子邮件地址。 如果管理员以这种方式配置了服务器，则客户端将搜索与您的电子邮件地址关联的 URL。
3. 点按**下一个**。
4. 在系统提示时提供凭据。
   - 对于**用户名**，授予具有访问资源权限的用户名。
   - 对于**密码**，请提供与用户名关联的密码。
   - 如果管理员以这种方式配置了身份验证，则可能还会提示您提供其他因素。

订阅后，连接中心应显示远程资源。

订阅源后，源的内容将自动定期更新。 资源可能会根据管理员所做的更改进行添加、更改或删除。

## <a name="next-steps"></a>后续步骤

要了解有关如何使用 Android 客户端的详细信息，请查看 Android[客户端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)入门。
