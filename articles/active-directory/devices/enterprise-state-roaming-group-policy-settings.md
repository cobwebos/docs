---
title: ESR 的组策略和 MDM 设置-Azure Active Directory
description: 企业状态漫游的管理设置
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdffbc3a140bd13dcd6d352db8c192803d39b03e
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672368"
---
# <a name="group-policy-and-mdm-settings"></a>组策略和 MDM 设置

仅在公司自有设备上使用这些组策略和移动设备管理 (MDM) 设置，因为这些策略将应用于用户的整个设备。 应用 MDM 策略禁用个人设备和用户自有设备的设置同步，这会对该设备的使用产生负面影响。 此外，设备上的其他用户帐户也将受到该策略的影响。

想要为个人（非托管）设备管理漫游的企业可以使用 Azure 门户来启用或禁用漫游，而不要使用组策略或 MDM。
下表描述了可用的策略设置。

> [!NOTE]
> 本文适用于在7月2015中通过 Windows 10 启动的 Microsoft Edge 旧版 HTML 浏览器。 本文不适用于2020年1月15日发布的新的基于 Chromium 的 Microsoft Edge 浏览器。 有关新 Microsoft Edge 的同步行为的详细信息，请参阅[Microsoft Edge 同步](/deployedge/microsoft-edge-enterprise-sync)文章。

## <a name="mdm-settings"></a>MDM 设置

MDM 策略设置适用于 Windows 10 和 Windows 10 移动版。  Windows 10 移动版支持仅适用于通过用户的 OneDrive 帐户进行的基于 Microsoft 帐户的漫游。 有关支持 Azure AD 的同步的设备的详细信息，请参阅[设备和终结点](enterprise-state-roaming-windows-settings-reference.md)。

| 名称 | 说明 |
| --- | --- |
| 允许 Microsoft 帐户连接 |允许用户使用设备上的 Microsoft 帐户进行身份验证 |
| 允许同步我的设置 |允许用户漫游 Windows 设置和应用数据；停用此政策会停用移动设备上的同步和备份 |

## <a name="group-policy-settings"></a>组策略设置

组策略设置适用于已加入 Active Directory 域的 Windows 10 设备。 此表还包括会显示为管理同步设置，但不适用于 Windows 10 企业状态漫游的旧设置，这些设置在说明中标有“请勿使用”。

这些设置位于以下位置：`Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| 名称 | 说明 |
| --- | --- |
| 帐户：阻止 Microsoft 帐户 |此策略设置阻止用户在此计算机上添加新的 Microsoft 帐户 |
| 不同步 |防止用户漫游 Windows 设置和应用数据 |
| 不同步个性化 |禁止同步“主题”组 |
| 不同步浏览器设置 |禁用 Internet Explorer 组的同步 |
| 不同步密码 |禁止同步“密码”组 |
| 不同步其他 Windows 设置 |禁止同步“其他 Windows 设置”组 |
| 不同步桌面个性化 |请勿使用；不起作用 |
| 不同步按流量计费的连接 |禁止通过按流量计费的连接（例如 3G 手机网络）进行漫游 |
| 不同步应用 |请勿使用；不起作用 |
| 不同步应用设置 |禁止漫游应用数据 |
| 不同步启动设置 |请勿使用；不起作用 |

## <a name="next-steps"></a>后续步骤

有关概述，请参阅[企业状态漫游概述](enterprise-state-roaming-overview.md)。
