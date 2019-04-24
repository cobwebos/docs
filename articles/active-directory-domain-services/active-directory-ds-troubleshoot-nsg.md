---
title: Azure Active Directory 域服务：网络安全组配置疑难解答 | Microsoft Docs
description: Azure AD 域服务的 NSG 配置的疑难解答
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: 503e52266c1c6be71e60a751c40ef0a54f0d9b12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60416411"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>排查托管域的无效网络配置问题
本文可帮助你排查和解决导致以下警报消息的与网络相关的配置错误：

## <a name="alert-aadds104-network-error"></a>警报 AADDS104：网络错误
**警报消息：** Microsoft 程序无法访问此托管域的域控制器。*如果虚拟网络上配置的网络安全组 (NSG) 阻止访问托管域，则可能会发生这种情况。另一个可能的原因为，如果有用户定义的路由阻止来自 Internet 的传入流量。*

无效的 NSG 配置是导致 Azure AD 域服务网络错误的最常见原因。 为虚拟网络配置的网络安全组 (NSG) 必须允许访问[特定端口](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)。 如果已阻止这些端口，Microsoft 将无法监视或更新托管域。 此外，Azure AD 目录与托管域之间的同步也会受到影响。 创建 NSG 时，请保持这些端口处于打开状态，以避免服务中断。

### <a name="checking-your-nsg-for-compliance"></a>检查 NSG 的合规性

1. 在 Azure 门户中导航到[网络安全组](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)页面
2. 从表中选择与启用了托管域的子网关联的 NSG。
3. 在左侧面板的“设置”下，单击“入站安全规则”
4. 就地检查规则并识别是哪些规则阻止了对[这些端口](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services)的访问
5. 通过删除规则、添加规则来编辑 NSG，或创建一个全新的 NSG，以确保合规性。 [添加规则](#add-a-rule-to-a-network-security-group-using-the-azure-portal)或创建新的合规 NSG 的步骤如下所示

## <a name="sample-nsg"></a>示例 NSG
下表描述了一个示例 NSG，它将在允许 Microsoft 监视、管理和更新信息的同时保持托管域的安全。

![示例 NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD 域服务需要从虚拟网络进行不受限制的出站访问。 我们建议不要创建任何其他 NSG 规则来虚拟网络的出站访问。

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>使用 Azure 门户将规则添加到网络安全组
如果不想使用 PowerShell，则可以使用 Azure 门户手动将单个规则添加到 NSG。 若要在网络安全组中创建规则，请完成以下步骤：

1. 在 Azure 门户中导航到[网络安全组](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups)页面。
2. 从表中选择与启用了托管域的子网关联的 NSG。
3. 在左侧面板的“设置”下，单击“入站安全规则”或“出站安全规则”。
4. 通过单击“添加”并填写信息来创建规则。 单击“确定”。
5. 通过在规则表中查找已创建的规则来验证规则。


## <a name="need-help"></a>需要帮助？
欢迎联系 Azure Active Directory 域服务产品团队[分享看法或请求支持](active-directory-ds-contact-us.md)。
