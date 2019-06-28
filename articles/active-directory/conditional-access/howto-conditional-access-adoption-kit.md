---
title: 条件性访问采用工具包-Azure Active Directory
description: 采用资源的访问权限的 Azure AD 条件性访问
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
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387568"
---
#  <a name="adopting-azure-ad-conditional-access"></a>采用 Azure AD 条件访问

在移动优先、 云优先世界中，用户可以从任何位置使用不同类型的设备和应用访问你组织的资源。 因此，仅关注谁可以访问资源不再足够。 您可以控制谁有权访问和标识用户所在和正在使用何种设备，并且更多。

若要提供此控件**Azure Active Directory (AD) 条件性访问**允许您指定的任何用户访问应用程序，如多重身份验证 (MFA) 必须满足的条件。 使用条件性访问策略控制如何授权的用户 （有权访问云应用的用户） 访问在特定条件下的云应用。 请参阅[什么是 Azure Active Directory 中条件性访问](overview.md#conditional-access-policies)有关详细信息。

## <a name="key-benefits"></a>主要优点

使用 Azure AD 条件访问的主要优点是：

* **提高工作效率：** 条件性访问 (CA) 策略允许您为目标的点的系统会提示用户使用 MFA，具有访问权限受到阻止，或使用受信任的设备时需要。 例如，可以设置策略，例如仅在企业网络外部的应用程序到需要 MFA 的用户。 减少 MFA 请求，可阻止用户比他们是否使用 mfa 每次登录的时提高工作效率。 此外，Azure AD 条件访问，可指定策略每用户为基础，并且还创建特定于应用的策略。
* **管理风险：** 启用条件性访问策略可提供云规模标识保护、 基于风险的访问控制功能，与本机多重身份验证支持。 使用 identity protection 耦合条件性访问，可定义被阻止或网关访问的应用程序时。
* **解决合规性和监管：** 审核访问请求和审批的应用程序和了解应用程序的总体使用情况是 Azure AD，更容易，因为它支持本机审核日志为执行每个应用程序访问请求。 审核包括请求者标识、 请求的日期、 业务理由、 批准状态和审批者标识。 此数据还会显示一个 API，能够将启用此数据到安全事件和事件监视 (SIEM) 系统选择的导入。
* **管理成本：** 移动到 Azure AD 的访问策略可以减少依赖于自定义或内部部署解决方案 (如 Active Directory 联合身份验证服务 (ADFS) 条件访问，减少运行基础结构的成本。

## <a name="customer-case-studies"></a>客户案例研究

了解如何大多数组织使用 Azure AD 条件访问定义和实现自动化的访问控制决策来访问云应用基于的条件。 下面的专题的故事演示了如何满足这些客户需求。

* [**Wipro**驱动器使用 Microsoft 云安全工具，以提高客户互动的移动工作效率。](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Azure AD 中的条件性访问策略已启用公司与受信任的外部实体---共享文档、 资源和应用程序能够使用他们自己的凭据---可使用同时保持对其自己的公司数据控制。
* [**Accenture**与 Microsoft Cloud App security 保护其迁移到云](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)评估 Accenture[条件访问应用控制](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)功能的 Cloud App Security，后者使用 Azure Active目录旨在限制应用程序访问基于特定条件的条件性访问。 LePenske 说，此功能将适用的假设，从而实现只读的文件访问，同时禁止下载。
* [**Aramex**交付有限的逻辑和运输家全球性公司使用标识和访问管理解决方案创建云连接 office](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)。 尤其是很难做到 Aramex 的远程员工是确保安全访问。 该公司正在应用条件性访问，以使这些远程员工可以访问其 SaaS 应用程序从网络外部的。 条件性访问规则将决定是否强制执行多重身份验证，为正确的人提供正确的访问权限。

若要了解有关 Azure AD 条件性访问的客户和合作伙伴体验的详细信息，请访问-[查看使用 Azure 创造令人惊叹的事情人](https://azure.microsoft.com/case-studies/?service=active-directory)。

## <a name="announcements"></a>公告

Azure AD 会不断改进。 若要保持最新状态的最新的开发成果，请参阅[什么是 Azure Active Directory 中的新增功能？](../fundamentals/whats-new.md)

通过技术社区和 Microsoft 标识部门的最新博客：

* 2018 年 9 月 24 日， [Azure Databricks 中的 Azure Active Directory 条件性访问](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018 年 9 月 21 日，[自定义控件是在公共预览版中的 Azure AD 条件访问](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 2018 年 9 月 21 日，[进行有限访问，Microsoft Cloud App Security 的 Azure AD 条件访问支持现已可用](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 2018 年 9 月 21 日， [Azure AD 条件访问：托管浏览器支持现在处于预览状态的 iOS/Android 平台](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 2018 年 9 月 21 日，[国家/地区代码的 Azure AD 条件性访问是公共预览版](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 2018 年 9 月 21 日， [Azure AD--使用条款现已推出](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>学习资源

请按照以下链接，获取 Azure AD 条件性访问函数的概述。

* 了解"[什么是 Azure Active Directory 中条件访问？](overview.md)"
* 了解"[什么是 Azure Active Directory 条件访问中的条件？](conditions.md)"
* 了解"[什么是 Azure Active Directory 条件访问中的位置条件？](location-condition.md)"
* 了解"[什么是访问控制在 Azure Active Directory 条件性访问？](controls.md)"
* 找到"[什么是什么如果工具在 Azure Active Directory 条件性访问？"](what-if-tool.md)
* 请按照[的 Azure Active Directory 中条件性访问的最佳做法](best-practices.md)

此外，请参阅以下链接以获取指导，保护对与 Azure Active Directory 集成的所有服务的访问。

* [什么是基线保护 （预览版）？](baseline-protection.md) 基线保护可确保您至少具有在 Azure Active Directory 环境中启用安全基线级别。
* [标识和设备访问配置](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)。 介绍如何通过实现建议的环境和配置，包括一组规定的条件性访问策略和相关的功能来配置对企业移动性 + 安全产品通过云服务的安全访问。
* [Azure Active Directory 条件访问设置参考](technical-reference.md)。 了解：
   * 哪些应用使用条件性访问？
   * 使用条件性访问已启用哪些服务？
* [启用安全的用户访问 Azure Active Directory 条件性访问](https://www.youtube.com/watch?v=eLAYBwjCGoA)。 观看此视频，了解条件性访问如何在其他企业和移动性套件的工作负荷发挥了作用。

### <a name="training-videos"></a>培训视频

**企业移动性 + 安全性中条件性访问**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**基于设备的条件性访问**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**启用 Azure Active Directory 条件性访问的安全用户的访问权限**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>在线课程

请参阅下面的条件性访问课程和的详细信息的[Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com:[设计 Microsoft Azure 中的标识管理](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "此课程指导您完成需要了解设计与 Azure AD 标识管理解决方案的重要项目。" "使用角色和使用 Azure AD 访问控制"中介绍了 azure AD 条件性访问模块。

* Pluralsight.com:[设计适用于 Microsoft Azure 的身份验证](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "本课程介绍如何使用 Azure AD 以解决你的云身份验证要求。" 在"身份验证要求的不同方案"模块中介绍了 azure AD 条件性访问。

* Pluralsight.com:[适用于 Microsoft Azure 设计授权](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "此课程指导授权选项适用于 Azure 和 Azure AD。" "使用 Azure 资源管理器和 Azure AD 授权"模块中介绍了 azure AD 条件性访问。

### <a name="books"></a>丛书

* O'Reilly-[实施 Azure 解决方案的第二个版本。](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "快速启动并运行 Azure 服务并了解如何在你的组织中实现它们。 一章中介绍了 azure AD 条件性访问[部署和同步 Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)。"

* Wiley-[掌握 Microsoft Azure 基础结构服务](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "这是您需要了解、 评估、 部署和维护环境下使用 Microsoft Azure 的所有内容。"

## <a name="white-papers"></a>白皮书

* 发布于 2018 年 12 月 18 日，[与 Azure Active Directory 中创建可复原的访问控制管理策略](../authentication/concept-resilient-controls.md)
   * 本文档提供的组织的策略指南可能采用的能提供复原能力来降低在无法预料的中断过程中锁定的风险。

* 发布于 2018 年 9 月 18 日，[资源迁移到 Azure Active Directory 的应用程序](../manage-apps/migration-resources.md)
   * 本白皮书包括一系列资源，以帮助您迁移到 Azure Active Directory (Azure AD) 的应用程序访问和身份验证。

* 发布于 2018 年 7 月 12 日[Azure 安全性和符合性蓝图：适用于英国官方工作负荷的托管的 PaaS Web 应用程序](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure 蓝图由指导文档和自动化模板组成，用于部署基于云的体系结构，为具有认证或符合性要求的方案提供解决方案。

## <a name="guidance-for-it-administrators"></a>面向 IT 管理员指南

登录到[Azure 门户](https://portal.azure.com/)作为全局管理员、 安全管理员或条件性访问管理员。 请参阅[Azure Active Directory 中的管理员角色权限。](../users-groups-roles/directory-assign-admin-roles.md)

作为 IT 管理员，使用[Azure AD 条件访问](overview.md)要让用户使用 Azure 多重身份验证进行身份验证，登录从受信任的网络或受信任的设备。

下面是帮助你入门的有用链接：

* [Azure Active Directory 中条件性访问的最佳做法](best-practices.md)
* [使用 Azure AD 访问评审管理用户已从条件性访问策略中排除](../governance/conditional-access-exclusion.md)
* [如何：规划在 Azure Active Directory 中的条件性访问部署](plan-conditional-access.md)
* [快速入门：使用 Azure Active Directory 条件性访问需要针对特定应用的 MFA](app-based-mfa.md)
* [快速入门：在访问云应用之前要求接受使用条款](require-tou.md)
* [快速入门：会话风险检测到与 Azure Active Directory 条件性访问时阻止访问](app-sign-in-risk.md)
* [Azure AD 条件访问常见问题解答](faqs.md)
   * 有关其他问题，还可以查看[MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)。
   * 如果找不到问题的答案，我们的支持团队始终是可用于进一步的帮助。 使用[请联系 Microsoft 支持部门](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support)。

### <a name="tutorials"></a>教程

* [**快速入门：使用 Azure Active Directory 条件性访问需要针对特定应用的 MFA**](app-based-mfa.md)
   * 本快速入门介绍如何配置你的环境中的所选的云应用程序要求多重身份验证的 Azure AD 条件访问策略。

* [**快速入门：需要访问云应用程序之前接受的使用条款**](require-tou.md)
   * 本快速入门介绍如何配置你的环境中的所选的云应用程序要求接受 ToU 的 Azure AD 条件访问策略。

* [**快速入门：会话风险检测到与 Azure Active Directory 条件性访问时阻止访问**](app-sign-in-risk.md)
   * 本快速入门介绍如何配置条件性访问策略时检测到已配置登录风险级别阻止登录。

* [教程：**迁移要求在 Azure 门户中的多重身份验证的经典策略**](policy-migration-mfa.md)
   * 本教程演示如何迁移要求多重身份验证 (MFA) 对云应用的经典策略。

## <a name="end-user-readiness-and-communication"></a>最终用户准备情况和通信

条件性访问使用其他 Azure AD 功能，可能会影响最终用户体验。 例如，可以使用 Azure 多重身份验证启用强身份验证的用户。 在这种情况下，将使用 Azure MFA 的最终用户模板。

## <a name="next-steps"></a>后续步骤

* 开始使用你的部署[条件性访问部署规划文档](plan-conditional-access.md)。
