---
title: 使用 Azure AD 域服务排除域联接故障 |微软文档
description: 了解如何在尝试域加入 VM 或将应用程序连接到 Azure 活动目录域服务且无法连接到托管域或进行身份验证时解决常见问题。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: f187dba4eace61695a72e4b7b08731e65ff0d7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299102"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>解决 Azure AD 域服务托管域的域联接问题

当您尝试加入虚拟机 （VM） 或将应用程序连接到 Azure 活动目录域服务 （AD DS） 托管域时，可能会收到无法执行此操作的错误。 要解决域加入问题，请查看以下哪些点存在问题：

* 如果未收到身份验证提示，则 VM 或应用程序无法连接到 Azure AD DS 托管域。
    * 开始解决[域联接的连接问题](#connectivity-issues-for-domain-join)。
* 如果在身份验证期间收到错误，则与 Azure AD DS 托管域的连接将成功。
    * 开始在[域加入期间解决与凭据相关的问题](#credentials-related-issues-during-domain-join)。

## <a name="connectivity-issues-for-domain-join"></a>域加入的连接问题

如果 VM 找不到 Azure AD DS 托管域，则通常存在网络连接或配置问题。 查看以下故障排除步骤以查找并解决问题：

1. 确保 VM 连接到为 Azure AD DS 启用的相同虚拟网络或对等虚拟网络。 如果没有，VM 无法查找并连接到域才能加入。
    * 如果 VM 未连接到同一虚拟网络，请确认虚拟网络对等*互连或*VPN 连接处于活动状态或*已连接*，以允许流量正确流动。
1. 尝试使用 Azure AD DS 托管域的域名（如`ping aaddscontoso.com`） 对域进行 ping。
    * 如果 ping 响应失败，请尝试 ping Azure AD DS 托管域门户概览页上显示的域的 IP 地址，例如`ping 10.0.0.4`。
    * 如果可以成功 ping IP 地址，但不能 ping 域，则 DNS 可能配置不正确。 确保已为虚拟网络配置 Azure AD DS 托管域 DNS 服务器。
1. 尝试刷新虚拟机上的 DNS 解析器缓存，例如`ipconfig /flushdns`。

### <a name="network-security-group-nsg-configuration"></a>网络安全组 （NSG） 配置

创建 Azure AD DS 托管域时，还会使用成功的域操作的适当规则创建网络安全组。 如果编辑或创建其他网络安全组规则，可能会无意中阻止 Azure AD DS 提供连接和身份验证服务所需的端口。 这些网络安全组规则可能会导致问题，如密码同步未完成、用户无法登录或域加入问题。

如果继续出现连接问题，请查看以下故障排除步骤：

1. 检查 Azure 门户中的 Azure AD DS 托管域的运行状况状态。 如果您有*AADDS001*的警报，则网络安全组规则将阻止访问。
1. 查看[所需的端口和网络安全组规则][network-ports]。 确保没有将网络安全组规则应用于从阻止这些网络端口连接的 VM 或虚拟网络。
1. 解决任何网络安全组配置问题后 *，AADDS001*警报大约在 2 小时内从运行状况页中消失。 现在可用的网络连接后，请尝试再次加入 VM。

## <a name="credentials-related-issues-during-domain-join"></a>域加入期间与凭据相关的问题

如果得到一个对话框，该对话框要求凭据加入 Azure AD DS 托管域，则 VM 能够使用 Azure 虚拟网络连接到域。 域联接进程在验证域或授权以使用凭据完成域加入过程时失败。

要排除与凭据相关的问题，请查看以下故障排除步骤：

1. 请尝试使用 UPN 格式指定凭据。例如 `dee@aaddscontoso.onmicrosoft.com`。 确保此 UPN 在 Azure AD 中配置正确。
    * 如果租户中有多个具有相同 UPN 前缀的用户，或者您的 UPN 前缀过长，则帐户的*SAMAccountName*可能会自动生成。 因此，您帐户的*SAMAccountName*格式可能与您期望或在本地域中使用的内容不同。
1. 尝试对属于 Azure AD DS 托管域的用户帐户使用凭据，将 VM 加入托管域。
1. 请确保您[已启用密码同步][enable-password-sync]，并等待初始密码同步完成足够长的时间。

## <a name="next-steps"></a>后续步骤

有关作为域联接操作一部分的活动目录进程的深刻理解，请参阅[加入和身份验证问题][join-authentication-issues]。

如果仍难以将 VM 加入 Azure AD DS 托管域，[请查找帮助并打开 Azure 活动目录的支持票证][azure-ad-support]。

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
