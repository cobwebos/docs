---
title: 了解 Azure AD Connect 1.4. x. x 和设备消失 |Microsoft Docs
description: 本文档描述了版本 1.4. x Azure AD Connect 的问题
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: b0ef3dd2f39802d07c4ae04ad1eca23e40db502a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345498"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>了解 Azure AD Connect 1.4. x. x 和设备消失
Azure AD Connect 的版本 1.4. x，某些客户可能会看到其所有 Windows 设备从 Azure AD 中消失。 这不是问题的原因，因为在条件访问授权期间 Azure AD 不会使用这些设备标识。 此更改不会删除为混合 Azure AD 联接正确注册 Azure AD 的任何 Windows 设备。

如果在 Azure AD 超过导出删除阈值时看到删除了设备对象，则建议客户允许删除操作。 [如何：允许删除超出删除阈值时流动](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>后台
注册为混合 Azure AD 的 Windows 设备在 Azure AD 中表示为设备对象。 这些设备对象可用于条件性访问。 Windows 10 设备通过 Azure AD Connect 同步到云，下一级 Windows 设备是使用 AD FS 或无缝单一登录直接注册的。

## <a name="windows-10-devices"></a>Windows 10 设备
只应 Azure AD Connect 将具有混合 Azure AD 联接配置的特定 userCertificate 属性值的 Windows 10 设备同步到云。 在的早期版本中 Azure AD Connect 未严格实施此要求，导致 Azure AD 中不必要的设备对象。 Azure AD 中的此类设备始终处于 "挂起" 状态，因为这些设备不打算注册到 Azure AD 中。 

此版本的 Azure AD Connect 将只同步正确配置为混合 Azure AD 加入的 Windows 10 设备。 将从 Azure AD 中删除没有 Azure AD 联接特定 userCertificate 的 Windows 10 设备对象。

## <a name="down-level-windows-devices"></a>下层 Windows 设备
Azure AD Connect 不得同步[下层 Windows 设备](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)。 将从 Azure AD 中删除之前未正确同步 Azure AD 中的任何设备。 如果 Azure AD Connect 尝试删除[下层 windows 设备](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)，则该设备不是由[Microsoft Workplace Join 为非 WINDOWS 10 计算机 MSI](https://www.microsoft.com/download/details.aspx?id=53554)创建的，并且不能被任何其他 Azure AD 功能使用。

某些客户可能需要重新访问[如何：计划混合 Azure Active Directory 联接实现](../devices/hybrid-azuread-join-plan.md) ，使其 Windows 设备正确注册，并确保此类设备可以完全参与基于设备的条件访问。 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>如何验证哪些设备已通过此更新删除？

若要验证已删除的设备，你可以使用以下 PowerShell 脚本： https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

此脚本将生成一个报表，该报表介绍 Active Directory 计算机对象中存储的证书，尤其是混合 Azure AD 联接功能颁发的证书。
它将检查 AD 中计算机对象的 UserCertificate 属性中存在的证书，并为每个未过期的证书提供验证是否为混合 Azure AD 联接功能颁发了证书（例如，使用者名称与 CN = {ObjectGUID} 匹配）。
之前，Azure AD Connect 将同步到至少包含一个有效证书但从 Azure AD Connect 版本1.4 开始的任何计算机 Azure AD，同步引擎可以识别混合 Azure AD 联接证书，并将 "cloudfilter"除非有有效的混合 Azure AD join 证书，否则计算机对象不会同步到 Azure AD。
同步引擎将删除已同步到 AD 但没有有效混合 Azure AD 联接证书的 Azure AD 设备（CloudFiltered = TRUE）。

## <a name="next-steps"></a>后续步骤
- [Azure AD Connect 版本历史记录](reference-connect-version-history.md)
