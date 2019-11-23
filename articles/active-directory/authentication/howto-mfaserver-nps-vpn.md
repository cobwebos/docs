---
title: Azure MFA Server and third-party VPNs - Azure Active Directory
description: Step-by-step configuration guides for Azure MFA Server to integrate with Cisco, Citrix, and Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50dc1d11b2a642e6569a74cb9052e09663f333d2
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404095"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Advanced scenarios with Azure MFA Server and third-party VPN solutions

Azure Multi-Factor Authentication Server (Azure MFA Server) can be used to seamlessly connect with various third-party VPN solutions. 本文重点介绍 Cisco® ASA VPN 设备、Citrix NetScaler SSL VPN 设备和 Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN 设备。 我们创建了配置指南来处理这三个常用设备。 Azure MFA Server can also integrate with most other systems that use RADIUS, LDAP, IIS, or claims-based authentication to AD FS. You can find more details in [Azure MFA Server configurations](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> As of July 1, 2019, Microsoft will no longer offer MFA Server for new deployments. New customers who would like to require multi-factor authentication from their users should use cloud-based Azure Multi-Factor Authentication. Existing customers who have activated MFA Server prior to July 1 will be able to download the latest version, future updates and generate activation credentials as usual.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN appliance and Azure MFA Server
Azure MFA Server integrates with your Cisco® ASA VPN appliance to provide additional security for Cisco AnyConnect® VPN logins and portal access.  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 描述 |
| --- | --- |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Cisco ASA VPN 设备与 Azure MFA 集成 |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Cisco ASA VPN 设备与 Azure MFA 集成 |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN and Azure MFA Server
Azure MFA Server integrates with your Citrix NetScaler SSL VPN appliance to provide additional security for Citrix NetScaler SSL VPN logins and portal access.  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 描述 |
| --- | --- |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Citrix NetScaler SSL VPN 与 Azure MFA 设备集成 |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Citrix NetScaler SSL VPN 设备与 Azure MFA 集成 |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN appliance and Azure MFA Server
Azure MFA Server integrates with your Juniper/Pulse Secure SSL VPN appliance to provide additional security for Juniper/Pulse Secure SSL VPN logins and portal access.  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 描述 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Juniper/Pulse Secure SSL VPN 与 Azure MFA 设备集成 |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Juniper/Pulse Secure SSL VPN 设备与 Azure MFA 集成 |

## <a name="next-steps"></a>后续步骤

- [通过适用于 Azure 多重身份验证的 NPS 扩展增强现有的身份验证基础结构](howto-mfa-nps-extension.md)

- [配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)
