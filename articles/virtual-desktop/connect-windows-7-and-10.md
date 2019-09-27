---
title: 从 Windows 10 或 Windows 7 连接到 Windows 虚拟桌面预览-Azure
description: 如何使用 Windows 桌面客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: helohr
ms.openlocfilehash: 49431f75fb801c683a167e1809724eeeef208561
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338446"
---
# <a name="connect-with-the-windows-desktop-client"></a>与 Windows 桌面客户端连接

> 适用于：Windows 7 和 Windows 10

可以使用 windows 桌面客户端通过 Windows 7 或 Windows 10 访问 Windows 虚拟桌面资源。

> [!IMPORTANT]
> Windows 虚拟桌面不支持 RemoteApp 和桌面连接（RADC）客户端或远程桌面连接（MSTSC）客户端。

## <a name="install-the-windows-desktop-client"></a>安装 Windows 桌面客户端

下面是你当前可以下载的客户端：

- [Windows 64 位](https://go.microsoft.com/fwlink/?linkid=2068602)

我们将更新此列表，因为客户端将可用于更多版本的 Windows。

您可以为当前用户安装客户端，而不需要管理员权限，或者您的管理员可以安装和配置客户端，以便设备上的所有用户都可以访问该客户端。

安装后，可以通过搜索 "**远程桌面**"，从 "开始" 菜单启动客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

通过订阅管理员提供的源，获取可供你使用的托管资源的列表。订阅会使资源在本地 PC 上可用。

订阅源：

1. 打开 Windows 桌面客户端。
2. 在主页上选择 "**订阅**"，连接到服务并检索资源。
3. 出现提示时，请用用户帐户登录。

成功登录后，应会看到可访问的资源列表。

可以通过以下两种方法之一启动资源。

- 在客户端的主页中，双击资源以启动它。
- 从 "开始" 菜单正常启动资源。
  - 你还可以在搜索栏中搜索应用。

订阅源后，会定期自动更新源的内容。 可以根据管理员所做的更改来添加、更改或删除资源。

## <a name="next-steps"></a>后续步骤

若要了解有关如何使用 Windows 桌面客户端的详细信息，请参阅[Windows 桌面客户端入门](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windowsdesktop)。
