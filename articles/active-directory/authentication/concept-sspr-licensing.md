---
title: 许可使用 Azure Active Directory 自助密码
description: Azure AD 自助密码重置的授权要求
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 83054c505689768c14d168841764a4557c3e1f8b
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158992"
---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD 自助密码重置的授权要求

Azure Active Directory (Azure AD) 提供 4 个版本：Free、Basic、Premium P1 和 Premium P2。 有几个不同的功能可以构成自助密码重置，包括更改、重置、解锁和写回，这些功能在 Azure AD 的不同版本中可用。 本文尝试说明这些差异。 可以在 [Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory/)上找到每个Azure AD 版本中包含的功能的更多详细信息。

## <a name="compare-editions-and-features"></a>比较版本和功能

Azure AD 自助密码重置是按用户许可的，为了维护合规性，需要组织为其用户分配相应的许可证。

* 云用户的自助密码更改
   * 我是**仅限云用户**并且知道我的密码。
      * 我想要将我的密码**更改**为新密码。
   * 此功能包含在 Azure AD 的所有版本中。

* 云用户的自助密码重置
   * 我是**仅限云用户**并且忘记了密码。
      * 我想要将我的密码**重置**为我知道的密码。
   * 此功能包含在 Azure AD Basic、Premium P1 或 Premium P2 版本中。

* **通过本地写回**实现自助密码重置/更改/解锁
   * 我是**混合用户**，我的本地 Active Directory 用户帐户已使用 Azure AD Connect 与我的 Azure AD 帐户同步。 我想要更改密码、已忘记密码或已被锁定。
      * 我想要更改密码或将其重置为我知道的密码，或者解锁我的帐户，**并**将此更改同步回本地 Active Directory。
   * 此功能包含在 Azure Premium P1 或 Premium P2 版本中。

> [!WARNING]
> 独立 Office 365 许可计划**不支持密码写回**，要使此功能正常工作，需要使用 Azure AD Premium P1 或 Premium P2 版本。
>

可在以下页上找到其他许可信息（包括成本）：

* [Azure Active Directory 定价网站](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 特性和功能](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [企业移动性 + 安全性](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 企业版](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>启用基于组或基于用户的许可

Azure AD 现在支持基于组的许可。 管理员可以将许可证批量分配给一组用户，而不是一次一个用户地分配。 有关详细信息，请参阅[分配、验证许可证和解决许可证问题](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)。

某些 Microsoft 服务不能在所有位置使用。 将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。 可在 Azure 门户中的“用户” > “配置文件” > “设置”部分下完成分配许可证。 *使用组许可证分配时，任何没有指定使用位置的用户将继承该目录的位置。*

## <a name="next-steps"></a>后续步骤

* [如何成功推出 SSPR？](howto-sspr-deployment.md)
* [重置或更改密码](../user-help/active-directory-passwords-update-your-own-password.md)
* [注册自助密码重置](../user-help/active-directory-passwords-reset-register.md)
* [SSPR 使用哪些数据？应为用户填充哪些数据？](howto-sspr-authenticationdata.md)
* [哪些身份验证方法可供用户使用？](concept-sspr-howitworks.md#authentication-methods)
* [SSPR 有哪些策略选项？](concept-sspr-policy.md)
* [什么是密码写回？我为什么关心它？](howto-sspr-writeback.md)
* [如何报告 SSPR 中的活动？](howto-sspr-reporting.md)
* [SSPR 中的所有选项有哪些？它们有哪些含义？](concept-sspr-howitworks.md)
* [我认为有些功能被破坏。如何对 SSPR 进行故障排除？](active-directory-passwords-troubleshoot.md)
* [我有在别处未涵盖的问题](active-directory-passwords-faq.md)
