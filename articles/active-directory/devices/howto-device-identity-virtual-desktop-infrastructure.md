---
title: 设备标识和桌面虚拟化 - Azure 活动目录
description: 了解如何将 VDI 和 Azure AD 设备标识一起使用
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900369"
---
# <a name="device-identity-and-desktop-virtualization"></a>设备标识和桌面虚拟化

管理员通常在其组织中部署托管 Windows 操作系统的虚拟桌面基础结构 （VDI） 平台。 管理员将 VDI 部署到：

- 简化管理。
- 通过整合和集中资源降低成本。
- 提供最终用户的移动性和随时随地在任何设备上访问虚拟桌面的自由。

虚拟桌面有两种主要类型：

- 永久
- 非永久

持久版本为每个用户或用户池使用唯一的桌面映像。 这些独特的桌面可以自定义和保存以供将来使用。 

非持久性版本使用用户可以根据需要访问的桌面集合。 用户退出后，这些非持久性桌面将恢复到其原始状态。

本文将介绍 Microsoft 向管理员提供有关支持设备标识和 VDI 的指导。 有关设备标识的详细信息，请参阅文章["什么是设备标识](overview.md)"。

## <a name="supported-scenarios"></a>支持的方案

在为 VDI 环境在 Azure AD 中配置设备标识之前，请熟悉支持的方案。 下表说明了支持哪些预配方案。 在此上下文中进行预配意味着管理员可以大规模配置设备标识，而无需任何最终用户交互。

| 设备标识类型 | 身份基础结构 | Windows 设备 | VDI 平台版本 | 支持 |
| --- | --- | --- | --- | --- |
| 已加入混合 Azure AD | 联合* | 视窗电流* 和 Windows 下级* | 永久 | 是 |
|   |   | 窗口当前 | 非持久性 | 否 |
|   |   | Windows 下层设备 | 非持久性 | 是 |
|   | 托管* | 窗口当前和窗口下级 | 永久 | 是 |
|   |   | 窗口当前 | 非持久性 | 否 |
|   |   | Windows 下层设备 | 非持久性 | 是 |
| 已加入 Azure AD | 联合 | 窗口当前 | 永久 | 否 |
|   |   |   | 非持久性 | 否 |
|   | 托管 | 窗口当前 | 永久 | 否 |
|   |   |   | 非持久性 | 否 |
| 已注册 Azure AD | 联合 | 窗口当前 | 永久 | 否 |
|   |   |   | 非持久性 | 否 |
|   | 托管 | 窗口当前 | 永久 | 否 |
|   |   |   | 非持久性 | 否 |

\***联合**标识基础结构环境表示具有标识提供程序（如 AD FS 或其他第三方 IDP）的环境。

\*\***托管**标识基础结构环境表示具有 Azure AD 的环境，该环境是使用[密码哈希同步 （PHS）](../hybrid/whatis-phs.md)或具有[无缝单一登录的](../hybrid/how-to-connect-sso.md)[直通身份验证 （PTA）](../hybrid/how-to-connect-pta.md)部署的标识提供程序。

\*\*\***Windows 当前**设备表示 Windows 10、Windows 服务器 2016 和 Windows 服务器 2019。

\*\*\*\***Windows 低级**设备表示 Windows 7、Windows 8.1、Windows 服务器 2008 R2、Windows 服务器 2012 和 Windows 服务器 2012 R2。 有关 Windows 7 的支持信息，请参阅[对 Windows 7 的支持即将结束](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)。 有关 Windows 服务器 2008 R2 的支持信息，请参阅[为 Windows 服务器 2008 结束支持做好准备](https://www.microsoft.com/cloud-platform/windows-server-2008)。

## <a name="microsofts-guidance"></a>微软指南

管理员应参考以下文章（基于其标识基础结构）来了解如何配置混合 Azure AD 联接。

- [为联合环境配置混合 Azure 活动目录联接](hybrid-azuread-join-federated-domains.md)
- [为托管环境配置混合 Azure 活动目录联接](hybrid-azuread-join-managed-domains.md)

如果依赖于系统准备工具 （sysprep.exe），并且使用 Windows 10 1809 前映像进行安装，请确保映像不是来自已注册为混合 Azure AD 加入的设备。

如果依赖于虚拟机 （VM） 快照来创建其他 VM，请确保快照不是来自已在 Azure AD 注册为混合 Azure AD 联接的 VM。

部署非持久性 VDI 时，IT 管理员应密切关注 Azure AD 中陈旧设备的管理。 Microsoft 建议 IT 管理员实施以下指南。 否则将导致目录具有大量从非持久性 VDI 平台注册的陈旧混合 Azure AD 加入设备。

- 创建并使用指示桌面为基于 VDI 的计算机显示名称的前缀。
- 作为注销脚本的一部分实现以下命令。 此命令将触发对 Azure AD 的尽最大努力调用以删除设备。
   - 对于 Windows 低级设备 – 自动工作区.exe /离开
- 定义和实现[管理陈旧设备的](manage-stale-devices.md)流程。
   - 制定识别非持久性混合 Azure AD 联接设备的策略后，可以更积极地清理这些设备，以确保目录不会被大量陈旧的设备所使用。
 
## <a name="next-steps"></a>后续步骤

[为联合环境配置混合 Azure 活动目录联接](hybrid-azuread-join-federated-domains.md)
