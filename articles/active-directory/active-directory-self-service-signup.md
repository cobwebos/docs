---
title: "Azure Active Directory 中的自助服务或病毒性注册 | Microsoft Docs"
description: "在 Azure Active Directory (Azure AD) 租户中使用自助服务注册"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 762627a1d743922d680d90d1065ae80fbd3f4420
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>什么是 Azure Active Directory 的自助服务注册？
此文章介绍自助服务注册及如何在 Azure Active Directory (Azure AD) 中支持自助服务注册的相关内容。 若要从非托管 Azure AD 租户处接管域名，请参阅[以管理员身份接管非托管目录](domains-admin-takeover.md)。

## <a name="why-use-self-service-signup"></a>为何使用自助服务注册？
* 让客户更快获得所需的服务
* 为服务创建基于电子邮件的促销
* 创建基于电子邮件的注册流程，让用户使用易记的工作电子邮件别名快速创建标识
* 通过自助服务创建的 Azure AD 目录可转变为托管目录，以供其他服务使用

## <a name="terms-and-definitions"></a>术语和定义
* 自助注册：用户注册云服务并让系统根据其电子邮件域在 Azure AD 中自动为其创建标识的方法。
* 非托管 Azure AD 目录：在其中创建标识的目录。 非托管目录是没有全局管理员的目录。
* **电子邮件验证的用户**：Azure AD 中的一种用户帐户类型。 在注册自助服务产品后自动创建标识的用户称为电子邮件验证的用户。 电子邮件验证的用户是目录的常规成员，带有 creationmethod=EmailVerified 标记。

## <a name="how-do-i-control-self-service-settings"></a>如何控制自助服务设置？
目前，管理员有两种自助服务控制方式。 他们可以控制：

* 用户是否可以通过电子邮件加入目录。
* 用户是否可以对自身授权以获取应用程序和服务。

### <a name="how-can-i-control-these-capabilities"></a>如何控制这些功能？
管理员可以使用以下 Azure AD cmdlet Set-MsolCompanySettings 参数配置这些功能：

* **AllowEmailVerifiedUsers** 控制用户是否可以创建或加入非托管目录。 如果将该参数设置为 $false，则电子邮件验证的用户不可以加入目录。
* AllowAdHocSubscriptions 控制用户执行自助服务注册的能力。 如果将该参数设置为 $false，则用户不可以执行自助服务注册。

### <a name="how-do-the-controls-work-together"></a>这些控制方式如何配合工作？
可以结合使用这两个参数，从而实现对自助服务注册更精确的控制。 例如，以下命令允许用户执行自助服务注册，但前提是这些用户已在 Azure AD 中拥有一个帐户（换言之，需要先创建电子邮件验证帐户的用户无法执行自助服务注册）：

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
以下流程图解释了这些参数的不同组合，以及目录和自助注册的最终状态。

![][1]

有关示例和如何使用这些参数的详细信息，请参阅 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)。

## <a name="next-steps"></a>后续步骤
* [向 Azure AD 添加自定义域名](add-custom-domain.md)
* [如何安装和配置 Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet 参考](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
