---
title: Azure MFA 服务器和第三方 VPN - Azure 活动目录
description: Azure MFA 服务器的分步配置指南，以便与思科、Citrix 和瞻联网络集成。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01decb99a9eb24ae60250f83f1f961b4c1690bc0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652848"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>使用 Azure MFA 服务器和第三方 VPN 解决方案的高级方案

Azure 多重身份验证服务器（Azure MFA 服务器）可用于与各种第三方 VPN 解决方案无缝连接。 本文重点介绍 Cisco&reg; ASA VPN 设备、Citrix NetScaler SSL VPN 设备以及瞻博网络安全访问/脉冲安全连接安全 SSL VPN 设备。 我们创建了配置指南来处理这三个常用设备。 Azure MFA 服务器还可以与大多数使用 RADIUS、LDAP、IIS 或基于声明的 AD FS 身份验证的其他系统集成。 您可以在[Azure MFA 服务器配置中找到](howto-mfaserver-deploy.md#next-steps)更多详细信息。

> [!IMPORTANT]
> 自 2019 年 7 月 1 日起，Microsoft 将不再为新部署提供 MFA 服务器。 希望用户进行多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 在 7 月 1 日之前激活 MFA 服务器的现有客户将能够像往常一样下载最新版本、将来的更新并生成激活凭据。

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>思科 ASA VPN 设备和 Azure MFA 服务器
Azure MFA 服务器与您的 Cisco&reg; ASA VPN 设备集成，为思科&reg;AnyConnect VPN 登录和门户访问提供额外的安全性。  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 说明 |
| --- | --- |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Cisco ASA VPN 设备与 Azure MFA 集成 |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Cisco ASA VPN 设备与 Azure MFA 集成 |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN 和 Azure MFA 服务器
Azure MFA 服务器与您的 Citrix NetScaler SSL VPN 设备集成，为 Citrix NetScaler SSL VPN 登录和门户访问提供额外的安全性。  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 说明 |
| --- | --- |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Citrix NetScaler SSL VPN 与 Azure MFA 设备集成 |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Citrix NetScaler SSL VPN 设备与 Azure MFA 集成 |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>瞻比器/脉冲安全 SSL VPN 设备和 Azure MFA 服务器
Azure MFA 服务器与您的瞻飞器/脉冲安全 SSL VPN 设备集成，为瞻飞器/脉冲安全 SSL VPN 登录和门户访问提供额外的安全性。  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 说明 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Juniper/Pulse Secure SSL VPN 与 Azure MFA 设备集成 |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Juniper/Pulse Secure SSL VPN 设备与 Azure MFA 集成 |

## <a name="next-steps"></a>后续步骤

- [通过适用于 Azure 多重身份验证的 NPS 扩展增强现有的身份验证基础结构](howto-mfa-nps-extension.md)

- [配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)
