---
title: 解决 Azure AD DS 中的网络安全组警报 | Microsoft Docs
description: 了解如何排查和解决 Azure Active Directory 域服务的网络安全组配置警报
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: f8917d7bd8fc1a4091607b9a405cfefbb51bc188
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962779"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>已知问题：Azure Active Directory 域服务中的网络配置警报

若要使应用程序和服务能够与 Azure Active Directory 域服务 (Azure AD DS) 托管域正确通信，必须打开特定的网络端口以允许流量流动。 在 Azure 中，你可使用网络安全组控制流量的流动。 如果所需的网络安全组规则未部署到位，则 Azure AD DS 托管域的运行状况将显示警报。

本文将帮助你了解和解决网络安全组配置问题的常见警报。

## <a name="alert-aadds104-network-error"></a>警报 AADDS104：网络错误

### <a name="alert-message"></a>警报消息

Microsoft 程序无法访问此托管域的域控制器。*如果虚拟网络上配置的网络安全组 (NSG) 阻止访问托管域，则可能会发生这种情况。另一个可能的原因为，如果有用户定义的路由阻止来自 Internet 的传入流量。*

无效的网络安全组配置是导致 Azure AD DS 网络错误的最常见原因。 虚拟网络的网络安全组必须允许访问特定端口和协议。 如果这些端口被阻止，Azure 平台将无法监视或更新托管域。 Azure AD 目录和 Azure AD DS 之间的同步也会受到影响。 请确保这些端口处于打开状态，以避免服务中断。

## <a name="default-security-rules"></a>默认安全规则

以下默认入站和出站安全规则适用于托管域的网络安全组。 这些规则保护 Azure AD DS 的安全，并允许 Azure 平台监视、管理和更新托管域。

### <a name="inbound-security-rules"></a>入站安全规则

| 优先级 | 名称 | 端口 | 协议 | Source | 目标 | 操作 |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | 任意 | 允许 |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | 任意 | 允许 |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | 任意 | 允许 |
| 65000    | AllVnetInBound | 任意 | 任意 | VirtualNetwork | VirtualNetwork | 允许 |
| 65001    | AllowAzureLoadBalancerInBound | 任意 | 任意 | AzureLoadBalancer | 任意 | 允许 |
| 65500    | DenyAllInBound | 任意 | 任意 | 任意 | 任意 | 拒绝 |

> [!NOTE]
> 如果[配置安全 LDAP][configure-ldaps]，还可使用其他规则来允许入站流量。 此附加规则是进行正确 LDAPS 通信的必需条件。

### <a name="outbound-security-rules"></a>入站安全规则

| 优先级 | 名称 | 端口 | 协议 | Source | 目标 | 操作 |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | 任意 | 任意 | VirtualNetwork | VirtualNetwork | 允许 |
| 65001    | AllowAzureLoadBalancerOutBound | 任意 | 任意 |  任意 | Internet | 允许 |
| 65500    | DenyAllOutBound | 任意 | 任意 | 任意 | 任意 | 拒绝 |

>[!NOTE]
> Azure AD DS 需要从虚拟网络进行不受限制的出站访问。 我们建议不要创建任何其他规则来限制虚拟网络的出站访问。

## <a name="verify-and-edit-existing-security-rules"></a>验证和编辑现有安全规则

若要验证现有的安全规则并确保默认端口已打开，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“网络安全组”。
1. 选择与托管域相关联的网络安全组，例如 AADDS-contoso.com-NSG。
1. “概述”页会显示现有的入站和出站安全规则。

    查看入站和出站规则，并将其与上一节中所需规则的列表进行比较。 如果需要，请选择并删除任何阻止所需流量的自定义规则。 如果缺少任何所需的规则，请在下一节中添加规则。

    添加或删除规则以允许所需流量后，托管域的运行状况会在两小时内自动更新，并删除警报。

### <a name="add-a-security-rule"></a>添加安全规则

若要添加缺少的安全规则，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“网络安全组”。
1. 选择与托管域相关联的网络安全组，例如 AADDS-contoso.com-NSG。
1. 在左侧面板的“设置”下，单击“入站安全规则”或“出站安全规则”，具体取决于需要添加的规则 。
1. 选择“添加”，然后根据端口、协议、方向等创建所需的规则。准备就绪后，选择“确定”。

添加安全规则并显示在列表中需要一些时间。

## <a name="next-steps"></a>后续步骤

如果仍有问题，请[发起 Azure 支持请求][azure-support]以获得额外的疑难解答帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
