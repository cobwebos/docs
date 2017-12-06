---
title: "Azure Active Directory 入门 | Microsoft Docs"
description: "获取许可证，在 Azure Active Diretory 中添加域名，创建自定义登录页，并添加自助密码重置"
keywords: 
author: curtand
manager: michael.tillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.prod: 
ms.service: active-directory
ms.workload: identity
ms.technology: 
ms.assetid: 
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: dd745869494a1ed740a0a05a5508363334aa7360
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="get-started-with-azure-ad"></a>Azure AD 入门
现代的标识管理需要一贯的可伸缩性和可靠性，确保只向经过身份验证的用户提供应用程序和服务。 为了充分支持用户对标识管理的需求，IT 部门需要通过某种方式提供对已批准的公用软件即服务 (SaaS) 应用的访问，托管内部业务线应用，甚至还需要通过某些方式来增强本地应用的开发与使用。 满足所有这些要求需要一种基于云的标识管理解决方案。      

Azure Active Directory (Azure AD) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 Azure AD 将核心目录服务、高级标识监管和应用程序访问管理相结合。 Azure AD 的多租户、地理分布、高可用性设计意味着可以依赖它来解决最关键的业务需求。

Azure AD 提供整套标识管理功能，包括同步本地资源信息、可自定义的公司品牌、简单的许可证管理，甚至自助密码管理。  这些易于设置的功能可帮助我们开始使用 Azure AD 来保护基于云的应用程序，简化 IT 流程，削减成本，确保符合企业的合规目标。

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

本文的余下部分说明如何开始使用 Azure AD。 

## <a name="sign-up-for-azure-active-directory-premium"></a>注册 Azure Active Directory Premium
若要[开始使用 Azure Active Directory (Azure AD) Premium](active-directory-get-started-premium.md)，可以购买许可证并将其与 Azure 订阅相关联。 若要创建新的 Azure 订阅，还需要激活许可计划和 Azure AD 服务访问权限。 

若要注册 Active Directory Premium，可以使用多个选项： 

- 使用 Azure 或 Office 365 订阅
- 使用企业移动性 + 安全性许可计划
- 使用 Microsoft 批量许可计划

### <a name="verification-step"></a>验证步骤
激活订阅后，确保可以登录到服务。

## <a name="add-a-custom-domain-name"></a>添加自定义域名
每个 Azure AD 都直接随附 domainname.onmicrosoft.com 形式的初始域名。无法更改或删除初始域名，但也可以[将企业域名添加到 Azure AD](add-custom-domain.md)。 例如，你的组织可能具有用来从事商业经营的其他域名和使用公司域名登录的用户。 通过在 Azure AD 中添加自定义域名可以在目录中分配用户熟悉的用户名，例如“alice@contoso.com”。 而不是“alice@.onmicrosoft.com”。 过程很简单：

1. 将自定义域名添加到目录
2. 在域名注册机构中为域名添加 DNS 条目
3. 在 Azure AD 中验证自定义域名

### <a name="verification-step"></a>验证步骤
添加自定义域后，请确保它在 Azure AD 门户的“自定义域名”边栏选项卡上显示的状态为“已验证”。

## <a name="add-company-branding-to-your-sign-in-page"></a>在登录页中添加公司品牌 
许多公司想要在他们管理的所有网站和服务上应用一致的外观，以免用户感到困惑。 Azure Active Directory (Azure AD) 提供了此功能，允许我们[使用自己的公司徽标和自定义的颜色方案来自定义登录页外观](customize-branding.md)。 登录页是登录到 Office 365 或其他使用 Azure AD 作为标识提供者的基于 Web 的应用程序时显示的页面。 用户与此页进行交互以输入其凭据。

### <a name="verification-step"></a>验证步骤
登录到 Azure 门户，并确保已正确配置自定义的登录页和其他任何语言自定义项。 

## <a name="add-new-users"></a>添加新用户
可以使用 Azure 门户或通过同步本地 Windows Server AD 资源数据，逐个地[将新用户添加到组织的 Azure AD](add-users-azure-active-directory.md)。 可以直接从 Azure AD 门户添加基于云的用户，或同步本地用户信息。

若要启用本地标识到 Azure AD 的同步，需在组织的服务器上安装并配置 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。 该应用程序负责将用户和组从现有的标识源同步到 Azure AD 租户。

### <a name="verification-step"></a>验证步骤
创建或同步新用户之后，请确保他们显示在 Azure AD 中。

## <a name="assign-licenses"></a>分配许可证
只要获取了订阅，就能配置付费功能，但是，要使用 Azure AD Premium 付费功能，仍需[向用户分配许可证](license-users-groups.md)。 必须向应有权访问 Azure AD 付费功能或者通过 Azure AD 付费功能管理的用户分配许可证。 许可证分配是用户与购买的服务（例如 Azure AD Premium、Basic 或企业移动性 + 安全性）之间的映射。

可以使用基于组的许可证分配设置规则，如以下示例所示：

- 目录中的所有用户会自动获取许可证
- 具有相应职务的每个人都会获得许可证
- 可以使用自助服务组，将决策权委托给组织中的其他管理者

### <a name="verification-step"></a>验证步骤
在“Azure Active Directory” > “许可证” > “所有产品”下面查看已分配的和可用的许可证。

## <a name="configure-self-service-password-reset"></a>配置自助密码重置
IT 管理员可以让用户通过[自助密码重置 (SSPR)](active-directory-passwords-getting-started.md) 这种简单方法来重置或解锁其密码或帐户。 系统提供详细的报告，用于跟踪用户使用系统的时间，同时还提供通知，提醒用户存在误用或滥用情况。

### <a name="verification-step"></a>验证步骤
在“Azure Active Directory” > “密码重置”下面查看已启用的 SSPR 属性，确保已完成适当的用户和组分配。 


## <a name="next-steps"></a>后续步骤
[Azure Active Directory 产品页](https://azure.microsoft.com/services/active-directory/)

[Azure Active Directory 定价信息页](https://azure.microsoft.com/pricing/details/active-directory/)