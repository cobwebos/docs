---
title: 使用 Azure AD 域服务对域加入进行故障排除 | Microsoft Docs
description: 了解如何解决出现以下情况时遇到的常见问题：尝试将 VM 加入域或将应用程序连接到 Azure Active Directory 域服务，而无法连接到托管域或通过其身份验证。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 4a472f0d1e31faea6b62eec004543b42e6add4fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039681"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>使用 Azure Active Directory 域服务托管域对域加入问题进行故障排除

尝试将虚拟机 (VM) 加入到（或将应用程序连接到）Azure Active Directory 域服务 (Azure AD DS) 托管域时，可能会收到错误，表示无法实现此操作。 若要对域加入问题进行故障排除，请查看以下有问题的相应点：

* 如果未收到身份验证提示，则 VM 或应用程序将无法连接到 Azure AD DS 托管域。
    * 开始对[域加入的连接性问题](#connectivity-issues-for-domain-join)进行故障排除。
* 如果在身份验证过程中收到错误，则说明成功连接到了托管域。
    * 开始对[域加入期间与凭据相关的问题](#credentials-related-issues-during-domain-join)进行故障排除。

## <a name="connectivity-issues-for-domain-join"></a>域加入的连接问题

如果 VM 找不到托管域，则通常存在网络连接或配置问题。 查看以下故障排除步骤，找到并解决问题：

1. 确保将 VM 连接到托管域的同一虚拟网络或对等虚拟网络。 否则，VM 无法找到并连接到域，因此无法加入域。
    * 如果 VM 未连接到同一虚拟网络，请确认虚拟网络对等互连或 VPN 连接处于“活动”或“已连接”状态，以允许流正确流动 。
1. 请尝试使用托管域的域名 ping 该域，例如 `ping aaddscontoso.com`。
    * 如果 ping 响应失败，请尝试 ping 托管域门户概述页中显示的域的 IP 地址，例如 `ping 10.0.0.4`。
    * 如果能够 ping 通该 IP 地址，但无法 ping 通域，则表示 DNS 的配置可能不正确。 请确保已[为虚拟网络配置托管域 DNS 服务器][configure-dns]。
1. 请尝试刷新虚拟机上的 DNS 解析程序缓存，例如 `ipconfig /flushdns`。

### <a name="network-security-group-nsg-configuration"></a>网络安全组 (NSG) 配置

创建托管域时，还会以相应的规则创建网络安全组以实现成功的域操作。 如果编辑或创建其他网络安全组规则，则可能会无意间阻止 Azure AD DS 提供连接和身份验证服务所需的端口。 这些网络安全组规则可能导致出现问题，例如密码同步无法完成、用户无法登录或域加入问题。

如果仍然遇到连接问题，请查看以下故障排除步骤：

1. 在 Azure 门户中检查托管域的运行状况。 如果存在 AADDS001 警报，则表示网络安全组规则正在阻止访问。
1. 查看[所需端口和网络安全组规则][network-ports]。 确保未向用于连接的 VM 或虚拟网络应用会阻止这些网络端口的网络安全组规则。
1. 解决所有网络安全组配置问题后，AADDS001 警报将在约 2 小时内从运行状况页中消失。 现在网络连接可用，请尝试再次将该 VM 加入域。

## <a name="credentials-related-issues-during-domain-join"></a>域加入期间与凭据相关的问题

如果出现要求输入凭据以加入托管域的对话框，则 VM 可以使用 Azure 虚拟网络连接到该域。 域加入流程在向域进行身份验证时失败，或在使用提供的凭据授权完成域加入流程时失败。

若要对与凭据相关的问题进行故障排除，请查看以下故障排除步骤：

1. 请尝试使用 UPN 格式指定凭据。例如 `dee@contoso.onmicrosoft.com`。 请确保在 Azure AD 中正确配置了此 UPN。
    * 如果租户中有多个用户具有相同的 UPN 前缀，或者 UPN 前缀过长，系统可能会自动生成帐户的 SAMAccountName。 因此，帐户的 SAMAccountName 格式可能不同于所需的格式或者在本地域中使用的格式。
1. 尝试使用属于托管域的用户帐户的凭据将 VM 加入托管域。
1. 请确保[启用了密码同步][enable-password-sync]，并确保为完成初始密码同步等待了足够长的时间。

## <a name="next-steps"></a>后续步骤

若要更深入地了解域加入操作中的 Active Directory 过程，请参阅[加入和身份验证问题][join-authentication-issues]。

如果在将 VM 加入托管域时仍有问题，请[查找帮助并创建 Azure Active Directory 的支持票证][azure-ad-support]。

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
