---
title: 排查 Azure AD 域服务的域加入问题 |Microsoft Docs
description: 了解如何解决在尝试将 VM 加入域或将应用程序连接到 Azure Active Directory 域服务时遇到的常见问题，并且不能对托管域进行连接或身份验证。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 73a76c4442bb8af70168e54a294f2cb100ff653c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74703656"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>排查 Azure AD 域服务托管域的域加入问题

尝试加入虚拟机（VM）或将应用程序连接到 Azure Active Directory 域服务（AD DS）托管域时，可能会收到一条错误消息，指出无法执行此操作。 若要解决域加入问题，请查看以下哪一点有问题：

* 如果未收到身份验证提示，则 VM 或应用程序无法连接到 Azure AD DS 托管域。
    * 开始解决[域加入连接问题](#connectivity-issues-for-domain-join)。
* 如果在身份验证过程中收到错误，则连接到 Azure AD DS 托管域是成功的。
    * [在加入域的过程中，开始对与凭据相关的问题](#credentials-related-issues-during-domain-join)进行故障排除。

## <a name="connectivity-issues-for-domain-join"></a>域加入连接问题

如果 VM 找不到 Azure AD DS 托管域，则通常会出现网络连接问题或配置问题。 查看以下故障排除步骤，查找并解决问题：

1. 确保 VM 连接到为 Azure AD DS 启用的相同或对等互连虚拟网络。 如果没有，则 VM 无法找到并连接到域，因此无法加入域。
    * 如果 VM 未连接到同一个虚拟网络，请确认虚拟网络对等互连或 VPN 连接处于*活动状态*还是*已连接*，以允许流量正常流动。
1. 尝试使用 Azure AD DS 托管域的域名来 ping 域，如 `ping aadds.contoso.com`。
    * 如果 ping 响应失败，请尝试对 Azure AD DS 托管域的门户的 "概述" 页上显示的域的 IP 地址进行 ping 操作，如 `ping 10.0.0.4`。
    * 如果可以成功地对 IP 地址进行 ping 操作，但不能对域进行 ping 操作，则可能会错误地配置 DNS。 请确保已为虚拟网络配置 Azure AD DS 托管域 DNS 服务器。
1. 请尝试刷新虚拟机上的 DNS 解析程序缓存，如 `ipconfig /flushdns`。

### <a name="network-security-group-nsg-configuration"></a>网络安全组（NSG）配置

创建 Azure AD DS 托管域时，还会创建一个网络安全组，其中包含成功的域操作的相应规则。 如果编辑或创建其他网络安全组规则，则可能会无意中阻止 Azure AD DS 提供连接和身份验证服务所需的端口。 这些网络安全组规则可能会导致密码同步未完成、用户无法登录或加入域的问题。

如果仍然遇到连接问题，请查看以下故障排除步骤：

1. 检查 Azure 门户中 Azure AD DS 托管域的运行状况状态。 如果你有*AADDS001*警报，则网络安全组规则会阻止访问。
1. 查看[所需的端口和网络安全组规则][network-ports]。 请确保未将网络安全组规则应用到要连接的 VM 或虚拟网络阻止这些网络端口。
1. 解决任何网络安全组配置问题后， *AADDS001*警报将在大约2小时内从运行状况页面中消失。 现在网络连接可用，尝试将 VM 重新加入域。

## <a name="credentials-related-issues-during-domain-join"></a>域加入过程中与凭据相关的问题

如果你收到一个对话框，该对话框要求提供凭据以加入 Azure AD DS 托管域，则 VM 可以使用 Azure 虚拟网络连接到域。 域加入过程在向域进行身份验证时失败，或授权使用凭据来完成域加入过程。

若要解决与凭据相关的问题，请查看以下故障排除步骤：

1. 请尝试使用 UPN 格式指定凭据。例如 `dee@contoso.onmicrosoft.com`。 请确保在 Azure AD 中正确配置了此 UPN。
    * 如果租户中有多个用户具有相同的 UPN 前缀，或者 UPN 前缀过长，则可能会自动生成帐户的*SAMAccountName* 。 因此，你的帐户的*SAMAccountName*格式可能不同于你在本地域中的预期或使用的格式。
1. 尝试使用属于 " *AAD DC 管理员*" 组的用户帐户的凭据将 vm 加入到 Azure AD DS 托管域。
1. 请确保已[启用密码同步][enable-password-sync]并等待足够长的时间，以便初始密码同步完成。

## <a name="next-steps"></a>后续步骤

若要深入 Active Directory 了解作为加入域的操作的一部分，请参阅[联接和身份验证问题][join-authentication-issues]。

如果在将 VM 加入到 Azure AD DS 托管域时仍出现问题，请[找到 "帮助" 并为 Azure Active Directory 打开支持票证][azure-ad-support]。

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
