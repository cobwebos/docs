---
title: 从 iOS 连接到 Windows 虚拟桌面 (经典) -Azure
description: 如何使用 iOS 客户端连接到 Windows 虚拟桌面 (经典) 。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7efedadbba196223db2ad5700035e27d37fb414
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88008451"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-ios-client"></a>通过 iOS 客户端连接到 Windows 虚拟桌面 (经典) 

> 适用于： iOS 13.0 或更高版本。 与 iPhone、iPad 和 iPod touch 兼容。

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../connect-ios.md)。

可以使用可下载的客户端从 iOS 设备访问 Windows 虚拟桌面资源。 本指南将说明如何设置 iOS 客户端。

## <a name="install-the-ios-client"></a>安装 iOS 客户端

若要开始，请在 iOS 设备上 [下载](https://aka.ms/rdios) 并安装客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅管理员提供的源，获取可在 iOS 设备上访问的托管资源的列表。

若要订阅源，请执行以下操作：

1. 在连接中心中，点击 **+** ""，然后点击 " **添加工作区**"。
2. 在 " **源 url** " 字段中输入源 url。 源 URL 可以是 URL，也可以是电子邮件地址。
   - 如果使用 URL，请输入管理员提供给你的 URL。 此 URL 通常是 <https://rdweb.wvd.microsoft.com>。
   - 若要使用电子邮件地址，请输入你的电子邮件地址。 这会指示客户端搜索与你的电子邮件地址关联的 URL，前提是管理员已采用这种方式配置了服务器。
3. 点击“下一步”。
4. 出现提示时，提供你的凭据。
   - 对于 " **用户名**"，为用户名提供访问资源的权限。
   - 对于 " **密码**"，请提供与用户名关联的密码。
   - 如果管理员以这种方式配置了身份验证，则还可能会提示你提供其他因素。
5. 点击“保存”。

之后，Connection Center 应该会显示远程资源。

订阅源后，源的内容会定期自动更新。 资源可能会根据管理员所做的更改而进行添加、更改或删除。

## <a name="next-steps"></a>后续步骤

若要了解有关如何使用 iOS 客户端的详细信息，请查看 [ios 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) 文档。
