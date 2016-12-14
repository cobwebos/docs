---
title: "使用 Azure Multi-Factor Authentication 与第三方 VPN 的高级方案"
description: "本页提供有关 Azure MFA 与第三方产品的分步设置配置的信息。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: 1f94a214-d6f6-48a8-8a12-006b5896ae45
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c7855ad2aa64000c7defd7b0df55fecf9f7ad53a


---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-3rd-party-vpn"></a>使用 Azure Multi-Factor Authentication 与第三方 VPN 的高级方案
Azure Multi-Factor Authentication 可用于与各种第三方 VPN 解决方案无缝连接。  这包括 Cisco® ASA VPN 设备、Citrix NetScaler SSL VPN 设备和 Juniper Networks Secure Access/Pulse Secure Connect Secure SSL VPN 设备。

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Cisco ASA VPN 设备和 Azure Multi-Factor Authentication
Azure Multi-Factor Authentication 可以与 Cisco® ASA VPN 设备无缝集成，以便为 Cisco AnyConnect® VPN 登录和门户访问提供更高的安全性。  可以使用 LDAP 或 RADIUS 协议来实现此目的。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 说明 |
| --- | --- |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) |使用 LDAP 将 Cisco ASA VPN 设备与 Azure MFA 无缝集成 |
| [Cisco ASA with Anyconnect VPN 与 Azure MFA Configuration for RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) |使用 RADIUS 将 Cisco ASA VPN 设备与 Azure MFA 无缝集成 |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN 与 Azure Multi-Factor Authentication
Azure Multi-Factor Authentication 可以与 Citrix NetScaler SSL VPN 设备无缝集成，以便为 Citrix NetScaler SSL VPN 登录和门户访问提供更高的安全性。  可以使用 LDAP 或 RADIUS 协议来实现此目的。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 说明 |
| --- | --- |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) |使用 LDAP 将 Citrix NetScaler SSL VPN 与 Azure MFA 设备无缝集成 |
| [Citrix NetScaler SSL VPN 与 Azure MFA Configuration for RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) |使用 RADIUS 将 Citrix NetScaler SSL VPN 设备与 Azure MFA 无缝集成 |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Juniper/Pulse Secure SSL VPN 设备与 Azure Multi-Factor Authentication
Azure Multi-Factor Authentication 可以与 Juniper/Pulse Secure SSL VPN 设备无缝集成，以便为 Juniper/Pulse Secure SSL VPN 登录和门户访问提供更高的安全性。  可以使用 LDAP 或 RADIUS 协议来实现此目的。  选择下列其中一项以下载详细的分步配置指南。

| 配置指南 | 说明 |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) |使用 LDAP 将 Juniper/Pulse Secure SSL VPN 与 Azure MFA 设备无缝集成 |
| [Juniper/Pulse Secure SSL VPN 与 Azure MFA Configuration for RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) |使用 RADIUS 将 Juniper/Pulse Secure SSL VPN 设备与 Azure MFA 无缝集成 |




<!--HONumber=Nov16_HO3-->


