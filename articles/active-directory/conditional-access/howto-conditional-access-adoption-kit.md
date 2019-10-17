---
title: 条件性访问采用工具包-Azure Active Directory
description: 采用 Azure AD 访问资源的条件性访问
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
ms.openlocfilehash: c34f59c3e9f679adf8ae410f648cb7de6dba6447
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430055"
---
#  <a name="adopting-azure-ad-conditional-access"></a>采用 Azure AD 条件性访问

在移动优先、云优先的世界中，用户可以使用不同种类的设备和应用从任何位置访问组织的资源。 因此，只关注可以访问资源的人员就不再足够了。 您可以控制谁有访问权限，确定用户是谁以及正在使用的设备，等等。

为了提供此控制， **Azure Active Directory （AD）条件性访问**允许指定任何用户必须满足的条件，以便访问应用程序，如多重身份验证（MFA）。 使用条件性访问策略控制经过授权的用户（已被授权访问云应用的用户）在特定条件下访问云应用的方式。 有关详细信息，请参阅[Azure Active Directory 中的条件性访问](overview.md)。

## <a name="key-benefits"></a>主要优点

使用 Azure AD 条件性访问的主要优点是：

* **提高工作效率：** 通过条件性访问（CA）策略，你可以将提示用户使用 MFA 的点设定为目标，使访问被阻止，或者需要使用受信任的设备。 例如，可以设置策略，例如仅要求用户在关闭企业网络时 MFA 到应用程序。 减少 MFA 请求比用户在每次登录时都需要进行 MFA 的工作效率更高。 此外，Azure AD 的条件访问允许你指定每个用户的策略，还可以创建特定于应用的策略。
* **管理风险：** 启用条件性访问策略为你提供云规模的标识保护、基于风险的访问控制功能和本机多重身份验证支持。 使用 identity protection 的耦合条件性访问允许你定义何时阻止或阻止对应用程序的访问。
* **解决合规性和监管：** 审核对应用程序的访问请求和批准，并了解 Azure AD 的总体应用程序使用情况，因为它支持对执行的每个应用程序访问请求使用本机审核日志。 审核包括申请人身份、请求日期、业务理由、批准状态和审批者身份。 此数据也可通过 API 获得，这将允许将此数据导入到所选的安全事件和事件监视（SIEM）系统中。
* **管理成本：** 将访问策略移动到 Azure AD 降低了自定义或本地解决方案（例如用于条件性访问的 Active Directory 联合身份验证服务（ADFS））的依赖，从而降低了运行该基础结构的成本。

## <a name="customer-case-studies"></a>客户案例研究

了解大多数组织如何使用 Azure AD 条件访问来定义和实现基于条件访问云应用的自动访问控制决策。 以下精选故事演示了如何满足这些客户需求。

* [**Wipro**通过 Microsoft 云安全工具推动移动工作效率，以改善客户服务。](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Azure AD 中的条件性访问策略使公司能够与受信任的外部实体共享文档、资源和应用程序---用户可以使用其自己的凭据---同时保持对其自己的公司数据的控制。
* [**Accenture**使用 Microsoft 云应用安全 Accenture 来保护其迁移到云](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)，以评估 Cloud App Security 的[条件访问应用控制](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)功能，该功能使用 Azure Active Directory 的条件性访问基于特定条件的应用程序访问。 LePenske 指出，此功能可用于在禁止下载时启用只读文件访问。
* [ **Aramex**交付有限-全球物流和运输公司使用标识和访问管理解决方案创建与云连接的办公室](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)。 确保安全访问在 Aramex 的远程员工中特别困难。 公司现在正在应用条件性访问，使这些远程员工可以从网络外部访问其 SaaS 应用程序。 条件性访问规则将决定是否强制执行多重身份验证，只向正确的人提供正确的访问权限。

若要了解有关客户和合作伙伴体验 Azure AD 条件访问的详细信息，请访问-[查看用户使用 Azure 所做的奇妙](https://azure.microsoft.com/case-studies/?service=active-directory)。

## <a name="announcements"></a>通告

Azure AD 会不断改进。 若要及时了解最新的开发，请参阅[Azure Active Directory 中的新增功能](../fundamentals/whats-new.md)

技术社区和 Microsoft 标识部门的最近博客：

* 2018年9月24日， [Azure Active Directory Azure Databricks 中的条件性访问](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018年9月21日， [Azure AD 条件访问自定义控件处于公共预览](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 2018年9月21日， [Azure AD 的条件访问支持使用 Microsoft Cloud App Security 的受限访问现已推出](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 2018年9月21日， [Azure AD 条件性访问：目前在预览版中提供适用于 iOS/Android 平台的托管浏览器支持](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 2018年9月21日， [Azure AD 国家/地区代码的条件性访问是公共预览](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 2018年9月21日，[现已推出 Azure AD 使用条款](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>学习资源

请单击下面的链接，大致了解 Azure AD 条件访问函数的工作原理。

* 了解 "[什么是 Azure Active Directory 中的条件性访问？](overview.md)"
* 知道 "[Azure Active Directory 条件性访问中的条件是什么？](conditions.md)"
* 知道 "[Azure Active Directory 条件性访问中的位置条件是什么？](location-condition.md)"
* 知道 "[什么是 Azure Active Directory 条件访问中的访问控制？](controls.md)"
* 查找 "[Azure Active Directory 条件性访问中的 if 工具是什么？"](what-if-tool.md)
* 遵循[Azure Active Directory 中条件性访问的最佳做法](best-practices.md)

此外，请参阅以下链接，以获取有关如何保护对与 Azure Active Directory 集成的所有服务的访问。

* [标识和设备访问配置](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)。 介绍如何通过实施推荐的环境和配置，通过企业移动性 + 安全性产品配置对云服务的安全访问，包括一组规定的条件性访问策略和相关功能。
* [Azure Active Directory 条件访问设置引用](technical-reference.md)。 了解
   * 哪些应用使用条件性访问？
   * 使用条件性访问启用了哪些服务？
* [启用 Azure Active Directory 的条件性访问以实现安全的用户访问](https://www.youtube.com/watch?v=eLAYBwjCGoA)。 观看此视频，了解条件性访问如何在其他企业和移动套件的工作负载中起作用。

### <a name="training-videos"></a>培训视频

**企业移动性 + 安全性中的条件性访问**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**基于设备的条件性访问**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**启用用于安全用户访问的条件性访问的 Azure Active Directory**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>联机课程

有关[Pluralsight.com](https://www.pluralsight.com/)的详细信息，请参阅以下条件访问课程：

* Pluralsight.com：[在 Microsoft Azure 中设计身份管理](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "本课程指导您完成使用 Azure AD 设计标识管理解决方案时需要了解的重要事项。 "对 Azure AD 使用角色和访问控制" 模块中介绍了 Azure AD 条件性访问。

* Pluralsight.com： [Microsoft Azure 的设计身份验证](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "本课程介绍了如何使用 Azure AD 来解决所有的云身份验证要求。" "不同方案的身份验证要求" 模块中介绍了 Azure AD 条件性访问。

* Pluralsight.com：[设计 Microsoft Azure 的授权](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "本课程讲授 Azure 和 Azure AD 提供的授权选项。 "使用 Azure 资源管理器和 Azure AD 授权" 模块中介绍了 Azure AD 条件性访问。

### <a name="books"></a>帐簿

* O'Reilly-[实现 Azure 解决方案-第二版。](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "启动并运行 Azure 服务，了解如何在你的组织中实现它们。 "[部署和同步 Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml)" 一章中介绍了 Azure AD 条件性访问。

* Wiley-[主控 Microsoft Azure 基础结构服务](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "以下是你需要了解、评估、部署和维护使用 Microsoft Azure 的环境所需的所有内容。"

## <a name="white-papers"></a>白皮书

* 于2018年12月18日发布，[创建弹性访问控制管理策略，Azure Active Directory](../authentication/concept-resilient-controls.md)
   * 本文档提供了组织可能采用的策略，以便在不可预见的中断期间降低锁定风险。

* 发布了2018年9月18日，[用于将应用程序迁移到 Azure Active Directory 的资源](../manage-apps/migration-resources.md)
   * 此白皮书包含一个资源列表，可帮助你将应用程序访问和身份验证迁移到 Azure Active Directory （Azure AD）。

* 于2018年7月12日发布[Azure 安全性与合规性蓝图：适用于英国官方工作负荷的 PaaS Web 应用程序托管](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure 蓝图由指导文档和自动化模板组成，用于部署基于云的体系结构，为具有认证或符合性要求的方案提供解决方案。

## <a name="guidance-for-it-administrators"></a>面向 IT 管理员的指南

以全局管理员、安全管理员或条件访问管理员身份登录到[Azure 门户](https://portal.azure.com/)。 请参阅[Azure Active Directory 中的管理员角色权限。](../users-groups-roles/directory-assign-admin-roles.md)

作为 IT 管理员，请使用[Azure AD 条件性访问](overview.md)，要求用户使用 Azure 多重身份验证进行身份验证，从受信任的网络或受信任的设备登录。

下面是一些有用的链接，可帮助你入门：

* [Azure Active Directory 中的条件性访问的最佳做法](best-practices.md)
* [使用 Azure AD 访问评审来管理已从条件访问策略中排除的用户](../governance/conditional-access-exclusion.md)
* [如何：在 Azure Active Directory 中规划条件访问部署](plan-conditional-access.md)
* [快速入门：需要对具有 Azure Active Directory 条件性访问的特定应用进行 MFA](app-based-mfa.md)
* [快速入门：在访问云应用之前要求接受使用条款](require-tou.md)
* [快速入门：使用 Azure Active Directory 条件访问检测到会话风险时阻止访问](app-sign-in-risk.md)
* [Azure AD 条件访问常见问题解答](faqs.md)
   * 有关其他问题，还可以查看[MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)。
   * 如果找不到问题的答案，我们的支持团队始终可用来协助你进一步操作。 [请使用联系 Microsoft 支持部门](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support)。

### <a name="tutorials"></a>教程

* [**快速入门：需要对具有 Azure Active Directory 条件性访问的特定应用进行 MFA**](app-based-mfa.md)
   * 本快速入门介绍如何配置一个 Azure AD 条件访问策略，该策略需要对环境中的选定云应用进行多重身份验证。

* [**快速入门：在访问云应用之前要求接受使用条款**](require-tou.md)
   * 本快速入门介绍如何配置 Azure AD 条件性访问策略，该策略要求在你的环境中为所选的云应用接受 ToU。

* [**快速入门：使用 Azure Active Directory 条件访问检测到会话风险时阻止访问**](app-sign-in-risk.md)
   * 本快速入门介绍如何配置条件访问策略，该策略在检测到已配置的登录风险级别时阻止登录。

* [教程：**在 Azure 门户中迁移需要多重身份验证的经典策略**](policy-migration-mfa.md)
   * 本教程演示如何迁移要求对云应用进行多重身份验证（MFA）的经典策略。

## <a name="end-user-readiness-and-communication"></a>最终用户准备情况和通信

条件性访问使用其他 Azure AD 功能，这些功能可能会影响最终用户体验。 例如，你可以使用 Azure 多重身份验证为用户启用强身份验证。 在这种情况下，你将使用 Azure MFA 的最终用户模板。

## <a name="next-steps"></a>后续步骤

* 使用[条件性访问部署规划文档](plan-conditional-access.md)开始部署。
