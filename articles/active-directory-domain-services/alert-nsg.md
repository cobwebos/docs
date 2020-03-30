---
title: 在 Azure AD DS 中解决网络安全组警报 |微软文档
description: 了解如何对 Azure 活动目录域服务的网络安全组配置警报进行故障排除和解析
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "71258007"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>已知问题：Azure 活动目录域服务中的网络配置警报

要让应用程序和服务与 Azure 活动目录域服务 （Azure AD DS） 正确通信，必须打开特定的网络端口以允许流量流动。 在 Azure 中，您可以使用网络安全组控制流量。 如果所需的网络安全组规则没有到位，Azure AD DS 托管域的运行状况将显示警报。

本文可帮助您了解和解决网络安全组配置问题的常见警报。

## <a name="alert-aadds104-network-error"></a>警报 AADDS104：网络错误

### <a name="alert-message"></a>警报消息

*Microsoft 无法访问此托管域的域控制器。如果虚拟网络上配置的网络安全组 （NSG） 阻止对托管域的访问，则可能发生此情况。另一个可能的原因是，是否有用户定义的路由阻止来自 Internet 的传入流量。*

无效的网络安全组规则是 Azure AD DS 网络错误的最常见原因。 虚拟网络的网络安全组必须允许访问特定端口和协议。 如果这些端口被阻止，Azure 平台无法监视或更新托管域。 Azure AD 目录和 Azure AD DS 托管域之间的同步也会受到影响。 请确保保持默认端口打开状态，以避免服务中断。

## <a name="default-security-rules"></a>默认安全规则

以下默认入站和出站安全规则将应用于 Azure AD DS 托管域的网络安全组。 这些规则确保 Azure AD DS 的安全，并允许 Azure 平台监视、管理和更新托管域。 如果[配置安全 LDAP，][configure-ldaps]您可能还有一条允许入站流量的附加规则。

### <a name="inbound-security-rules"></a>入站安全规则

| 优先度 | “属性” | 端口 | 协议 | 源 | 目标 | 操作 |
|----------|------|------|----------|--------|-------------|--------|
| 101      | 允许同步与 AzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 201      | 允许RD | 3389 | TCP | 公司网络锯 | Any | Allow |
| 301      | 允许PS重新移动 | 5986| TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 65000    | 所有VnetinBound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Any | Allow |
| 65500    | DenyAllInBound | Any | Any | Any | Any | 拒绝 |

### <a name="outbound-security-rules"></a>入站安全规则

| 优先度 | “属性” | 端口 | 协议 | 源 | 目标 | 操作 |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | 所有VnetOut绑定 | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | 允许 Azure 加载平衡器退出绑定 | Any | Any |  Any | Internet | Allow |
| 65500    | DenyAllOutBound | Any | Any | Any | Any | 拒绝 |

>[!NOTE]
> Azure AD DS 需要从虚拟网络无限制的出站访问。 我们不建议您创建任何其他规则来限制虚拟网络的出站访问。

## <a name="verify-and-edit-existing-security-rules"></a>验证和编辑现有安全规则

要验证现有安全规则并确保默认端口处于打开状态，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择**网络安全组**。
1. 选择与托管域关联的网络安全组，例如*AADDS-contoso.com-NSG*。
1. 在 **"概述"** 页上，将显示现有的入站和出站安全规则。

    查看入站和出站规则，并比较上一节中所需的规则列表。 如果需要，选择并删除阻止所需流量的任何自定义规则。 如果缺少任何必需的规则，则在下一节中添加规则。

    添加或删除规则以允许所需的流量后，Azure AD DS 托管域的运行状况会在两小时内自动更新并删除警报。

### <a name="add-a-security-rule"></a>添加安全规则

要添加缺少的安全规则，完成以下步骤：

1. 在 Azure 门户中，搜索并选择**网络安全组**。
1. 选择与托管域关联的网络安全组，例如*AADDS-contoso.com-NSG*。
1. 在左侧面板中的 **"设置"** 下，单击 *"入站安全规则*"或 *"出站安全规则*"，具体取决于需要添加的规则。
1. 选择 **"添加**"，然后根据端口、协议、方向等创建所需的规则。准备就绪后，选择 **"确定**"。

添加安全规则并显示在列表中需要一些时间。

## <a name="next-steps"></a>后续步骤

如果仍有问题，[请打开 Azure 支持请求][azure-support]以获取其他故障排除帮助。

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
