---
title: Windows 虚拟桌面环境-Azure
description: Windows 虚拟桌面环境的基本元素。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ad535dd18b89cbe2fceab90f73789180ad332b57
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612361"
---
# <a name="windows-virtual-desktop-environment"></a>Windows 虚拟桌面环境

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/environment-setup-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Windows 虚拟桌面是一种服务，使用户能够轻松安全地访问其虚拟化桌面和 RemoteApps。 本主题将详细介绍 Windows 虚拟桌面环境的一般结构。

## <a name="host-pools"></a>主机池

主机池是在运行 Windows 虚拟桌面代理时注册为 Windows 虚拟桌面作为会话主机的 Azure 虚拟机的集合。 主机池中的所有会话主机虚拟机都应源自同一映像，以实现一致的用户体验。

主机池可以是以下两种类型之一：

- 个人，其中每个会话主机分配给单个用户。
- 池，其中的会话主机可以接受来自主机池内某个应用组的任何授权用户的连接。

你可以在主机池上设置附加属性以更改其负载平衡行为、每个会话主机可以使用的会话数，以及用户在登录到其 Windows 虚拟桌面会话时可以执行的操作。 可以通过应用组控制发布给用户的资源。

## <a name="app-groups"></a>应用组

应用组是在主机池中的会话主机上安装的应用程序的逻辑分组。 应用组可以是以下两种类型之一：

- RemoteApp，用户可在其中访问单独选择并发布到应用组的 RemoteApps
- 桌面，用户在其中访问完整桌面

默认情况下，当你创建主机池时，将自动创建一个桌面应用组（名为 "桌面应用程序组"）。 你可以随时删除此应用组。 但是，如果存在桌面应用组，则无法在主机池中创建另一个桌面应用组。 若要发布 RemoteApps，必须创建 RemoteApp 应用组。 可以创建多个 RemoteApp 应用组来适应不同的辅助角色。 不同的 RemoteApp 应用组还可以包含重叠的 RemoteApps。

若要将资源发布到用户，你必须将其分配给应用组。 将用户分配到应用组时，请考虑以下事项：

- 用户可以在同一主机池中同时分配到桌面应用组和 RemoteApp 应用组。 但是，用户只能为每个会话启动一种类型的应用组。 用户无法在单个会话中同时启动这两种类型的应用程序组。
- 用户可以分配给同一主机池中的多个应用组，其源将是这两个应用组的累积。

## <a name="workspaces"></a>工作区

工作区是 Windows 虚拟桌面中的应用程序组的逻辑分组。 每个 Windows 虚拟桌面应用程序组必须与一个工作区关联，用户才能查看向其发布的远程应用和桌面。  

## <a name="end-users"></a>最终用户

将用户分配到其应用组后，他们可以使用任何 Windows 虚拟桌面客户端连接到 Windows 虚拟桌面部署。

## <a name="next-steps"></a>后续步骤

了解有关委派访问权限的详细信息，以及如何在[Windows 虚拟桌面中委派访问权限](delegated-access-virtual-desktop.md)将角色分配给用户。

若要了解如何设置 Windows 虚拟桌面主机池，请参阅[使用 Azure 门户创建主机池](create-host-pools-azure-marketplace.md)。

若要了解如何连接到 Windows 虚拟桌面，请参阅以下文章之一：

- [与 Windows 10 或 Windows 7 连接](connect-windows-7-and-10.md)
- [使用 Web 浏览器建立连接](connect-web.md)
- [使用 Android 客户端进行连接](connect-android.md)
- [使用 macOS 客户端进行连接](connect-macos.md)
- [使用 iOS 客户端进行连接](connect-ios.md)