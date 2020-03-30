---
title: 连接到 Windows 虚拟桌面 Windows 10 或 7 - Azure
description: 如何使用 Windows 桌面客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b6436059cc8b3637edfc2a146d0aab3e2beae6a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154332"
---
# <a name="connect-with-the-windows-desktop-client"></a>使用 Windows 桌面客户端进行连接

> 适用于：Windows 7、Windows 10 和 Windows 10 IoT 企业

您可以使用 Windows 桌面客户端访问 Windows 7、Windows 10 和 Windows 10 IoT 企业版设备上的 Windows 虚拟桌面资源。

> [!IMPORTANT]
> Windows 虚拟桌面不支持远程应用和桌面连接 （RADC） 客户端或远程桌面连接 （MSTSC） 客户端。

> [!IMPORTANT]
> Windows 虚拟桌面当前不支持 Windows 应用商店中的远程桌面客户端。 将在将来的版本中添加对此客户端的支持。

## <a name="install-the-windows-desktop-client"></a>安装 Windows 桌面客户端

选择与你的 Windows 版本相匹配的客户端：

- [视窗 64 位](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 位](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

你可以为当前用户安装客户端（这不需要管理员权限），或者你的管理员可以安装和配置客户端，以便设备上的所有用户都可以访问该客户端。

安装后，可以通过搜索“远程桌面”****，从“开始”菜单启动客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

通过订阅管理员提供的源，获取可供您使用的托管资源列表。订阅使资源在本地 PC 上可用。

要订阅源：

1. 打开 Windows 桌面客户端。
2. 在主页上选择 **"订阅**"以连接到服务并检索资源。
3. 出现提示时，请使用用户帐户登录。

成功登录后，应看到可以访问的资源的列表。

您可以通过两种方法之一启动资源。

- 从客户端的主页中，双击资源以启动它。
- 像通常从"开始"菜单中启动其他应用一样启动资源。
  - 您还可以在搜索栏中搜索应用。

订阅源后，源的内容将自动定期更新。 资源可能会根据管理员所做的更改进行添加、更改或删除。

## <a name="next-steps"></a>后续步骤

要了解有关如何使用 Windows 桌面客户端的详细信息，请查看 Windows[桌面客户端入门](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)。
