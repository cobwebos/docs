---
title: Azure 标识管理基础知识
description: 基于云的标识现已成为控制和洞察用户如何以及何时访问企业应用程序与数据的最佳方式。
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewr: jsnow
ms.author: jeffgilb
ms.date: 07/05/2017
ms.topic: article
ms.prod: ''
ms.service: azure
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 7c00ccf214cfd683d04a76cc3cfad9bd27080561
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
---
# <a name="fundamentals-of-azure-identity-management"></a>Azure 标识管理基础知识

随着越来越多的公司数字资源驻留在企业网络外部的云中和设备上，采用优越的基于云的标识和访问管理解决方案变得很有必要。 基于云的标识现已成为控制和洞察用户如何以及何时访问企业应用程序与数据的最佳方式。

十多年来，Microsoft 一直在保护基于云的标识的安全，而现在则通过 [Azure Active Directory (AD)](active-directory-whatis.md) 提供相同的保护系统。 有了 Azure AD，企业管理员就可以轻松地确认用户和管理员的责任，提供比以前更好的安全性和监管。

Azure AD Premium 是基于云的标识和访问管理解决方案，提供的高级保护功能支持对所有应用使用一个安全标识、支持标识保护（通过 [Microsoft Intelligence Security Graph](https://www.microsoft.com/en-us/security/intelligence) 增强），以及支持 Privileged Identity Management。 Azure AD Premium 不只是另一个监视或报告工具，它还能实时保护用户的标识，允许创建基于风险的自适应访问策略来保护组织的数据。

请观看以下短视频，快速了解 Azure AD 标识管理和保护：
>[!VIDEO https://www.youtube.com/embed/9LGIJ2-FKIM]

Microsoft 不仅提供各处通用的标识，而且提供一套可以在组织中实现 IT 自动化、确保 IT 安全性以及进行 IT 管理的工具。 即使在出现云计算以后，也仍然需要管理和控制各种 IT 任务，例如通过呼叫支持人员来重置用户密码、进行用户组管理，以及提出应用程序请求。 让情况更为复杂的是，员工现在会将其个人设备带到工作中，并且会使用随时可用的 SaaS 应用程序。 这就使得跨公司数据中心和公有云平台维持对应用程序的控制极具挑战性。

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="connect-on-premises-active-directory-with-azure-ad-and-office-365"></a>通过 Azure AD 和 Office 365 连接本地 Active Directory
对本地 Active Directory 进行了大量投资的组织可以将这些投资扩展到云，只需将其本地目录和 Azure AD 集成到[混合标识管理](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview)中即可。 这样做可以为用户提供一个通用标识，用于访问各个位置的资源，从而提高工作效率。 因此，用户和组织可以使用单一登录 (SSO) 来访问本地资源和云服务（例如 Office 365）。

若要完成集成，只需使用 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 工具。 Azure AD Connect 提供满足标识同步需求的功能，并替换旧版标识集成工具（例如 DirSync 和 Azure AD Sync）。有了 Azure AD Connect，即可通过以下方式进行标识管理并在本地和 Azure AD 之间实现同步：

- 同步 - 此组件负责创建用户、组和其他对象。 它还负责确保本地用户和组的标识信息与云匹配。 也可启用密码写回，这样当用户在 Azure AD 中更新其密码时，就能保持本地目录同步。
- AD FS - 联合身份验证是 Azure AD Connect 提供的可选功能，可用于通过本地 AD FS 基础结构配置混合环境。 组织可以使用联合身份验证来解决复杂的部署，例如单一登录、实施 AD 登录策略和智能卡或第三方 MFA。
- 运行状况监视 - [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health) 提供可靠监视，并在 Azure 门户中提供一个中心位置，用于查看此活动。

## <a name="increase-productivity-and-reduce-helpdesk-costs-with-self-service-and-single-sign-on-experiences"></a>通过自助服务和单一登录体验提高工作效率，降低支持人员成本

如果只需记住单一用户名和密码且所有设备的体验是一致的，则员工的工作效率更高。 另外，如果能够执行自助任务，例如，如果能够[重置遗忘的密码](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)或请求访问某个应用程序而不需等待支持人员的帮助，则还可节省时间。

Azure AD [将本地 Active Directory 扩展](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)到云，让用户不仅能够将主要组织帐户用于已加入域的设备和公司资源，而且还能用于完成作业所需的全部 Web 和 SaaS 应用程序。 用户除了无需记忆多组用户名和密码，还可根据其组织的组成员身份和身为员工的状态，自动预配（或取消预配）其应用程序访问权限。 可以控制库应用或者自行通过 [Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)开发和发布的本地应用的该访问权限。

## <a name="manage-and-control-access-to-corporate-resources"></a>管理和控制对公司资源的访问
Microsoft 标识和访问管理解决方案可帮助 IT 部门保护对企业数据中心和云中的应用程序和资源的访问，从而支持附加的验证级别，比如[多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)和[条件性访问策略](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)。 通过高级安全报告、审核和警报来监视可疑活动，有助于减少潜在的安全问题。

Azure AD Premium 中的条件性访问策略允许（企业管理员）为任何通过 Azure AD 连接的应用程序（SaaS 应用、运行在云中的自定义应用，或者本地 Web 应用程序）创建基于策略的访问规则。 Azure AD 实时评估这些策略，并在用户尝试访问应用程序时强制实施它们。 发现可疑活动时，可以通过 Azure 标识保护策略自动采取措施。 这些措施可能包括阻止高风险用户访问、实施多重身份验证，以及在凭据可能泄露的情况下重置用户密码。


## <a name="azure-active-directory-privileged-identity-management"></a>Azure Active Directory Privileged Identity Management

Azure Active Directory Premium P2 产品随附的 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) 用于发现、限制和监视管理帐户及其对 Azure Active Directory 和其他 Microsoft 联机服务中资源的访问权限。 它还可用于管理按需性的管理访问权限，具体时间段视需要而定。

Privileged Identity Management 可以强制实施按需性的管理员权限，使管理员可以在预配置的时间段内请求需要进行多重身份验证的临时性特权提升，然后其帐户又会回到正常的用户状态。

## <a name="benefits-of-azure-identity"></a>Azure 标识的优点

使用 Azure 标识管理可以执行以下操作：

-   为整个企业中的每个用户创建和管理单一标识，从而通过 [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) 保持用户、组和设备的同步。

-   提供对各种应用程序（包括数千预集成的 SaaS 应用）的单一登录访问权限，或者提供使用 [Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)对本地 SaaS 应用程序进行的安全远程访问的权限。

-   通过对本地应用程序和云应用程序强制执行基于规则的[多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)，启用应用程序访问安全措施。

-   允许通过 [MyApps 门户](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-user-help)进行[自助密码重置](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)以及提交组和应用程序访问请求，从而提高用户工作效率。

-   充分利用基于云的世界性企业级标识和访问管理解决方案的[高可用性和可靠性](https://docs.microsoft.com/azure/architecture/resiliency/high-availability-azure-applications)。

## <a name="next-steps"></a>后续步骤
[详细了解 Azure 标识解决方案](https://docs.microsoft.com/azure/active-directory/understand-azure-identity-solutions)