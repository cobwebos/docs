---
title: 设备标识和桌面虚拟化-Azure Active Directory
description: 了解如何结合使用 VDI 和 Azure AD 设备标识
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
ms.openlocfilehash: 0dde9d8b50233c3c4033daf618e0e626c0174b0c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903147"
---
# <a name="device-identity-and-desktop-virtualization"></a>设备标识和桌面虚拟化

管理员通常会将虚拟桌面基础结构 (VDI) 平台部署在其组织中。 管理员将 VDI 部署到：

- 简化管理。
- 通过整合和集中资源降低成本。
- 提供最终用户的移动性，随时随地在任何设备上随时随地访问虚拟机。

虚拟桌面有两种主要类型：

- 永久
- 非永久

持久性版本为每个用户或用户池使用唯一的桌面映像。 可以自定义这些唯一的桌面并保存，供将来使用。 

非持久性版本使用桌面集合，用户可以根据需要对其进行访问。 在用户注销后，这些非持久性桌面将恢复为其原始状态。

本文将介绍 Microsoft 有关设备标识和 VDI 支持的指导。 有关设备标识的详细信息，请参阅[什么是设备标识一](overview.md)文。

## <a name="supported-scenarios"></a>支持的方案

在为 VDI 环境 Azure AD 中配置设备标识之前，请先熟悉受支持的方案。 下表说明了支持的预配方案。 在此上下文中进行预配意味着管理员可以大规模配置设备标识，无需任何最终用户交互。

| 设备标识类型 | 标识基础结构 | Windows 设备 | VDI 平台版本 | 支持 |
| --- | --- | --- | --- | --- |
| 已加入混合 Azure AD | 联合 | Windows 当前 * * * 和 Windows 下层 * * * * | 永久 | 是 |
|   |   | Windows 当前 | 非持久 | 否 |
|   |   | Windows 下层设备 | 非持久 | 是 |
|   | 托管 * * | Windows 当前和 Windows 下层 | 永久 | 是 |
|   |   | Windows 当前 | 非持久 | 否 |
|   |   | Windows 下层设备 | 非持久 | 是 |
| 已加入 Azure AD | 联合 | Windows 当前 | 永久 | 否 |
|   |   |   | 非持久 | 否 |
|   | 托管 | Windows 当前 | 永久 | 否 |
|   |   |   | 非持久 | 否 |
| 已注册 Azure AD | 联合 | Windows 当前 | 永久 | 不适用 |
|   |   |   | 非持久 | 不适用 |
|   | 托管 | Windows 当前 | 永久 | 不适用 |
|   |   |   | 非持久 | 不适用 |

\***联合**标识基础结构环境表示具有标识提供者（如 AD FS 或其他第三方 IDP）的环境。

\*\***托管**标识基础结构环境表示一个环境，该环境的 Azure AD 为使用[密码哈希同步 (PHS) ](../hybrid/whatis-phs.md)或通过[无缝单一登录](../hybrid/how-to-connect-sso.md) [ (PTA) ](../hybrid/how-to-connect-pta.md)部署的标识提供者。

\*\*\***Windows 当前**设备表示 windows 10、windows server 2016 和 windows server 2019。

\*\*\*\***Windows 下层**设备表示 windows 7、Windows 8.1、windows Server 2008 R2、windows server 2012 和 windows Server 2012 R2。 有关 Windows 7 的支持信息，请参阅对[windows 7 的支持正在结束](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)。 有关 Windows Server 2008 R2 的支持信息，请参阅[准备 Windows server 2008 终止支持](https://www.microsoft.com/cloud-platform/windows-server-2008)。

## <a name="microsofts-guidance"></a>Microsoft 指南

管理员应根据其标识基础结构参考以下文章，以了解如何配置混合 Azure AD 联接。

- [为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
- [为托管环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)

如果你依赖于系统准备工具 ( # A0) 并且你使用 Windows 之前的 Windows 10 1809 映像进行安装，请确保映像不是从已注册 Azure AD 为已加入混合 Azure AD 的设备进行的。

如果依赖于虚拟机 (VM) 快照来创建其他 Vm，请确保快照不是从已注册 Azure AD 作为混合 Azure AD 联接的 VM 使用。

Windows 当前版本不支持非持久 VDI 混合 Azure AD 联接。 为 Windows 下层部署非持久性 VDI 时，IT 管理员应密切关注在 Azure AD 管理过时设备。 Microsoft 建议 IT 管理员执行以下指导。 如果不这样做，会导致目录中有大量过时混合 Azure AD 联接的设备，这些设备已从非持久性 VDI 平台注册。

- 为计算机的显示名称创建并使用一个前缀，将桌面指定为基于 VDI 的计算机。
- 在注销脚本中实现以下命令。 此命令将触发对 Azure AD 删除设备的尽力操作调用。
   - 对于 Windows 下层设备-autoworkplace.exe/leave
- 定义并实现[管理过时设备](manage-stale-devices.md)的过程。
   - 一旦你有策略来识别你的非持久性混合 Azure AD 联接设备，你就可以更积极地在这些设备上进行清理，以确保你的目录不会被大量过时设备使用。
 
## <a name="next-steps"></a>后续步骤

[为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
