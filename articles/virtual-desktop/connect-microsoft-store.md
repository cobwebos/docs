---
title: 连接到 Microsoft Store 客户端-Azure
description: 如何使用 Microsoft Store 客户端连接到 Windows 虚拟桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bab2a11ff9e7907621087e5027929e3e42eaf29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91744740"
---
# <a name="connect-with-the-microsoft-store-client"></a>使用 Microsoft Store 客户端进行连接

>适用于： Windows 10。

可以使用 Windows 10 访问设备上的 Windows 虚拟桌面资源。

## <a name="install-the-microsoft-store-client"></a>安装 Microsoft Store 客户端

你可以为当前用户安装客户端，这不需要管理员权限。 或者，管理员可以安装和配置客户端，以便设备上的所有用户都可以访问该客户端。

安装后，可以通过搜索“远程桌面”，从“开始”菜单启动客户端。

若要开始，请 [从 Microsoft Store 下载并安装客户端](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS)。

## <a name="subscribe-to-a-workspace"></a>订阅工作区

订阅你的管理员提供的工作区，获取你的电脑上可访问的托管资源的列表。

订阅工作区：

1. 在连接中心屏幕上，点击 " **+ 添加**"，然后点击 " **工作区**"。
2. 将工作区 URL 输入到管理员提供的 "工作区 URL" 字段。工作区 URL 可以是 URL 或电子邮件地址。
   
   - 如果使用的是工作区 URL，请使用管理员提供的 URL。
   - 如果要从 Windows 虚拟桌面进行连接，请根据所使用的服务版本，使用以下 Url 之一：
       - Windows 虚拟桌面 (经典) ： `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` 。
       - Windows 虚拟桌面： `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` 。
  
3. 点击 " **订阅**"。
4. 在系统提示时提供凭据。
5. 订阅后，工作区应显示在连接中心中。

可以根据管理员所做的更改添加、更改或删除工作区。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Microsoft Store 客户端，请查看 [Microsoft Store 客户端入门](/windows-server/remote/remote-desktop-services/clients/windows/)。