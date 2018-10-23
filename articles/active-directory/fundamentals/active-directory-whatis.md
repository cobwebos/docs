---
title: 什么是 Azure Active Directory (Azure AD)？ | Microsoft Docs
description: 了解如何使用 Azure Active Directory 将现有的本地标识扩展到云中，或开发 Azure AD 集成的应用。
services: active-directory
author: eross-msft
manager: mtillman
ms.author: lizross
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: overview
ms.date: 09/13/2018
ms.custom: it-pro
ms.openlocfilehash: 08d32a99e2f1a77bcc7a5900b47e6771c33ec356
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393315"
---
# <a name="what-is-azure-active-directory"></a>什么是 Azure Active Directory？
Azure Active Directory (Azure AD) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 Azure AD 将核心目录服务、应用程序访问管理和标识保护融入单个解决方案中，提供基于标准的平台，可帮助开发人员根据集中的策略和规则实现对其应用的访问控制。

![Azure AD Connect 堆栈](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="benefits-of-azure-ad"></a>Azure AD 的益处
Azure AD 有助于：

-   为整个企业中的每个用户创建和管理单一标识，从而通过 [Azure AD Connect](../connect/active-directory-aadconnect.md) 保持用户、组和设备的同步。

-   提供对各种应用（包括数千预集成的 SaaS 应用）的单一登录访问权限，并使用 [Azure AD 应用程序代理](../manage-apps/application-proxy.md)提供对本地 SaaS 应用程序更为安全的远程访问权限。

-   通过对本地和云应用均强制执行基于规则的[多重身份验证](../authentication/concept-mfa-howitworks.md)策略，允许应用程序访问安全措施。

-   允许通过 [MyApps 门户](../user-help/active-directory-saas-access-panel-introduction.md)进行[自助密码重置](../user-help/user-help-reset-password.md)以及提交组和应用程序访问请求，从而提高用户工作效率。

-   充分利用基于云的世界性企业级标识和访问管理解决方案的[高可用性和可靠性](https://docs.microsoft.com/azure/architecture/checklist/availability)。

## <a name="who-uses-azure-ad"></a>谁在使用 Azure AD
Azure AD 面向 IT 管理员、应用开发人员以及 Office 365、Azure 或 Dynamics CRM Online 的用户。

- **IT 管理员。** Azure AD 使用了更强大的标识管理，并且可以通过单一登录 (SSO) 来访问数千个[基于云的 SaaS 应用](../saas-apps/tutorial-list.md)和本地应用，为你的组织提供更安全的解决方案。 通过这些应用，你还会实现基于云的应用安全性、无缝访问、增强的协作，并可自动完成用户的标识生命周期，从而增强安全性和符合性。

    此外，借助 [Azure AD Connect](../connect/active-directory-aadconnect-get-started-express.md) 可将 Azure AD 与现有的 Windows Server Active Directory 集成，使组织能够使用现有的本地标识投资来管理基于云的 SaaS 应用访问。

- **面向应用开发人员。** Azure AD 可集成全球数百万个组织所用的标识管理解决方案，帮助你专注于构建应用。

- **面向 Office 365、Azure 或 Dynamics CRM Online 客户。** 你已在使用 Azure AD。 每个 Office 365、Azure 和 Dynamics CRM Online 租户实际上都是 Azure AD 租户，因此你可以立即开始管理用户对集成云应用的访问。

## <a name="how-reliable-is-azure-ad"></a>Azure AD 的可靠性如何？
Azure AD 的多租户、地理分布和高可用性设计意味着可以依赖它来解决最关键的业务需求。 使用自动故障转移，Azure AD 可在全球 28 个数据中心运行。 这意味着，即使数据中心出现故障，目录数据的副本也存在于至少两个区域分散的数据中心并可供即时访问。

有关服务级别协议的详细信息，请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/)。

## <a name="choose-an-edition"></a>选择版本
所有 Microsoft Online 业务服务都依赖于 Azure AD 进行登录和满足其他标识需求。 如果订阅了任何 Microsoft Online 业务服务（例如，Office 365 或 Microsoft Azure），则表示已自动获得 Azure AD，可用于访问所有免费功能。 使用 Azure Active Directory 免费版，可以管理用户和组、与本地目录同步，以及在 Azure、Office 365 和数千种主流 SaaS 应用（如 Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox 等）上单一登录。 

为了增强 Azure AD 实现，还可以通过升级为 Azure Active Directory Basic、Premium P1 或 Premium P2 版添加付费功能。 Azure AD 付费版建立在现有免费目录基础之上，提供企业级功能，包括自助服务、增强型监视、安全报告、多重身份验证 (MFA) 和移动工作者安全访问。

> [!NOTE]
> 有关这些版本的定价选项，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。 中国地区目前不支持 Azure Active Directory Premium P1、Premium P2 和 Azure Active Directory Basic。 有关 Azure AD 定价的详细信息，可与 Azure Active Directory 论坛联系。

- **Azure Active Directory Basic。** 面向有着云优先需求的任务工作人员，此版本提供以云为中心的应用程序访问和自助标识管理解决方案。 使用 Basic 版本，可以增强工作效率并获得成本缩减功能，例如基于组的访问管理、用于云应用的自助密码重置、Azure Active Directory 应用程序代理（使用 Azure AD 发布本地 Web 应用），以及 99.9％ 正常运行时间的企业级 SLA。

- **Azure Active Directory Premium P1。** 专门支持对标识和访问管理具有更高要求的组织，Azure Active Directory Premium Edition 添加了功能丰富的企业级标识管理功能，并让混合用户无缝访问本地和云功能。 此版本为混合环境中的信息工作者和标识管理员提供一切必要的功能，让他们执行应用程序访问、自助标识和访问管理 (IAM)、标识保护及实现云中的安全性。 它支持动态组和自助服务组管理等高级管理与委派资源。 它包含 Microsoft 标识管理器（一个本地标识与访问管理套件），并提供云写回功能，使本地用户能够使用自助密码重置等解决方案。

- **Azure Active Directory Premium P2。** 这款新产品包含 Azure AD Premium P1 以及 Identity Protection 和 Privileged Identity Management 中的所有功能，旨在为用户和管理员提供高级保护。 Azure Active Directory Identity Protection 利用数十亿信号，针对应用和重要公司数据提供基于风险的条件访问。 此外，我们还使用 Azure Active Directory Privileged Identity Management 帮助管理和保护特权帐户，方便发现、限制和监视管理员以及他们对资源的访问，并在需要时提供恰时访问。  

> [!NOTE]
> 许多 Azure Active Directory 功能也以“即付即用”版本的形式提供：<ul><li>**Azure Active Directory B2C。** 适用于面向消费者应用的标识和访问管理解决方案。 有关详细信息，请参阅 [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)。</li><li>**Azure 多重身份验证。** 按用户或按验证提供程序使用。 有关详细信息，请参阅[什么是 Azure 多重身份验证？](../authentication/multi-factor-authentication.md)。

## <a name="as-an-admin-how-do-i-get-started"></a>管理员如何入门？
注册免费的 30 天试用版并部署第一个云解决方案，请参阅 [Azure Active Directory Premium 试用版](https://azure.microsoft.com/trial/get-started-active-directory/)。

## <a name="as-a-developer-how-do-i-get-started"></a>开发人员如何入门？
注册免费的 30 天试用版并开始将应用与 Azure AD 集成，请参阅 [Azure Active Directory Premium 试用版](https://azure.microsoft.com/trial/get-started-active-directory/)。 有关详细信息，还可以参阅 Azure Active directory 的[开发者指南](../develop/azure-ad-developers-guide.md)。

## <a name="next-steps"></a>后续步骤
- [详细了解 Azure 标识和访问管理的基础知识](identity-fundamentals.md)。

- [将 Azure AD 与 Windows Server Active Directory 集成](../hybrid/how-to-connect-install-express.md)。
