---
title: 解决 Azure AD DS 中的网络安全组警报 |Microsoft Docs
description: 了解如何排查和解决 Azure Active Directory 域服务的网络安全组配置警报
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258007"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>已知问题：Azure Active Directory 域服务中的网络配置警报

为了使应用程序和服务能够与 Azure Active Directory 域服务（Azure AD DS）正确通信，必须打开特定的网络端口以允许流量流动。 在 Azure 中，可以使用网络安全组控制流量的流动。 如果所需的网络安全组规则不存在，则 Azure AD DS 托管域的运行状况状态将显示警报。

本文将帮助你了解和解决网络安全组配置问题的常见警报。

## <a name="alert-aadds104-network-error"></a>警报 AADDS104：网络错误

### <a name="alert-message"></a>警报消息

Microsoft 程序无法访问此托管域的域控制器。*如果虚拟网络上配置的网络安全组 (NSG) 阻止访问托管域，则可能会发生这种情况。另一个可能的原因为，如果有用户定义的路由阻止来自 Internet 的传入流量。*

网络安全组规则无效是 Azure AD DS 的网络错误的最常见原因。 虚拟网络的网络安全组必须允许访问特定端口和协议。 如果这些端口被阻止，Azure 平台将无法监视或更新托管域。 Azure AD 目录和 Azure AD DS 托管域之间的同步也会受到影响。 请确保保持打开默认端口，以避免服务中断。

## <a name="default-security-rules"></a>默认安全规则

以下默认入站和出站安全规则适用于 Azure AD DS 托管域的网络安全组。 这些规则使 Azure AD DS 安全，并允许 Azure 平台监视、管理和更新托管域。 如果[配置安全 LDAP][configure-ldaps]，还可以使用其他规则来允许入站流量。

### <a name="inbound-security-rules"></a>入站安全规则

| Priority | 姓名 | Port | Protocol | Source | Destination | 操作 |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | 任意 | Allow |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | 任意 | Allow |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | 任意 | Allow |
| 65000    | AllVnetInBound | 任意 | 任意 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | 任意 | 任意 | AzureLoadBalancer | 任意 | Allow |
| 65500    | DenyAllInBound | 任意 | 任意 | 任意 | 任意 | 拒绝 |

### <a name="outbound-security-rules"></a>出站安全规则

| Priority | 姓名 | Port | Protocol | Source | Destination | 操作 |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | 任意 | 任意 | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | 任意 | 任意 |  任意 | Internet | Allow |
| 65500    | DenyAllOutBound | 任意 | 任意 | 任意 | 任意 | 拒绝 |

>[!NOTE]
> Azure AD DS 需要虚拟网络的无限制出站访问权限。 建议你不要创建任何其他规则来限制对虚拟网络的出站访问。

## <a name="verify-and-edit-existing-security-rules"></a>验证和编辑现有安全规则

若要验证现有的安全规则并确保默认端口已打开，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择 "**网络安全组**"。
1. 选择与托管域相关联的网络安全组，例如*AADDS-contoso.com-NSG*。
1. 在 "**概述**" 页上，将显示现有的入站和出站安全规则。

    查看入站和出站规则，并将其与上一节中所需规则的列表进行比较。 如果需要，请选择并删除任何阻止所需流量的自定义规则。 如果缺少任何所需的规则，请在下一部分中添加规则。

    添加或删除允许所需流量的规则后，Azure AD DS 托管域的运行状况将在两小时内自动更新，并删除警报。

### <a name="add-a-security-rule"></a>添加安全规则

若要添加缺少的安全规则，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择 "**网络安全组**"。
1. 选择与托管域相关联的网络安全组，例如*AADDS-contoso.com-NSG*。
1. 在左侧面板的 "**设置**" 下，根据需要添加的规则，单击 "*入站安全规则*" 或 "*出站安全规则*"。
1. 选择 "**添加**"，然后根据端口、协议、方向等创建所需的规则。准备就绪后，选择 **"确定"** 。

添加安全规则需要一些时间，并在列表中显示。

## <a name="next-steps"></a>后续步骤

如果仍有问题，请[提出 Azure 支持请求][azure-support]，以获取额外的故障排除帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
