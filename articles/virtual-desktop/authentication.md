---
title: Windows 虚拟桌面身份验证-Azure
description: Windows 虚拟桌面的身份验证方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5681228e5da2708912d69f16a4b09a4a93d8bb04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89500294"
---
# <a name="supported-authentication-methods"></a>支持的身份验证方法

本文简要概述了可在 Windows 虚拟桌面中使用的身份验证类型。

## <a name="session-host-authentication"></a>会话主机身份验证

Windows 虚拟桌面支持 NT LAN Manager (NTLM) ，以及用于会话主机身份验证的 Kerberos。 但是，若要使用 Kerberos，客户端需要从域控制器上运行的密钥发行中心 (KDC) 服务获取 Kerberos 安全票证。 若要获取票证，客户端需要向域控制器直接显示一行。 你可以通过使用企业网络直接了解这一行为。 你还可以使用与企业网络的 VPN 连接。

以下是当前支持的登录方法：

- Windows 桌面客户端
    - 用户名和密码
    - 智能卡
    - Windows Hello
- Windows Store 客户端
    - 用户名和密码
- Web 客户端
    - 用户名和密码
- Android
    - 用户名和密码
- iOS
    - 用户名和密码
- macOS
    - 用户名和密码

>[!NOTE]
>智能卡和 Windows Hello 只能使用 Kerberos 进行登录。 用 Kerberos 登录需要对域控制器有线路视觉。

## <a name="hybrid-identity"></a>混合标识

Windows 虚拟桌面通过 Azure Active Directory (AD) （包括使用 Active Directory 联合身份验证服务 (ADFS) 的联合身份验证）支持 [混合标识](../active-directory/hybrid/whatis-hybrid-identity.md) 。 由于用户必须通过 Azure AD 发现，因此 Windows 虚拟桌面不支持使用 ADFS 的独立 Active Directory 部署。

## <a name="single-sign-on-sso"></a>单一登录 (SSO)

Windows 虚拟桌面目前不支持 Active Directory 联合身份验证服务 (ADFS) 用于 SSO。

避免系统提示输入会话主机凭据的唯一方法是将其保存在客户端中。 建议仅对安全设备执行此操作，以防其他用户访问资源。

## <a name="next-steps"></a>后续步骤

您是否想了解用于确保部署安全的其他方法？ 查看 [最佳安全方案](security-guide.md)。
