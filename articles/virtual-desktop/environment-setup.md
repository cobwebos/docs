---
title: Windows 虚拟桌面环境 - Azure
description: Windows 虚拟桌面环境的基本元素。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 33d058f028b7032f296ffcf82f0e5fe2c993e6fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127922"
---
# <a name="windows-virtual-desktop-environment"></a>Windows 虚拟桌面环境

Windows 虚拟桌面是一项服务，使用户能够轻松安全地访问其虚拟化桌面和远程应用。 本主题将告诉您有关 Windows 虚拟桌面环境的一般结构的更多信息。

## <a name="tenants"></a>租户

Windows 虚拟桌面租户是管理 Windows 虚拟桌面环境的主要界面。 每个 Windows 虚拟桌面租户都必须与 Azure 活动目录相关联，该目录包含将登录到环境的用户。 从 Windows 虚拟桌面租户中，您可以开始创建主机池以运行用户的工作负载。

## <a name="host-pools"></a>主机池

主机池是 Azure 虚拟机的集合，在运行 Windows 虚拟桌面代理时，这些虚拟机会注册到 Windows 虚拟桌面作为会话主机。 主机池中的所有会话主机虚拟机都应来自同一映像，以便获得一致的用户体验。

主机池可以是两种类型之一：

- 个人，其中每个会话主机分配给单个用户。
- 池，其中会话主机可以接受从任何用户授权到主机池中的应用组的连接。

您可以在主机池上设置其他属性，以更改其负载平衡行为、每个会话主机可以执行多少会话，以及用户可以在登录到其 Windows 虚拟桌面会话时对主机池中的会话主机执行哪些操作。 通过应用组控制发布到用户的资源。

## <a name="app-groups"></a>应用组

应用组是安装在主机池中的会话主机上的应用程序的逻辑分组。 应用组可以是两种类型之一：

- 远程应用，用户访问您单独选择的远程应用并将其发布到应用组
- 桌面，用户访问整个桌面

默认情况下，每当创建主机池时，都会自动创建桌面应用组（名为"桌面应用程序组"）。 您可以随时删除此应用组。 但是，当桌面应用组存在时，无法在主机池中创建另一个桌面应用组。 要发布远程应用，必须创建远程应用应用组。 您可以创建多个 RemoteApp 应用组，以适应不同的辅助方案。 不同的 RemoteApp 应用组还可以包含重叠的远程应用。

要向用户发布资源，必须将它们分配给应用组。 将用户分配给应用组时，请考虑以下事项：

- 无法将用户分配给同一主机池中的桌面应用组和 RemoteApp 应用组。
- 可以将用户分配给同一主机池中的多个应用组，并且其源将是两个应用组的累积。

## <a name="tenant-groups"></a>租户组

在 Windows 虚拟桌面中，Windows 虚拟桌面租户是大多数设置和配置发生的位置。 Windows 虚拟桌面租户包含主机池、应用组和应用组用户分配。 但是，在某些情况下，可能需要同时管理多个 Windows 虚拟桌面租户，尤其是在您是云服务提供商 （CSP） 或托管合作伙伴的情况下。 在这些情况下，可以使用自定义 Windows 虚拟桌面租户组放置每个客户的 Windows 虚拟桌面租户并集中管理访问权限。 但是，如果您只管理单个 Windows 虚拟桌面租户，则租户组概念不适用，您可以继续操作和管理默认租户组中存在的租户。

## <a name="end-users"></a>最终用户

将用户分配到其应用组后，他们可以使用任何 Windows 虚拟桌面客户端连接到 Windows 虚拟桌面部署。

## <a name="next-steps"></a>后续步骤

详细了解委派访问以及如何[在 Windows 虚拟桌面中委派访问](delegated-access-virtual-desktop.md)的用户分配角色。

要了解如何设置 Windows 虚拟桌面租户，请参阅在[Windows 虚拟桌面中创建租户](tenant-setup-azure-active-directory.md)。

要了解如何连接到 Windows 虚拟桌面，请参阅以下文章之一：

- [从 Windows 10 或 Windows 7 进行连接](connect-windows-7-and-10.md)
- [从 Web 浏览器进行连接](connect-web.md)
