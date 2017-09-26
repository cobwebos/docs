---
title: "授权：Azure AD SSPR | Microsoft Docs"
description: "Azure AD 自助密码重置的授权要求"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: 5445a479fd6893048eb8ff356fa829a3dcd5f7d3
ms.contentlocale: zh-cn
ms.lasthandoff: 08/31/2017

---
# <a name="licensing-requirements-for-azure-ad-self-service-password-reset"></a>Azure AD 自助密码重置的授权要求

要使 Azure AD 密码重置正常工作，**必须在组织中至少分配一个许可证**。 我们不对密码重置体验强制实施每用户授权。 为了遵守 Microsoft 许可协议，需要向使用高级功能的所有用户分配许可证。

* **仅限云用户** - Office 365 (O365) 任何付费 SKU 或 Azure AD Basic
* **云**和/或**本地用户** - Azure AD Premium P1 或 P2、企业移动性 + 安全性 (EMS) 或 Secure Productive Enterprise (SPE)

## <a name="licenses-required-for-password-writeback"></a>密码写回所需的许可证

若要使用密码写回，必须在租户中分配以下许可证之一。

* Azure AD Premium P1
* Azure AD Premium P2
* 企业移动性 + 安全性 E3
* 企业移动性 + 安全性 E5
* Microsoft 365 E3
* Microsoft 365 E5

> [!NOTE]
> 独立 Office 365 许可计划**不支持密码写回**，要使此功能正常工作，需要上述计划之一。

可在以下页上找到其他许可信息（包括成本）

* [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)
* [Azure Active Directory 特性和功能](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [企业移动性 + 安全性](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365](https://www.microsoft.com/microsoft-365/enterprise)

## <a name="enable-group-or-user-based-licensing"></a>启用基于组或基于用户的许可

Azure AD 现在支持基于组的许可，从而允许管理员将许可证批量分配给一组用户，而不是一次一个用户地分配。 [分配、验证和解决许可证问题](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses)

某些 Microsoft 服务不能在所有位置使用。 在将许可证分配给用户之前，管理员必须为该用户指定“使用位置”属性。 可在 Azure 门户中的“用户”>“配置文件”>“设置”部分下完成分配许可证。 **使用组许可证分配时，任何没有指定使用位置的用户将继承该目录的位置。**

## <a name="next-steps"></a>后续步骤

以下链接提供了有关使用 Azure AD 进行密码重置的其他信息

* [**快速入门**](active-directory-passwords-getting-started.md) - 启动并运行 Azure AD 自助服务密码管理 
* [**数据**](active-directory-passwords-data.md) - 了解所需的数据以及如何使用它进行密码管理
* [**推出**](active-directory-passwords-best-practices.md) - 使用此处提供的指南计划 SSPR 并将其部署到用户
* [**自定义**](active-directory-passwords-customize.md) - 自定义公司的 SSPR 体验的外观。
* [**报告**](active-directory-passwords-reporting.md) - 了解用户是否访问 SSPR 功能，以及在何时何处进行访问
* [深入技术探究](active-directory-passwords-how-it-works.md) - 了解幕后的工作原理
* [**常见问题**](active-directory-passwords-faq.md) - 如何？ 为什么？ 什么？ 何处？ 谁？ 何时？ - 常见问题的答案
* [**故障排除**](active-directory-passwords-troubleshoot.md) - 了解如何解决使用 SSPR 时遇到的常见问题

