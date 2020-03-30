---
title: 了解 Azure AD 连接 1.4.xx.x 和设备消失 |微软文档
description: 本文档介绍 Azure AD 连接版本 1.4.xx.x 中出现的问题
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176020"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>了解 Azure AD 连接 1.4.xx.x 和设备消失
使用 Azure AD Connect 的版本 1.4.xx.x，某些客户可能会看到其部分或全部 Windows 设备从 Azure AD 中消失。 这不应该担心，因为在进行条件访问授权期间，Azure AD 不使用这些设备标识。 此更改不会删除在 Azure AD 中正确注册用于混合 Azure AD 联接的任何 Windows 设备。

如果看到 Azure AD 中的设备对象的删除超过导出删除阈值，则建议客户允许删除。 [操作操作：允许删除在超过删除阈值时流动](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>背景
注册为混合 Azure AD 加入的 Windows 设备在 Azure AD 中表示为设备对象。 这些设备对象可用于条件访问。 Windows 10 设备通过 Azure AD 连接同步到云，下级 Windows 设备直接使用 AD FS 或无缝单一登录注册。

## <a name="windows-10-devices"></a>Windows 10 设备
只有具有由混合 Azure AD 联接配置的特定用户证书属性值的 Windows 10 设备才应通过 Azure AD 连接同步到云。 在早期版本的 Azure AD Connect 中，此要求未严格执行，从而导致 Azure AD 中不必要的设备对象。 Azure AD 中的此类设备始终处于"挂起"状态，因为这些设备不打算在 Azure AD 中注册。 

此版本的 Azure AD 连接将仅同步正确配置为混合 Azure AD 加入的 Windows 10 设备。 没有 Azure AD 加入特定用户证书的 Windows 10 设备对象将从 Azure AD 中删除。

## <a name="down-level-windows-devices"></a>低级 Windows 设备
Azure AD 连接不应同步[下级 Windows 设备](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)。 以前未正确同步的 Azure AD 中的任何设备现在都将从 Azure AD 中删除。 如果 Azure AD Connect 尝试删除[低级 Windows 设备](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)，则该设备不是 Microsoft[工作区为非 Windows 10 计算机 MSI](https://www.microsoft.com/download/details.aspx?id=53554)创建的设备，并且它无法被任何其他 Azure AD 功能使用。

某些客户可能需要重新访问["如何操作：计划混合 Azure 活动目录加入实现](../devices/hybrid-azuread-join-plan.md)"，以便正确注册其 Windows 设备，并确保此类设备可以完全参与基于设备的条件访问。 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>如何验证使用此更新删除哪些设备？

要验证已删除哪些设备，可以使用此 PowerShell 脚本：https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

此脚本生成有关存储在 Active Directory 计算机对象中的证书的报告，特别是混合 Azure AD 联接功能颁发的证书。
它检查 AD 中计算机对象的 User 证书属性中的证书，并且对于存在的每个未过期证书，验证证书是否为混合 Azure AD 联接功能颁发（即主题名称与 CN[ObjectGUID]匹配）。
以前，Azure AD Connect 将同步到 Azure AD 任何至少包含一个有效证书但从 Azure AD Connect 版本 1.4 开始的计算机，同步引擎可以标识混合 Azure AD 联接证书，并将"云筛选"计算机对象从同步到 Azure AD，除非有有效的混合 Azure AD 联接证书。
已同步到 AD 但没有有效混合 Azure AD 联接证书的 Azure AD 设备将由同步引擎删除（云筛选=TRUE）。

## <a name="next-steps"></a>后续步骤
- [Azure AD 连接版本历史记录](reference-connect-version-history.md)
