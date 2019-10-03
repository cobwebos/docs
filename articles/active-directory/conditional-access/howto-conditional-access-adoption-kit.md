---
title: 条件访问采用工具包 - Azure Active Directory
description: 采用 Azure AD 条件访问以访问资源
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: c75ec0ec212c99e474d13892178acbdc14e5f386
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075278"
---
#  <a name="adopting-azure-ad-conditional-access"></a>采用 Azure AD 条件访问

在移动优先、云优先的世界，用户可从任意位置使用各种设备和应用访问组织的资源。 因此，仅关注谁可以访问资源不再能满足需求。 你可以控制谁有访问权限、识别用户的位置、正在使用哪些设备，等等。

为了提供这种控制，**Azure Active Directory (AD) 条件访问**允许指定任何用户必须满足哪种条件才能访问应用程序，例如，必须使用多重身份验证 (MFA)。 使用条件访问策略可以控制已授权用户（已获得访问云应用访问权限的用户）在特定条件下访问云应用的方式。 有关详细信息，请参阅 [Azure Active Directory 中的条件访问是什么](overview.md)。

## <a name="key-benefits"></a>主要优点

使用 Azure AD 条件访问的主要优点是：

* **提高工作效率：** 使用条件访问 (CA) 策略可以确定何时要提示用户使用 MFA、阻止其访问，或者要求他们使用受信任的设备。 例如，可以设置相应的策略，规定仅当企业网络断开时，才要求用户通过 MFA 访问某个应用程序。 与要求用户每次登录时都需要执行 MFA 相比，减少 MFA 请求数可让用户保持较高的工作效率。 此外，Azure AD 条件访问允许按用户指定策略，以及创建特定于应用的策略。
* **控制风险：** 启用条件访问策略可以提供云规模的标识保护、基于风险的访问控制功能和本机多重身份验证支持。 将条件访问与标识保护配合使用，可以定义何时要阻止或限制对某个应用程序的访问。
* **解决合规与监管要求：** 使用 Azure AD 可以更轻松地审核对应用程序的访问请求和审批，以及了解应用程序的总体使用情况，因为 Azure AD 支持对执行的每个应用程序访问请求使用本机审核日志。 审核包括请求者身份、请求日期、业务理由、审批状态和审批者身份。 也可以通过 API 获得此数据。使用 API 可将此数据导入到所选的安全事故和事件监视 (SIEM) 系统。
* **控制成本：** 将访问策略转移到 Azure AD 可以减少对自定义或本地解决方案（例如用于条件访问的 Active Directory 联合身份验证服务 (ADFS)）的依赖，从而降低运行该基础结构的成本。

## <a name="customer-case-studies"></a>客户案例研究

了解大多数组织如何使用 Azure AD 条件访问来定义和实施自动化的访问控制决策，以基于条件访问云应用。 以下特选案例演示了如何解决这些客户需求。

* [**Wipro**通过 Microsoft 云安全工具推动移动工作效率，以改善客户服务。](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Azure AD 中的条件访问策略可让公司与受信任的外部实体（它们可以使用自己的凭据）共享文档、资源和应用程序，同时保留对自身企业数据的控制权。
* [**Accenture** 通过 Microsoft Cloud App Security 来保护其到云的迁移](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)Accenture 正在评估 Cloud App Security 的[条件访问应用控制](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)功能，该功能使用 Azure Active Directory 条件访问根据特定的条件来限制应用程序访问。 LePenske 指出，此功能可用于实现只读文件访问以及禁止下载等目的。
* [**Aramex Delivery Limited** - 跨国物流和运输公司，使用标识和访问管理解决方案设立了与云联网的办事处](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)。 确保 Aramex 远程工作者的安全访问特别困难。 该公司目前正在应用条件访问，使这些远程工作者能够从网络外部访问其 SaaS 应用程序。 条件访问规则将确定是否强制实施多重身份验证，只向适当的人员提供适当的访问权限。

若要详细了解 Azure AD 条件访问的客户与合作伙伴体验，请访问 - [See the amazing things people are doing with Azure](https://azure.microsoft.com/case-studies/?service=active-directory)（看一看 Azure 的美妙用途）。

## <a name="announcements"></a>公告

Azure AD 会不断改进。 若要及时了解最新的开发，请参阅[Azure Active Directory 中的新增功能](../fundamentals/whats-new.md)

技术社区和 Microsoft 标识部门最近发布的博客：

* 2018 年 9 月 24 日：[Azure Databricks 中的 Azure Active Directory 条件访问](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018 年 9 月 21 日：[Azure AD 条件访问自定义控制目前已推出公共预览版](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 2018 年 9 月 21 日：[现已推出使用 Microsoft Cloud App Security 进行受限制访问的 Azure AD 条件访问支持](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 2018 年 9 月 21 日：[Azure AD 条件访问：适用于 iOS/Android 平台的托管浏览器支持现已推出预览版](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 2018 年 9月 21 日：[适用于国家/地区代码的 Azure AD 条件访问现已推出公共预览版](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 2018 年 9 月 21 日：[现已发布 Azure AD 使用条款](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>学习资源

请单击以下链接获取 Azure AD 条件访问的功能概述。

* 了解“[Azure Active Directory 中的条件访问是什么？](overview.md)”
* 知道“[Azure Active Directory 条件访问中的条件是什么？](conditions.md)”
* 知道“[Azure Active Directory 条件访问中的位置条件是什么？](location-condition.md)”
* 知道“[Azure Active Directory 条件访问中的访问控制是什么？](controls.md)”
* 了解“[Azure Active Directory 条件访问中的 What If 工具是什么？](what-if-tool.md)”
* 遵循 [Azure Active Directory 中的条件访问的最佳做法](best-practices.md)

此外，请参阅以下指导文章，了解如何保护对集成到 Azure Active Directory 的所有服务的访问。

* [什么是基线保护（预览版）？](baseline-protection.md) 基线保护确保在 Azure Active Directory 环境中启用至少为基线级别的安全性。
* [标识和设备访问权限配置](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)。 介绍如何通过通过企业移动性 + 安全性产品，并实施建议的环境和配置（包括一组预先规定的条件访问策略和相关功能），来配置对云服务的安全访问。
* [Azure Active Directory 条件访问设置参考](technical-reference.md)。 了解：
   * 哪些应用使用条件访问？
   * 哪些服务启用了条件访问？
* [启用 Azure Active Directory 的条件性访问以实现安全的用户访问](https://www.youtube.com/watch?v=eLAYBwjCGoA)。 观看此视频，了解条件性访问如何在其他企业和移动套件的工作负载中起作用。

### <a name="training-videos"></a>培训视频

**企业移动性 + 安全性中的条件性访问**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**基于设备的条件性访问**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**启用用于安全用户访问的条件性访问的 Azure Active Directory**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>在线课程

参阅以下条件访问课程，以及 [Pluralsight.com](https://www.pluralsight.com/) 上的详细信息：

* Pluralsight.com：[Microsoft Azure 中的设计身份管理](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * “本课程引导你完成一些重要的准备工作，以帮助你使用 Azure AD 设计标识管理解决方案。” “在 Azure AD 中使用角色和访问控制”模块介绍了 Azure AD 条件访问。

* Pluralsight.com：[Microsoft Azure 的设计身份验证](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * “本课程介绍如何使用 Azure AD 来解决所有的云身份验证要求。” “不同方案的身份验证要求”模块介绍了 Azure AD 条件访问。

* Pluralsight.com：[Microsoft Azure 的设计授权](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * “本课程讲解可在 Azure 和 Azure AD 中使用的授权选项。” “使用 Azure 资源管理器和 Azure AD 授权”模块介绍了 Azure AD 条件访问。

### <a name="books"></a>书籍

* O'Reilly - [Implementing Azure Solutions - Second Edition](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)（实施 Azure 解决方案 - 第二版）。
   * “开始运行 Azure 服务，并了解如何在组织中实施这些服务。 [部署和同步 Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml) 一章中介绍了 Azure AD 条件访问。”

* Wiley-[主控 Microsoft Azure 基础结构服务](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "以下是你需要了解、评估、部署和维护使用 Microsoft Azure 的环境所需的所有内容。"

## <a name="white-papers"></a>白皮书

* 于2018年12月18日发布，[创建弹性访问控制管理策略，Azure Active Directory](../authentication/concept-resilient-controls.md)
   * 本文档提供了组织可能采用的策略，以便在不可预见的中断期间降低锁定风险。

* 发布了2018年9月18日，[用于将应用程序迁移到 Azure Active Directory 的资源](../manage-apps/migration-resources.md)
   * 此白皮书包含可帮助你将应用程序访问和身份验证迁移到 Azure Active Directory (Azure AD) 的资源列表。

* 2018 [年7月12日发布 Azure 安全性与合规性蓝图：适用于英国官方工作负载的 PaaS Web 应用程序](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure 蓝图由指导文档和自动化模板组成，用于部署基于云的体系结构，为具有认证或符合性要求的方案提供解决方案。

## <a name="guidance-for-it-administrators"></a>面向 IT 管理员的指南

以全局管理员、安全管理员或条件访问管理员的身份登录到 [Azure 门户](https://portal.azure.com/)。 参阅 [Azure Active Directory 中的管理员角色权限](../users-groups-roles/directory-assign-admin-roles.md)。

IT 管理员可以使用 [Azure AD 条件访问](overview.md)来要求用户执行多重身份验证，或者从受信任的网络或设备登录。

下面是一些可帮助你入门的有用链接：

* [Azure Active Directory 中的条件访问的最佳做法](best-practices.md)
* [使用 Azure AD 访问评审管理已从条件访问策略中排除的用户](../governance/conditional-access-exclusion.md)
* [如何：规划 Azure Active Directory 中的条件访问部署](plan-conditional-access.md)
* [快速入门：使用 Azure Active Directory 条件访问要求针对特定应用进行 MFA](app-based-mfa.md)
* [快速入门：在访问云应用之前要求接受使用条款](require-tou.md)
* [快速入门：使用 Azure Active Directory 条件访问检测到会话风险时阻止访问](app-sign-in-risk.md)
* [Azure AD 条件访问常见问题解答](faqs.md)
   * 如有其他问题，还可以访问 [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)。
   * 如果找不到问题的解答，我们的支持团队始终愿意提供进一步的帮助。 [请使用联系 Microsoft 支持部门](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support)。

### <a name="tutorials"></a>教程

* [**快速入门：使用 Azure Active Directory 条件访问要求针对特定应用进行 MFA**](app-based-mfa.md)
   * 此快速入门介绍如何配置 Azure AD 条件访问策略，以要求针对环境中的所选云应用进行多重身份验证。

* [**快速入门：在访问云应用之前要求接受使用条款**](require-tou.md)
   * 此快速入门介绍如何配置一个 Azure AD 条件访问策略，要求用户在访问环境中的选定云应用之前接受 ToU。

* [**快速入门：使用 Azure Active Directory 条件访问检测到会话风险时阻止访问**](app-sign-in-risk.md)
   * 此快速入门介绍如何配置条件访问策略，以便在检测到已配置的登录风险级别时阻止登录。

* [教程：**迁移要求在 Azure 门户中进行多重身份验证的经典策略**](policy-migration-mfa.md)
   * 此教程介绍如何迁移要求对云应用进行多重身份验证 (MFA) 的经典策略。

## <a name="end-user-readiness-and-communication"></a>最终用户就绪性和沟通

条件访问使用其他 Azure AD 功能，这可能会影响最终用户的体验。 例如，你可能会使用 Azure 多重身份验证来为用户启用强身份验证。 在这种情况下，需使用 Azure MFA 的最终用户模板。

## <a name="next-steps"></a>后续步骤

* 参考[条件访问部署规划文档](plan-conditional-access.md)开始部署。
