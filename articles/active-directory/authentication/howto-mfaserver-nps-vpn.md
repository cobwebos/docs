---
title: 使用 Azure MFA 和第三方 VPN 的高级方案
description: 用于将 Azure MFA 与 Cisco、Citrix 和 Juniper 集成的分步配置指南。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 75eb522e4576929ec2898df35a3af97d54db5d7b
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162025"
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>使用 Azure 多重身份验证和第三方 VPN 解决方案的高级方案

Azure 多重身份验证可用于与各种第三方 VPN 解决方案无缝连接。 本文重点介绍 Cisco® ASA VPN 设备、Citrix NetScaler SSL VPN 设备和 Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN 设备。 我们创建了配置指南来处理这三个常用设备。 多重身份验证服务器还可与将 RADIUS、 LDAP、 IIS 或基于声明的身份验证用于 AD FS 的大多数其他系统集成。 可以在 [MFA 服务器配置](howto-mfaserver-deploy.md#next-steps)中找到更多详细信息。

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN 设备和 Azure 多重身份验证
Azure 多重身份验证可以与 Cisco® ASA VPN 设备集成，以便为 Cisco AnyConnect® VPN 登录和门户访问提供更高的安全性。  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | Description |
| --- | --- |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Cisco ASA VPN 设备与 Azure MFA 集成 |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Cisco ASA VPN 设备与 Azure MFA 集成 |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN 与 Azure 多重身份验证
Azure 多重身份验证可以与 Citrix NetScaler SSL VPN 设备集成，以便为 Citrix NetScaler SSL VPN 登录和门户访问提供更高的安全性。  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | Description |
| --- | --- |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Citrix NetScaler SSL VPN 与 Azure MFA 设备集成 |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Citrix NetScaler SSL VPN 设备与 Azure MFA 集成 |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN 设备与 Azure 多重身份验证
Azure 多重身份验证可以与 Juniper/Pulse Secure SSL VPN 设备集成，以便为 Juniper/Pulse Secure SSL VPN 登录和门户访问提供更高的安全性。  可以使用 LDAP 或 RADIUS 协议。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | Description |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | 使用 LDAP 将 Juniper/Pulse Secure SSL VPN 与 Azure MFA 设备集成 |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | 使用 RADIUS 将 Juniper/Pulse Secure SSL VPN 设备与 Azure MFA 集成 |

## <a name="next-steps"></a>后续步骤

- [通过适用于 Azure 多重身份验证的 NPS 扩展增强现有的身份验证基础结构](howto-mfa-nps-extension.md)

- [配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)