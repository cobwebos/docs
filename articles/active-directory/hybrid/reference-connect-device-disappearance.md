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
ms.openlocfilehash: 1ba17feea880bb55c3b4a14a06b3d803dba2350a
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316949"
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

## <a name="next-steps"></a>后续步骤
- [Azure AD Connect 版本历史记录](reference-connect-version-history.md)
