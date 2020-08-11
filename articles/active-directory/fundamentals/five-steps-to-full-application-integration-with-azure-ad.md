---
title: 将所有应用与 Azure AD 集成的五个步骤
description: 本指南说明如何将所有应用程序与 Azure AD 集成。 在每个步骤中，我们将介绍此值，并提供指向将说明技术详细信息的资源的链接。
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057134"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>将所有应用与 Azure AD 集成的五个步骤

Azure Active Directory (Azure AD) 是 Microsoft 基于云的标识和访问管理服务。 Azure AD 提供安全的身份验证和授权解决方案，使客户、合作伙伴和员工可以访问所需的应用程序。 通过 Azure AD、[条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)、[多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)、[单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)和[自动用户预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)，可轻松、安全地进行标识和访问管理。

如果你的公司有 Microsoft 365 订阅，你可能[已使用](https://docs.microsoft.com/office365/enterprise/about-office-365-identity)Azure AD。 不过，Azure AD 可用于所有应用程序，通过[集中应用程序管理](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios)，你可以跨整个应用程序组合使用相同的标识管理功能、工具和策略。 这样做将提供一个统一的解决方案，可提高安全性、降低成本、提高工作效率，并可确保符合性。 你将获得对本地应用的远程访问权限。

本指南说明如何将所有应用程序与 Azure AD 集成。 在每个步骤中，我们将介绍此值，并提供指向将说明技术详细信息的资源的链接。 我们按建议的顺序显示这些步骤。 但是，您可以跳转到过程的任何部分，以开始添加最多的值。

本主题中的其他资源（包括深入的业务流程白皮书）可以在[将应用程序迁移到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)页面的资源中找到。

## <a name="1-use-azure-ad-for-new-applications"></a>1. 将 Azure AD 用于新应用程序

首先，重点关注新获取的应用程序。 当你的业务开始使用新的应用程序时，立即[将其添加到 Azure AD 租户](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)。 设置公司政策，以便将新应用添加到 Azure AD 是组织中的标准做法。 这对现有业务流程的影响最小，并使你能够调查并证明你在集成应用时获得的价值，而不会改变用户当前在环境中的业务方式。

Azure Active Directory (Azure AD) 包含了包含数千个预先集成的应用程序的库，便于入门。 可以[将库应用添加到 Azure AD 组织](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)，其中包含与常用应用集成的分步[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)，如：

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

此外，你还可以将[不在库中的应用](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)程序（包括你的组织中已存在的任何应用程序）或不属于 Azure AD 库的供应商的任何第三方应用程序集成在一起。 如果应用程序不在库中，也可以[将其添加到库](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing)。

最后，你还可以将内部开发的应用进行集成。 这将在本指南的第5步中介绍。

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. 确定现有应用程序使用情况并排定工作优先级

接下来，发现员工经常使用的应用程序，并确定如何将工作与 Azure AD 进行集成。

你可以使用 Microsoft Cloud App Security&#39;的[Cloud discovery 工具](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)开始发现和管理 &quot; &quot; 网络中的影子 IT (也就是说，不是由 IT 部门) 管理的应用程序。 你可以[使用 Microsoft Defender 高级威胁防护 (ATP) ](https://docs.microsoft.com/cloud-app-security/wdatp-integration)来简化和扩展发现过程。

此外，你可以使用 Azure 门户中的 " [AD FS 应用程序活动" 报表](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)来发现组织中的所有 AD FS 应用、登录到这些应用的唯一用户的数量，以及将其与 Azure AD 集成的兼容性。

发现现有的布局后，需要[创建一个计划](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)，并排定优先级最高的应用程序的优先级。 您可以指导您执行此过程的一些示例问题如下：

- 最常使用哪些应用？
- 最危险是什么？
- 将来哪些应用将会被停用，不必要地进行移动？
- 哪些应用需要保留在本地，并且不能移到云中？

在所有应用程序集成后，你将看到最大的好处和成本节约，你不再依赖多个标识解决方案。 但是，当你将逐步迁移到此目标时，你将体验到更轻松的身份管理和提高安全性。 您希望使用这一时间来确定工作优先级，并决定您的情况。

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. 集成依赖于其他标识提供者的应用

在发现过程中，你可能找到了 IT 部门未跟踪的应用程序，这会使你的数据和资源容易受到攻击。 你还可能具有使用替代标识解决方案的应用程序，包括 Active Directory 联合身份验证服务 (ADFS) 或其他标识提供者。 考虑如何合并标识和访问管理，以节省资金并提高安全性。 减少您拥有的标识解决方案的数量：

- 通过无需本地用户预配和身份验证，以及对同一服务的其他云标识提供者支付的许可费用，为你节省资金。
- 减少管理开销并实现更严格的安全性，并且标识和访问管理过程中的冗余更少。
- 使员工能够通过[MyApps 门户](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)来安全地对所需的所有应用程序进行单一登录访问。
- 通过增加从应用使用情况获取的数据量来改善 Azure AD&#39;s[标识保护](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)相关服务（如条件访问）的智能，并将其优势扩展到新添加的应用。

我们发布了有关如何管理将应用与 Azure AD 集成的业务流程的指南，包括可用于使业务和应用程序所有者了解和感兴趣的[海报](https://aka.ms/AppOnePager)和[演示](https://aka.ms/AppGuideline)。 你可以使用自己的品牌修改这些示例，并在完成此过程时通过公司门户、新闻稿或其他介质将它们发布到组织。

一个好的起点是评估你使用 (ADFS) Active Directory 联合身份验证服务。 许多组织使用 ADFS 通过 SaaS 应用、自定义业务线应用和 Office 365 和基于 Azure AD 的应用进行身份验证：

![关系图显示本地应用程序、业务线应用程序、SaaS 应用程序，并通过 Azure AD，Office 365 所有都用虚线连接到 Active Directory 和 AD FS。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

可以通过将[ADFS 替换为标识管理解决方案的中心作为 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure)来升级此配置。 这样做可以为你的员工要访问的每个应用启用登录，并使员工可以通过[MyApps 门户](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)轻松找到所需的任何业务应用程序，以及上述其他权益。

![关系图显示了通过 Active Directory 和 AD FS、业务线应用、SaaS 应用和 Office 365 的本地应用程序，所有这些应用程序都以虚线连接到 Azure Active Directory。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

一旦 Azure AD 成为中心标识提供者，你就可以从 ADFS 完全切换，而不是使用联合解决方案。 以前使用 ADFS 进行身份验证的应用现在可以单独使用 Azure AD。

![关系图显示了本地、业务线应用、SaaS 应用和 Office 365，它们都以虚线连接到 Azure Active Directory。 Active Directory 和 AD FS 不存在。](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

你还可以将使用其他基于云的标识提供者的应用迁移到 Azure AD。 你的组织可能有多个 (IAM) 解决方案的标识访问管理。 迁移到一个 Azure AD 基础结构是为了减少 (本地或云) 和基础结构成本中的 IAM 许可证的依赖项。 如果你可能已通过 M365 许可证为 Azure AD 付费，则没有理由支付另一个 IAM 解决方案的额外成本。

## <a name="4-integrate-on-premises-applications"></a>4. 集成本地应用程序

通常，应用程序在连接到企业网络时只允许访问，从而保持安全。 但是，在越来越多的环境中，我们希望允许客户、合作伙伴和/或员工访问应用，而不考虑他们在世界各地的位置。 [Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) 是 Azure AD 的一项功能，它将现有的本地应用程序连接到 Azure AD，无需维护边缘服务器或其他其他基础结构来执行此操作。

![关系图显示应用程序代理服务的运行情况。 用户访问 " https://sales.contoso.com "，并通过将 https://sales-contoso.msappproxy.net Azure Active Directory 中的 "" 重定向到本地地址 " http://sales "](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

你可以使用[教程：在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)程序，以启用应用程序代理并将本地应用程序添加到 Azure AD 租户。

此外，你可以集成应用程序传送控制器（如 F5 大 IP APM 或 Zscaler 私有访问）。 通过将这些功能与 Azure AD 集成，你可以与合作伙伴产品的流量管理和安全功能 Azure AD 进行新式身份验证和标识管理。 我们称之为此解决方案的[安全的混合访问](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)。 如果你现在使用以下任何服务，我们将提供一些教程，指导你如何将它们与 Azure AD 集成。

- [Akamai 企业应用程序访问 (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Citrix 应用程序将控制器 (ADC) ](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (以前称为 Citrix Netscaler) 
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. 集成开发人员构建的应用

对于在公司内部构建的应用程序，开发人员可以使用[Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)来实现身份验证和授权。 与平台集成的应用程序，通过将其[注册到 Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app)和管理，就像你的公文包中的任何其他应用程序一样。

开发人员可以将平台用于内部使用的应用和面向客户的应用，还可以使用平台带来的其他优势。 [Microsoft 身份验证库 (MSAL) ](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)，它是平台的一部分，使开发人员能够实现多种新式体验，例如多重身份验证和使用安全密钥来访问其应用，而无需自行实现。 此外，与 Microsoft 标识平台集成的应用可以访问[Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) -一种统一的 API 终结点，提供描述组织中的工作效率、标识和安全性模式的 Microsoft 365 数据。 开发人员可以使用这些信息来实现提高用户工作效率的功能。 例如，通过识别用户最近与最近交互的人员并在应用&#39;s UI 中将其呈现。

我们提供了一[系列视频系列](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX)，其中提供了平台以及支持的语言和平台中的[许多代码示例](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code)。

## <a name="next-steps"></a>后续步骤

- [用于将应用程序迁移到 Azure Active Directory 的资源](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
