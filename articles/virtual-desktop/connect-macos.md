---
title: 从 macOS 连接到 Windows 虚拟桌面 - Azure
description: 如何使用 macOS 客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6ac3ee8d44d68f5c7929c2f93f1a1182c7f6083b
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891248"
---
# <a name="connect-with-the-macos-client"></a>使用 macOS 客户端进行连接

> 适用于： macOS 10.12 或更高版本

您可以使用我们的可下载客户端从 macOS 设备访问 Windows 虚拟桌面资源。 本指南将告诉您如何设置客户端。

## <a name="install-the-client"></a>安装客户端

要开始使用，[请在](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12) macOS 设备上下载并安装客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅管理员提供的源，获取 macOS 设备上可用的托管资源列表。

要订阅源：

1. 在主页上选择 **"添加工作区**"以连接到服务并检索资源。
2. 输入“源 URL”。 该项可以是 URL 或电子邮件地址：
   - 如果您使用 URL，请使用管理员为您提供的 URL。 通常，URL 是<https://rdweb.wvd.microsoft.com>。
   - 要使用电子邮件，请输入您的电子邮件地址。 如果管理员以这种方式配置了服务器，则告诉客户端搜索与您的电子邮件地址关联的 URL。
3. 选择 **添加** 。
4. 出现提示时，请使用用户帐户登录。

登录后，应看到可用资源的列表。

订阅源后，源的内容将自动定期更新。 资源可能会根据管理员所做的更改进行添加、更改或删除。

## <a name="next-steps"></a>后续步骤

要了解有关 macOS 客户端的详细信息，请查看[macOS 客户端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)文档入门。
