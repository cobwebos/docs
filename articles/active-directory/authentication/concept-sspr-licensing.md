---
title: 许可证自助密码重置 - Azure Active Directory
description: Azure AD 自助密码重置的授权要求
services: active-directory
keywords: ''
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 9eb0ccefc1d80fedbb827456faf2ef75d45a5c45
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD 自助密码重置的授权要求

要使 Azure Active Directory (Azure AD) 密码重置正常工作，*必须在组织中至少分配一个许可证*。 我们不对密码重置体验强制实施每用户授权。 为了遵守 Microsoft 许可协议，需要向使用高级功能的所有用户分配许可证。

* **仅限云用户**：Office 365 任何付费 SKU 或 Azure AD Basic
* **云**或**本地用户**：Azure AD Premium P1 或 P2、企业移动性 + 安全性 (EMS) 或 Microsoft 365

## <a name="licenses-required-for-password-writeback"></a>密码写回所需的许可证

若要使用密码写回，必须在租户中分配以下许可证之一：

* Azure AD Premium P1
* Azure AD Premium P2
* 企业移动性 + 安全性 E3
* 企业移动性 + 安全性 E5
* Microsoft 365（计划 E3）
* Microsoft 365（计划 E5）

> [!WARNING]
> 独立 Office 365 许可计划*不支持密码写回*，要使此功能正常工作，需要使用上述计划之一。
>

可在以下页上找到其他许可信息（包括成本）：

* [Azure Active Directory 定价网站](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 特性和功能](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [企业移动性 + 安全性](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企业版](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>启用基于组或基于用户的许可

Azure AD 现在支持基于组的许可。 管理员可以将许可证批量分配给一组用户，而不是一次一个用户地分配。 有关详细信息，请参阅[分配、验证许可证和解决许可证问题](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)。

某些 Microsoft 服务不能在所有位置使用。 将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。 可在 Azure 门户中的“用户” > “配置文件” > “设置”部分下完成分配许可证。 *使用组许可证分配时，任何没有指定使用位置的用户将继承该目录的位置。*

## <a name="next-steps"></a>后续步骤

* [如何成功推出 SSPR？](howto-sspr-deployment.md)
* [重置或更改密码](../active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](../active-directory-passwords-reset-register.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](howto-sspr-authenticationdata.md)
* [哪些身份验证方法可供用户使用？](concept-sspr-howitworks.md#authentication-methods)
* [SSPR 有哪些策略选项？](concept-sspr-policy.md)
* [什么是密码写回？我为什么关心它？](../active-directory-passwords-writeback.md)
* [如何报告 SSPR 中的活动？](howto-sspr-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](concept-sspr-howitworks.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)
