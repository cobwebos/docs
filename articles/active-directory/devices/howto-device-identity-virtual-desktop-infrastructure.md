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
ms.openlocfilehash: 1a1cba2c4572b2f898f631aefbbf316fae1195ac
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596363"
---
# <a name="device-identity-and-desktop-virtualization"></a>设备标识和桌面虚拟化

管理员通常会在其组织中部署托管 Windows 操作系统的虚拟桌面基础结构（VDI）平台。 管理员将 VDI 部署到：

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

| 设备标识类型 | 标识基础结构 | Windows 设备 | VDI 平台版本 | 受支持 |
| --- | --- | --- | --- | --- |
| 已加入混合 Azure AD | 联合 | Windows 当前 * * * 和 Windows 下层 * * * * | 永久 | 是 |
|   |   |   | 非持久 | 是 |
|   | 托管 * * | Windows 当前和 Windows 下层 | 永久 | 是 |
|   |   | Windows 下层设备 | 非持久 | 是 |
|   |   | Windows 当前 | 非持久 | No |
| 已加入 Azure AD | 联合 | Windows 当前 | 永久 | No |
|   |   |   | 非持久 | No |
|   | 托管 | Windows 当前 | 永久 | No |
|   |   |   | 非持久 | No |
| 已注册 Azure AD | 联合 | Windows 当前 | 永久 | No |
|   |   |   | 非持久 | No |
|   | 托管 | Windows 当前 | 永久 | No |
|   |   |   | 非持久 | No |

\***联合**标识基础结构环境表示具有标识提供者的环境，如 AD FS 或其他第三方 IDP。

\* \***托管**标识基础结构环境表示一个环境，Azure AD 作为使用[密码哈希同步（PHS）](../hybrid/whatis-phs.md)或[传递身份验证（PTA）](../hybrid/how-to-connect-pta.md) [部署的标识提供者无缝单一登录](../hybrid/how-to-connect-sso.md)。

\* \* \* **windows 当前**设备表示 windows 10、windows server 2016 和 windows server 2019。

\* \* \* \* **windows 下层**设备表示 windows 7、Windows 8.1、windows Server 2008 R2、windows server 2012 和 windows Server 2012 R2。 有关 Windows 7 的支持信息，请参阅对[windows 7 的支持正在结束](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)。 有关 Windows Server 2008 R2 的支持信息，请参阅[准备 Windows server 2008 终止支持](https://www.microsoft.com/cloud-platform/windows-server-2008)。

## <a name="microsofts-guidance"></a>Microsoft 指南

管理员应根据其标识基础结构参考以下文章，以了解如何配置混合 Azure AD 联接。

- [为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
- [为托管环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)

如果你依赖于系统准备工具（sysprep.inf），并且如果你使用的是 Windows 之前 10 1809 映像进行安装，请确保映像不是来自已在 Azure AD 中注册的设备，因为已加入混合 Azure AD。

如果你依赖于虚拟机（VM）快照来创建其他 Vm，请确保快照不是来自已注册到 Azure AD 的 VM，因为混合 Azure AD 加入。

部署非持久性 VDI 时，IT 管理员应密切关注如何管理 Azure AD 中的过时设备。 Microsoft 建议 IT 管理员执行以下指导。 如果不这样做，会导致目录中有大量过时混合 Azure AD 联接的设备，这些设备已从非持久性 VDI 平台注册。

- 为计算机的显示名称创建并使用一个前缀，将桌面指定为基于 VDI 的计算机。
- 在注销脚本中实现以下命令。 这些命令将触发对 Azure AD 删除设备的尽力操作调用。
   - 对于 Windows 当前设备– dsregcmd.exe/leave
   - 对于 Windows 下层设备–了 autoworkplace.exe/leave
- 定义并实现[管理过时设备](manage-stale-devices.md)的过程。
   - 一旦你有策略来识别你的非持久性混合 Azure AD 联接设备，你就可以更积极地在这些设备上进行清理，以确保你的目录不会被大量过时设备使用。
 
## <a name="next-steps"></a>后续步骤

[为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
