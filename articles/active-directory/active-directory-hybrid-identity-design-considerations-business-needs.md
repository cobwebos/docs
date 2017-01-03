---
title: "Azure Active Directory 混合标识设计注意事项 - 确定标识要求 | Microsoft 文档"
description: "识别公司的业务需求，引导读者定义混合标识设计的要求。"
documentationcenter: 
services: active-directory
author: billmath
manager: femila
editor: 
ms.assetid: de690978-84ef-41ad-9dfe-785722d343a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 73b5a52b143d17283f677ce3181d8381db41d3c4


---
# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>确定混合标识解决方案的标识要求
设计混合标识解决方案的第一个步骤是确定要利用此解决方案的企业组织有何要求。  混合标识最初是一个支持角色（通过提供身份验证来支持其他所有云解决方案），然后提供一些新奇的功能，为用户解锁新的工作负荷。  要为用户采用的这些工作负荷或服务指明了混合标识设计的要求。  这些服务和工作负荷在本地和云中都需要利用混合标识。  

我们需要通盘审视企业的各个主要层面，了解其当前要求，以及公司的将来规划。 如果不清楚混合标识设计的长期策略，解决方案将来有可能无法随着企业的成长和变化而缩放。   下图中的示例演示了混合标识体系结构以及要为用户解锁的工作负荷。 此示例只是说明可以通过健全的混合标识策略解锁和提供的各种新机会。 

属于混合标识架构的部分组件 ![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>确定业务需求
每家公司都有不同的要求，即使这些公司属于相同的行业，实际的业务要求仍可能有所不同。 可以继续利用行业最佳实践，但最终引导定义混合标识设计要求的仍是公司的业务需求。 

请务必回答以下问题来识别业务需求：

* 公司是否想要降低 IT 运营成本？
* 公司是否想要保护云资产（SaaS 应用、基础结构）？
* 公司是否要将 IT 现代化？
  * 用户是否经常移动办公，高度依赖 IT 资源满足外围网络的预期需求，以便允许不同类型的流量访问不同的资源？
  * 公司是否有需要发布给这些新潮用户、但并不容易改写的传统应用？
  * 公司是否需要在全盘掌控的情况下完成这些任务？
* 公司是否想要在本地导入新工具，利用 Microsoft Azure 安全专业技术保护用户的标识和降低风险？
* 公司是否正在尝试从本地摆脱可怕的“外部”帐户并将其转移到云中，使其不再是本地环境中的潜在威胁？

## <a name="analyze-on-premises-identity-infrastructure"></a>分析本地标识基础结构
对公司的业务要求有所了解后，需要评估本地标识基础结构。 在定义将当前标识解决方案集成到云标识管理系统所要解决的技术要求时，此项评估非常重要。 务必回答以下问题：

* 公司在本地使用哪种身份验证和授权解决方案？ 
* 公司目前是否有任何本地同步服务？
* 公司是否使用任何第三方标识提供者 (IdP)？

此外，需要认识公司可能使用的云服务。 执行评估了解环境中当前与 SaaS、IaaS 或 PaaS 模型的集成，这一点非常重要。 请务必在此评估期间回答以下问题：

* 贵公司是否与任何云服务提供程序集成？
* 如果是，使用了哪些服务？
* 此集成目前已投入使用，还是在试运行期？

> [!NOTE]
> 如果无法正确映射所有应用与云服务，可以使用 Cloud App Discovery 工具。 IT 部门可以使用此工具深入了解组织的所有业务云应用与使用者云应用。 发现组织中的影子 IT 将变得前所未有的轻松，就连使用模式的详细信息，以及正在访问云应用程序的用户都能发现。 若要访问此工具，请转到 [https://appdiscovery.azure.com](https://appdiscovery.azure.com/)
> 
> 

## <a name="evaluate-identity-integration-requirements"></a>评估标识集成要求
接下来，需要评估标识集成要求。 若要定义有关用户身份验证方式、组织在云中的存在状态、组织提供授权的方式以及提供何种用户体验的技术要求，必须执行此评估。 务必回答以下问题：

* 组织是否使用联合和/或标准身份验证？
* 是否必须使用联合身份验证？  原因如下：
  * 基于 Kerberos 的 SSO
  * 公司的某个本地应用程序（内置或第三方）使用 SAML 或类似的联合身份验证功能。
  * 通过智能卡执行 MFA。 RSA SecurID 等等。
  * 可以解决以下问题的客户端访问规则：
    1. 是否可以根据客户端的 IP 地址阻止对 Office 365 的所有外部访问？
    2. 是否可以阻止对 Office 365 的所有外部访问（Exchange ActiveSync 除外）？
    3. 是否可以阻止对 Office 365 的所有外部访问（基于浏览器的应用（OWA、SPO）除外）？
    4. 是否可以阻止指定的 AD 组成员对 Office 365 的所有外部访问？
* 安全/审核考虑因素
* 对联合身份验证的既有投资
* 组织在云中的域将使用哪种名称？
* 组织是否有自定义域？
  1. 该域是否公开，可通过 DNS 轻松验证？
  2. 如果不是，是否有公共域可用于在 AD 中注册备用 UPN？
* 云中显示的用户标识符是否一致？ 
* 组织是否有需要与云服务集成的应用？
* 组织是否有多个域，这些域是否全都使用标准身份验证或联合身份验证？

## <a name="evaluate-applications-that-run-in-your-environment"></a>评估环境中运行的应用程序
对本地和云基础结构有所了解后，需要评估这些环境中运行的应用程序。 若要定义有关如何将这些应用程序集成到云标识管理系统的技术要求，必须执行此项评估。 务必回答以下问题：

* 应用程序驻留在何处？
* 用户是要访问本地应用程序，  还是访问云中的应用程序？ 或者访问这两种应用程序？
* 是否有将现有应用程序工作负荷转移到云的计划？
* 是否有开发驻留在本地或云中，但使用云身份验证的新应用程序的计划？

## <a name="evaluate-user-requirements"></a>评估用户要求
此外，必须评估用户要求。 若要定义在用户过渡到云时登记用户并为其提供帮助所要执行的步骤，必须执行此项评估。 务必回答以下问题：

* 用户是否要访问本地应用程序？
* 用户是否要访问云中的应用程序？
* 用户通常以哪种方式登录到其本地环境？
* 如何将用户登录到云？

> [!NOTE]
> 务必记下每个答案并了解答案背后的依据。 [确定事件响应要求](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)中介绍了可用的选项，以及每个选项的优缺点。  回答了这些问题之后，就会挑选出最适合你的业务需求的选项。
> 
> 

## <a name="next-steps"></a>后续步骤
[确定目录同步要求](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>另请参阅
[设计注意事项概述](active-directory-hybrid-identity-design-considerations-overview.md)




<!--HONumber=Dec16_HO5-->


