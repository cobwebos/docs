---
title: 从 macOS 连接到 Windows 虚拟桌面-Azure
description: 如何使用 macOS 客户端连接到 Windows 虚拟桌面。
services: virtual-desktop
author: heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f2b4696886c898eec5895c4ccb59347cf2a49f03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208988"
---
# <a name="connect-with-the-macos-client"></a>使用 macOS 客户端进行连接

> 适用于： macOS 10.12 或更高版本

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 2020 春季更新版。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面 2019 秋季版，请参阅[此文](./virtual-desktop-fall-2019/connect-macos-2019.md)。
>
> Windows 虚拟桌面 2020 春季更新版目前为公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

可以使用可下载的客户端从 macOS 设备访问 Windows 虚拟桌面资源。 本指南将说明如何设置客户端。

## <a name="install-the-client"></a>安装客户端

若要开始， [download](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)请   在 macOS 设备上下载并安装客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅管理员提供给你的源，以获取能够在 macOS 设备上访问的受管理资源的列表。

若要订阅源，请执行以下操作：

1. 在主页上选择 "**添加工作区**" 以连接到服务并检索资源。
2. 输入源 URL。 该项可以是 URL 或电子邮件地址：
   - 如果使用 URL，请输入管理员提供给你的 URL。 此 URL 通常是 <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>。
   - 若要使用电子邮件地址，请输入你的电子邮件地址。 这会指示客户端搜索与你的电子邮件地址关联的 URL，前提是管理员已采用这种方式配置了服务器。
3. 选择 **添加** 。
4. 出现提示时，请使用用户帐户登录。

登录后，应该会看到可用资源的列表。

订阅源后，源的内容会定期自动更新。 资源可能会根据管理员所做的更改而进行添加、更改或删除。

## <a name="next-steps"></a>后续步骤

若要了解有关 macOS 客户端的详细信息，请查看[macOS 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)文档。
