---
title: Windows 虚拟桌面环境-Azure
description: Windows 虚拟桌面环境的基本元素。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d666f7e3e43b5429423a5356aa00e074ed020869
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615326"
---
# <a name="windows-virtual-desktop-environment"></a>Windows 虚拟桌面环境

>[!IMPORTANT]
>此内容适用于不支持 Azure 资源管理器 Windows 虚拟桌面对象的秋季2019版本。 如果尝试管理春季2020更新中引入的 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[此文](../environment-setup.md)。

Windows 虚拟桌面是一种服务，使用户能够轻松安全地访问其虚拟化桌面和 RemoteApps。 本主题将详细介绍 Windows 虚拟桌面环境的一般结构。

## <a name="tenants"></a>租户

Windows 虚拟桌面租户是用于管理 Windows 虚拟桌面环境的主要界面。 每个 Windows 虚拟桌面租户必须与包含将登录到环境的用户的 Azure Active Directory 相关联。 从 Windows 虚拟桌面租户中，你可以开始创建主机池来运行用户的工作负载。

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

- 不能将用户同时分配到桌面应用组和同一主机池中的 RemoteApp 应用组。
- 用户可以分配给同一主机池中的多个应用组，其源将是这两个应用组的累积。

## <a name="tenant-groups"></a>租户组

在 Windows 虚拟桌面中，大多数设置和配置都会在 Windows 虚拟桌面租户中发生。 Windows 虚拟桌面租户包含主机池、应用组和应用组用户分配。 但是，在某些情况下，你可能需要同时管理多个 Windows 虚拟桌面租户，尤其是在你是云服务提供商（CSP）或托管合作伙伴时。 在这些情况下，可以使用自定义 Windows 虚拟桌面租户组放置每个客户的 Windows 虚拟桌面租户，并集中管理访问权限。 但是，如果你只管理单个 Windows 虚拟桌面租户，则租户组概念不适用，你可以继续操作并管理默认租户组中存在的租户。

## <a name="end-users"></a>最终用户

将用户分配到其应用组后，他们可以使用任何 Windows 虚拟桌面客户端连接到 Windows 虚拟桌面部署。

## <a name="next-steps"></a>后续步骤

了解有关委派访问权限的详细信息，以及如何在[Windows 虚拟桌面中委派访问权限](delegated-access-virtual-desktop-2019.md)将角色分配给用户。

若要了解如何设置 Windows 虚拟桌面租户，请参阅[在 Windows 虚拟桌面中创建租户](tenant-setup-azure-active-directory.md)。

若要了解如何连接到 Windows 虚拟桌面，请参阅以下文章之一：

- [从 Windows 10 或 Windows 7 进行连接](../connect-windows-7-and-10.md)
- [从 Web 浏览器进行连接](connect-web-2019.md)
