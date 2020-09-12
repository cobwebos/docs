---
title: 使用用于标识管理的 Azure Active Directory 租户查看应用
description: 了解如何使用 Azure Active Directory 租户查看标识管理的所有应用程序。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 955efe45be27fa2f4e738066bb3b69b3604be33a
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400715"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>使用用于标识管理的 Azure AD 租户查看应用
[有关应用程序管理的快速入门系列](view-applications-portal.md)介绍了基础知识。 在此示例中，你将了解如何使用用于标识管理的 Azure AD 租户查看所有应用。 本文更深入地介绍了深入的应用类型。

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>为什么在所有应用程序列表中出现某个特定应用程序？
筛选为“所有应用程序”时，“所有应用程序列表” 会显示租户中的每个服务主体对象。 服务主体对象以多种方式出现在此列表中：
- 添加应用程序库的任何应用程序时，包括：
   - **Azure AD 企业应用程序** –使用 Azure AD 门户上的 " **企业应用程序** " 选项添加到租户的应用。 通常使用 SAML 标准集成的应用。
   - **Azure AD 应用注册** –使用 Azure AD 门户上的 **应用注册** 选项添加到租户的应用。 通常，使用 Open ID Connect 和 OAuth 标准开发自定义开发的应用程序。
   - **应用程序代理应用程序** - 要从外部向其提供单一登录且在本地环境中运行的应用程序
- 注册或登录到时，与 Azure Active Directory 集成的第三方应用程序。 [Smartsheet](https://app.smartsheet.com/b/home) 或 [DocuSign](https://www.docusign.net/member/MemberLogin.aspx) 就是一个示例。
- Microsoft 应用，如 Microsoft 365 或 Office 365。
- 当通过使用[应用程序注册表](https://docs.microsoft.com/azure/active-directory/active-directory-app-registration)创建以自定义方式开发的应用程序，来添加新应用程序注册之时
- 当通过使用 [V2.0 应用程序注册门户](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-app-registration)创建以自定义方式开发的应用程序，来添加新应用程序注册之时
- 添加应用程序时，使用 Visual Studio 的[ASP.NET 身份验证方法](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)或[连接的服务](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)进行开发
- 使用 [Azure AD PowerShell 模块](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)创建服务主体对象时
- 以管理员身份[同意某应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)使用租户中的数据时
- [用户同意某应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)使用租户中的数据时
- 启用某些在租户中存储数据的服务时。 相应的一个示例是密码重置，密码重置是作为服务主体进行建模的，以便安全存储密码重置策略。

详细了解如何将应用添加到你的目录，以及如何将应用程序添加到 [Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added)。

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 管理应用程序](what-is-application-management.md)
