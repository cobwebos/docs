---
title: "什么是 Azure Active Directory？"
description: "使用 Azure Active Directory 将现有的本地标识扩展到云中，或开发 Azure AD 集成的应用程序。"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.assetid: 498820c4-9ebe-42be-bda2-ecf38cc514ca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: b6746afd508832afbd54153851b6f2ae404af147
ms.contentlocale: zh-cn
ms.lasthandoff: 07/26/2017

---
# <a name="what-is-azure-active-directory"></a>什么是 Azure Active Directory？
Azure Active Directory (Azure AD) 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。 Azure AD 将核心目录服务、高级标识监管和应用程序访问管理相结合。 Azure AD 还提供功能丰富、基于标准的平台，该平台支持开发人员根据集中的策略和规则为应用程序提供访问控制。 

对于 IT 管理员而言，Azure AD 提供经济实惠、易于使用的解决方案，使员工和业务合作伙伴能够使用单一登录 (SSO) 功能来访问[数千种云 SaaS 应用程序](active-directory-saas-tutorial-list.md)，例如 Office365、Salesforce.com、DropBox 和 Concur。

对于应用程序开发人员而言，Azure AD 可让你专注于构建应用程序，快速方便地集成数百万个全球各地组织所用的一流标识管理解决方案。

Azure AD 还包含整套标识管理功能，例如多重身份验证、设备注册、自助密码管理、自助组管理、特权帐户管理、基于角色的访问控制、应用程序使用情况监视、多样化审核以及安全监视和警报。 这些功能可以帮助保护基于云的应用程序的安全，简化 IT 流程，削减成本，以及确保实现公司的合规目标。

此外，只需[单击四下](./connect/active-directory-aadconnect-get-started-express.md)，Azure AD 就能与现有的 Windows Server Active Directory 集成，使组织能够运用现有的本地标识管理系统投资来管理对基于云的 SaaS 应用程序的访问。

Office 365、Azure 或 Dynamics CRM Online 的客户可能不知道自己已在使用 Azure AD。 每个 Office 365、Azure 和 Dynamics CRM 租户实际上都已经是 Azure AD 租户。 每当有需要时，你就可以开始使用该租户来为 Azure AD 集成的其他数千种云应用程序管理访问权限！

![Azure AD Connect 堆栈](./media/active-directory-whatis/Azure_Active_Directory.png)

## <a name="how-reliable-is-azure-ad"></a>Azure AD 的可靠性如何？
Azure AD 的多租户、地理分布、高可用性设计意味着你可以依赖它来解决最关键的业务需求。 在全球运转的 28 个可自动故障转移的数据中心使你能够体会到 Azure AD 的高度可靠，即使数据中心发生服务中断，你的目录数据也至少会在两个以上的地域分散的数据中心保留副本，而且可让你立即访问。

有关详细信息，请参阅[服务级别协议](https://azure.microsoft.com/support/legal/sla/)。

## <a name="choose-an-edition"></a>选择版本
所有 Microsoft Online 业务服务都依赖于 Azure Active Directory (Azure AD) 进行登录及满足其他标识需求。 如果订阅了任何 Microsoft Online 业务服务（例如，Office 365 或 Microsoft Azure），则表示已获得 Azure AD，可用于访问所有免费功能。 使用 Azure Active Directory 免费版，你可以管理用户和组、与本地目录同步，以及在 Azure、Office 365 和数千种主流 SaaS 应用程序（如 Salesforce、Workday、Concur、DocuSign、Google Apps、Box、ServiceNow、Dropbox 等）上单一登录。 

若要增强 Azure Active Directory，可以使用 Azure Active Directory Basic、Premium P1、和 Premium P2 版添加付费功能。 Azure Active Directory 付费版建立在现有免费目录基础之上，提供企业级功能，包括自助服务、增强型监视、安全报告、多重身份验证 (MFA) 和移动工作者安全访问。

> [!NOTE]
> 有关这些版本的定价选项，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。 中国地区目前不支持 Azure Active Directory Premium P1、Premium P2 和 Azure Active Directory Basic。 有关详细信息，请通过 Azure Active Directory 论坛与我们联系。
>

* **Azure Active Directory Basic** - 面向具有云优先需求的任务工作者，此版本提供以云为中心的应用程序访问和自助标识管理解决方案。 使用 Azure Active Directory 基本版，你可以增强工作效率并获得成本缩减功能，例如基于组的访问管理、用于云应用程序的自助密码重置、Azure Active Directory 应用程序代理（使用 Azure Active Directory 发布本地 Web 应用程序），以及 99.9％ 正常运行时间的企业级 SLA。
* **Azure Active Directory Premium P1** - Azure Active Directory Premium 版添加了丰富的企业级标识管理功能，并允许各类用户无缝访问本地与云功能，旨在满足组织更加严苛的标识和访问管理需求。 此版本为混合环境中的信息工作者和标识管理员提供一切必要的功能，让他们执行应用程序访问、自助标识和访问管理 (IAM)、标识保护及实现云中的安全性。 它支持动态组和自助服务组管理等高级管理与委派资源。 它包含 Microsoft 标识管理器（一个本地标识与访问管理套件），并提供云写回功能，使本地用户能够使用自助密码重置等解决方案。
* **Azure Active Directory Premium P2** - 这款新产品包含 Azure AD Premium P1，以及新的 Identity Protection 和 Privileged Identity Management 中的所有功能，旨在为所有用户和管理员提供高级保护。 Azure Active Directory Identity Protection 利用数十亿信号，针对应用程序和重要公司数据提供基于风险的条件访问。 此外，我们还使用 Azure Active Directory Privileged Identity Management 帮助管理和保护特权帐户，方便发现、限制和监视管理员以及他们对资源的访问，并在需要时提供实时访问。  

> [!NOTE]
> 许多 Azure Active Directory 功能以“即付即用”版本的形式提供：
>
> * Active Directory B2C 是适用于面向消费者应用程序的标识和访问管理解决方案。 有关更多详细信息，请参阅 [Azure Active Directory B2C](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
> * 可以通过基于用户或基于身份验证的提供程序使用 Azure 多重身份验证。 有关更多详细信息，请参阅[什么是 Azure 多重身份验证？](../multi-factor-authentication/multi-factor-authentication.md)
>

## <a name="how-can-i-get-started"></a>如何入门？

**如果你是 IT 管理员：**

* [试试看！](https://azure.microsoft.com/trial/get-started-active-directory/) - 现在就可以使用此链接注册 30 天免费试用版，然后在不到 5 分钟内部署第一个云解决方案

* 阅读 [Azure AD 入门](https://docs.microsoft.com/azure/active-directory/active-directory-get-started-premium)，获取有关如何快速启动和运行 Azure AD 租户的提示与技巧

**如果你是开发人员：**
 
* 查看 Azure Active Directory 的[开发人员指南](active-directory-developers-guide.md)

* [开始试用](https://azure.microsoft.com/trial/get-started-active-directory/) - 立即注册 30 天免费试用版，开始集成应用与 Azure AD

## <a name="next-steps"></a>后续步骤
[详细了解 Azure 标识和访问管理的基础知识](https://docs.microsoft.com/azure/active-directory/identity-fundamentals)

