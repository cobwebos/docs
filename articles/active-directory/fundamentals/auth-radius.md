---
title: Azure Active Directory 的 RADIUS 身份验证
description: 实现此身份验证模式的体系结构指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff6210741d87602b4f695633b11d2641a6bb6781
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114163"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Azure Active Directory 的 RADIUS 身份验证

远程身份验证拨入用户服务 (RADIUS) 是一种网络协议，它通过对拨入用户启用集中式身份验证和授权来保护网络。 许多应用程序仍然依赖于 RADIUS 协议来对用户进行身份验证。

Microsoft Windows Server 具有名为网络策略服务器 (NPS) 的角色，该角色可充当 RADIUS 服务器并支持 RADIUS 身份验证。

Azure Active Directory (Azure AD) 启用基于 RADIUS 的系统的多重身份验证。 如果客户想要将 Azure 多重身份验证应用到前面提到的任何 RADIUS 工作负荷，他们可以在其 Windows NPS 服务器上安装 Azure 多重身份验证 NPS 扩展。 

Windows NPS 服务器会根据 Active Directory 对用户的凭据进行身份验证，然后将多重身份验证请求发送到 Azure。 然后，用户在其移动身份验证器上收到质询。 成功后，将允许客户端应用程序连接到该服务。 

## <a name="usewhen"></a>何时使用： 

需要向应用程序添加多重身份验证，如
*  (VPN) 的虚拟专用网络
* WiFi 访问
* 远程桌面网关 (RDG) 
* 虚拟桌面基础结构 (VDI)
* 依赖于 RADIUS 协议的任何其他人对服务中的用户进行身份验证。 

> [!NOTE]
> 建议将 VPN 升级为 SAML，并使用 Azure AD 直接联合 VPN，而不是依靠 RADIUS 和 Azure 多重身份验证 NPS 扩展将 Azure 多重身份验证应用于 VPN 工作负荷。 这为你的 VPN 提供了全面的 Azure AD 保护，包括条件访问、多重身份验证、设备相容性和标识保护。

![体系结构图](./media/authentication-patterns/radius-auth.png)


## <a name="componentsofthe-system"></a>系统的组件 

* **客户端应用程序 (VPN 客户端) **：向 RADIUS 客户端发送身份验证请求。

* **RADIUS 客户端**：转换来自客户端应用程序的请求，并将其发送到安装了 NPS 扩展的 RADIUS 服务器。

* **Radius 服务器**：连接 Active Directory 以执行 RADIUS 请求的主要身份验证。 成功后，会将请求传递到 Azure 多重身份验证 NPS 扩展。

* **NPS 扩展**：触发对 Azure 多重身份验证进行辅助身份验证的请求。 如果成功，NPS 扩展将向 RADIUS 服务器提供包含由 Azure 的安全令牌服务颁发的多重身份验证声明的安全令牌，从而完成身份验证请求。

* **Azure 多重身份验证**：与 Azure AD 通信以检索用户的详细信息，并使用用户配置的验证方法执行辅助身份验证。

## <a name="implementradiuswith-azure-ad"></a>通过 Azure AD 实现 RADIUS 

* [使用 NPS 提供 Azure 多重身份验证功能](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension) 

* [配置 Azure 多重身份验证 NPS 扩展](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-advanced) 

* [使用 NPS 扩展进行 Azure 多重身份验证的 VPN](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-vpn) 

  
‎ 

 
