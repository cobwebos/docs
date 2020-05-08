---
title: 设计 Microsoft Azure 上的安全应用程序
description: 本文介绍了在 web 应用程序项目的要求和设计阶段要考虑的最佳实践。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 533f287693ca8aac76a3233674d95f3f49d4ae22
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857171"
---
# <a name="design-secure-applications-on-azure"></a>在 Azure 上设计安全应用程序
本文介绍了在为云设计应用程序时要考虑的安全活动和控制措施。 还介绍了在 Microsoft[安全开发生命周期（SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的要求和设计阶段，培训资源以及要考虑的安全问题和概念。 其目标是帮助你定义可用于设计更安全的应用程序的活动和 Azure 服务。

本文介绍了以下 SDL 阶段：

- 培训
- 要求
- 设计

## <a name="training"></a>培训
在开始开发云应用程序之前，请花些时间了解 Azure 上的安全和隐私。 通过执行此步骤，可以减少应用程序中可被利用的漏洞的数量和严重性。 你将能够更好地对不断变化的威胁环境作出反应。

在训练阶段使用以下资源来熟悉开发人员提供的 Azure 服务和 Azure 上的最佳安全方案：

  - [Azure 开发人员指南](https://azure.microsoft.com/campaigns/developer-guide/)介绍了如何开始 azure。 本指南向您展示了可用于运行应用程序的服务、存储数据、并入智能、构建 IoT 应用程序，以及以更高效、更安全的方式部署解决方案。

  - [Azure 开发人员入门指南](../../guides/developer/azure-developer-guide.md)为寻找开始使用 Azure 平台满足其开发需求的开发人员提供了基本信息。

  - [Sdk 和工具](https://docs.microsoft.com/azure/index?pivot=sdkstools)介绍 Azure 上可用的工具。

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/)提供开发协作工具。 这些工具包括高性能管道、免费的 Git 存储库、可配置的看板以及广泛的自动化和基于云的负载测试。
    [DevOps 资源中心](https://docs.microsoft.com/azure/devops/learn/)结合了我们的资源，以实现学习 DevOps 实践、Git 版本控制、敏捷方法、我们如何在 Microsoft 中使用 DevOps，以及如何评估你自己的 DevOps 进度。

  - [推送到生产之前要考虑的前5个安全事项](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca)说明了如何帮助保护 Azure 上的 web 应用程序并保护应用免受最常见和危险性最高的 web 应用程序攻击。

  - [适用于 Azure 的 Secure DevOps 工具包](https://azsk.azurewebsites.net/index.html)是一个脚本、工具、扩展和自动化的集合，该集合适用于使用广泛的自动化的 DevOps 团队的综合性 Azure 订阅和资源安全需求。 适用于 Azure 的 Secure DevOps 工具包可以向你展示如何将安全性平稳集成到你的本机 DevOps 工作流中。 工具包提供了安全验证测试（SVTs）等工具，可帮助开发人员编写安全代码，并在编码和早期开发阶段测试云应用程序的安全配置。

  - [Azure 解决方案的最佳安全方案](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions)提供一系列安全最佳做法，以便在使用 Azure 设计、部署和管理云解决方案时使用。

## <a name="requirements"></a>要求
要求定义阶段是定义应用程序的内容以及发布应用程序时将执行的操作的关键步骤。 "要求" 阶段也是考虑你将在应用程序中生成的安全控制的时间。 在此阶段，还会开始在整个 SDL 中执行的步骤，以确保发布和部署安全的应用程序。

### <a name="consider-security-and-privacy-issues"></a>考虑安全和隐私问题
此阶段是考虑基础安全和隐私问题的最佳时间。 在项目开始时定义可接受的安全和隐私级别可帮助团队：

- 了解与安全问题相关的风险。
- 在开发过程中确定和修复安全错误。
- 在整个项目中应用既定的安全和隐私级别。

编写应用程序的要求时，请确保考虑有助于确保应用程序和数据安全的安全控制。

### <a name="ask-security-questions"></a>询问安全问题
提出安全问题，例如：

  - 我的应用程序是否包含敏感数据？

  - 我的应用程序是收集还是存储需要遵守行业标准和法规遵从性计划[（如联邦金融机构检查委员会（FFIEC）](../blueprints/ffiec-analytics-overview.md)或[支付卡行业数据安全标准（PCI DSS））](../blueprints/pcidss-analytics-overview.md)的数据？

  - 我的应用程序是收集还是包含敏感的个人数据或客户数据，这些数据可以单独使用，也可以与其他信息一起使用，以确定、联系或查找单个人员？

  - 我的应用程序是收集还是包含可用于访问个人的医疗、教育、财务或聘用信息的数据？ 在需求阶段确定数据的敏感性有助于对数据进行分类，并识别将用于应用程序的数据保护方法。

  - 如何存储数据？ 考虑如何监视应用程序用于任何意外更改（如响应时间较慢）的存储服务。 是否能够影响日志记录以收集更详细的数据并深入分析问题？

  - 我的应用程序是公开的（在 internet 上）还是在内部使用？ 如果你的应用程序可供公开使用，你如何保护可能被收集的数据不会以错误的方式使用呢？ 如果你的应用程序仅在内部使用，请考虑你的组织中的哪些人员应该有权访问该应用程序以及他们应该有多长时间才能访问。

  - 开始设计应用程序之前，你是否了解标识模型？ 你如何确定用户是谁说他们是谁，以及用户有权执行哪些操作？

  - 我的应用程序是否执行敏感或重要的任务（例如转让资金、解除门的锁定或交付医科）？
    请考虑如何验证执行敏感任务的用户是否有权执行该任务，以及如何验证该用户的身份。 授权（AuthZ）是指向经过身份验证的安全主体授予执行某些操作的权限。 身份验证（身份验证）是一种向参与方提供合法凭据的行为。

  - 我的应用程序是否执行任何有风险的软件活动，如允许用户上传或下载文件或其他数据？ 如果你的应用程序执行了危险活动，请考虑你的应用程序将如何保护用户处理恶意文件或数据。

### <a name="review-owasp-top-10"></a>查看 OWASP 前10个
考虑查看[<span class="underline">OWASP 前10个应用程序安全风险</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)。
OWASP Top 10 解决了 web 应用程序面临的严重安全风险。
认识到这些安全风险可帮助你做出要求和设计决策，以最大程度地降低应用程序的风险。

考虑用于防止违规的安全控制措施非常重要。
但是，您还需要[假设发生了破坏](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops)。 假设违规会提前回答一些有关安全问题的重要问题，因此不需要在紧急情况下解答这些问题：

  - 如何检测攻击？

  - 如果存在攻击或违规情况，该怎么办？

  - 如何从数据泄露或篡改等攻击中恢复？

## <a name="design"></a>设计

设计阶段对于制定设计和功能规范的最佳实践至关重要。 它对于执行风险分析也至关重要，有助于减少整个项目中的安全和隐私问题。

如果你有安全要求并使用安全的设计概念，你可以避免或尽量减少安全漏洞的机会。 在应用程序的设计中，安全漏洞是对应用程序设计的一项监督，使用户可以在发布应用程序后执行恶意或意外操作。

在设计阶段，还应考虑如何在层中应用安全性;一道防御措施并不一定能满足需要。 如果攻击者过去你的 web 应用程序防火墙（WAF），会发生什么情况呢？ 你需要另一个安全控制来防范这种攻击。

考虑到这一点，我们将讨论以下安全设计概念，以及在设计安全应用程序时应满足的安全控制：

- 使用安全的编码库和软件框架。
- 扫描易受攻击的组件。
- 在应用程序设计过程中使用威胁建模。
- 减小攻击面。
- 采用标识策略作为主要安全外围网络。
- 重要事务需要重新进行身份验证。
- 使用密钥管理解决方案来保护密钥、凭据和其他机密。
- 保护敏感数据。
- 实现故障安全措施。
- 利用错误和异常处理。
- 使用日志记录和警报。

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>使用安全的编码库和软件框架

对于开发，请使用安全的编码库和具有内嵌安全性的软件框架。 开发人员可以使用现有的、经验证的功能（加密、输入下水道、输出编码、密钥或连接字符串以及任何其他被视为安全控制的内容），而不是从头开发安全控制。 这有助于防范安全相关的设计和实现缺陷。

请确保使用最新版本的框架和框架中提供的所有安全功能。 Microsoft 为所有开发人员提供了一[套全面的开发工具](https://azure.microsoft.com/product-categories/developer-tools/)，可使用任何平台或语言提供云应用程序。 您可以通过从各种[sdk](https://azure.microsoft.com/downloads/)中进行选择，使用您选择的语言进行编码。
你可以利用功能全面的集成开发环境（Ide）和具有高级调试功能的编辑器以及内置的 Azure 支持。

Microsoft 提供多种[语言、框架和工具](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all)，可用于在 Azure 上开发应用程序。 例如， [Azure 适用于 .net 和 .Net Core 开发人员](https://docs.microsoft.com/dotnet/azure/)。 对于我们提供的每种语言和框架，你会发现快速入门、教程和 API 参考，以帮助你快速入门。

Azure 提供了多种可用于托管网站和 web 应用程序的服务。 这些服务使你能够以你最喜欢的语言进行开发，无论是 .NET、.NET Core、Java、Ruby、node.js、PHP 还是 Python。
[Azure App Service Web 应用](../../app-service/overview.md)（web 应用）是这些服务之一。

Web 应用将 Microsoft Azure 的功能添加到应用程序。 它包括安全性、负载均衡、自动缩放和自动化管理。 你还可以利用 Web 应用中的 DevOps 功能，例如包管理、过渡环境、自定义域、SSL/TLS 证书，以及从 Azure DevOps、GitHub、Docker 中心和其他源进行的持续部署。

Azure 提供了可用于托管网站和 web 应用程序的其他服务。 大多数情况下，Web 应用是最佳选择。 对于微服务体系结构，请考虑使用[Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。
如果需要更好地控制运行代码的 VM，请考虑使用 [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)。
有关如何在这些 Azure 服务之间进行选择的详细信息，请参阅[Azure App Service、虚拟机、Service Fabric 和云服务的比较](/azure/architecture/guide/technology-choices/compute-decision-tree)。

### <a name="apply-updates-to-components"></a>将更新应用于组件

若要防止出现漏洞，应持续清点客户端和服务器端组件（例如，框架和库）及其更新依赖项。 新漏洞和更新的软件版本不断发布。 确保你有一个正在进行的计划来监视、会审并将更新或配置更改应用于所使用的库和组件。

请参阅[打开 Web 应用程序安全项目（OWASP）](https://www.owasp.org/index.php/Main_Page)页，了解如何在工具建议中[使用具有已知漏洞的组件](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities)。 你还可以订阅电子邮件警报，查看与你使用的组件有关的安全漏洞。

### <a name="use-threat-modeling-during-application-design"></a>在应用程序设计期间使用威胁建模

威胁建模是确定业务和应用程序的潜在安全威胁的过程，然后确保适当的缓解措施到位。 SDL 指定团队应在设计阶段进行威胁建模，在解决潜在问题时相对简单且经济高效。 在设计阶段使用威胁建模可以极大地降低开发总成本。

为帮助简化威胁建模过程，我们设计了[SDL Threat Modeling Tool](threat-modeling-tool.md) ，其中包含非安全专家。 此工具通过提供有关如何创建和分析威胁模型的清楚指导，使所有开发人员能够更轻松地进行威胁建模。

在所有信任边界之间对应用程序设计和枚举[STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)威胁进行建模，从而在所有信任边界中进行了一种有效的方法来捕获设计错误。 下表列出了 STRIDE 威胁，并提供了一些使用 Azure 提供的功能的示例缓解措施。 这些缓解措施并非在每种情况下都起作用。

| 威胁 | 安全属性 | 潜在的 Azure 平台缓解 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 欺骗               | 身份验证        | [需要 HTTPS 连接](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio)。 |
| 篡改              | 完整性             | 验证 SSL/TLS 证书。 使用 SSL/TLS 的应用程序必须完全验证其连接到的实体的 x.509 证书。 使用 Azure Key Vault 证书来[管理 x509 证书](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)。 |
| 否认            | 不可否认性       | 启用 Azure [监视和诊断](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。|
| 信息泄露 | 机密性       | 加密和[传输中](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)[的敏感](../fundamentals/encryption-atrest.md)数据。 |
| 拒绝服务      | 可用性          | 监视性能指标，以了解潜在的拒绝服务情况。 实现连接筛选器。 [Azure DDoS 保护](../../virtual-network/ddos-protection-overview.md#next-steps)与应用程序设计最佳做法相结合，提供对 DDoS 攻击的防御。|
| 特权提升 | 授权         | 使用 Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)。|

### <a name="reduce-your-attack-surface"></a>减小攻击面

攻击面是可能出现潜在漏洞的总次数。 本文重点介绍应用程序的攻击面。
重点是保护应用程序免受攻击。 最大程度地减少攻击面，一种简单快捷的方法是从应用程序中删除未使用的资源和代码。 应用程序越小，攻击面就越小。 例如，删除：

- 尚未发布的功能的代码。
- 调试支持代码。
- 未使用或已弃用的网络接口和协议。
- 不使用的虚拟机和其他资源。

定期清除资源并确保删除未使用的代码是一种很好的方法，可确保恶意攻击者遭受攻击的机会更少。

若要减少攻击面，更详细、更深入的方法是完成攻击面分析。 攻击面分析可帮助你映射系统中需要检查和测试的部分以发现安全漏洞。

攻击面分析的目的是了解应用程序中的风险领域，使开发人员和安全专家知道应用程序的哪些部分受到了攻击。 然后，您可以找到最大程度地降低这种可能性的方法，跟踪攻击面更改的时间和方式，以及这从风险角度的意义。

攻击面分析可帮助您确定：

- 需要查看和测试安全漏洞的系统功能和部件。
- 需要纵深防御保护（系统中需要保护的部分）的高风险代码区域。
- 当你改变攻击面并需要刷新威胁评估时。

为了降低攻击者利用潜在薄弱环节的机会，要求您全面分析应用程序的总体攻击面。 它还包括禁用或限制对系统服务的访问、应用最低权限原则，并尽可能采用分层防御原则。

我们讨论如何在 SDL 验证阶段进行[攻击面检查](secure-develop.md#conduct-attack-surface-review)。

> [!NOTE]
> **威胁建模和攻击面分析之间的区别是什么？**
威胁建模是确定应用程序的潜在安全威胁的过程，并确保适当地防范威胁。 攻击面分析识别开放攻击的代码的高风险区域。 它涉及到如何在部署应用程序之前查找保护应用程序的高风险区域以及查看和测试这些代码区域的方法。

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>采用标识用作主要安全边界的策略

设计云应用程序时，从以网络为中心的方式到以以身份为中心的方法，以以网络为中心的方式重点，这一点很重要。 在过去，本地安全外围网络是组织的网络。 大多数本地安全设计使用网络作为主要的安全透视。 对于云应用程序，你可以通过将标识作为主要安全边界来提供更好的服务。

要开发以身份为中心的 web 应用程序开发方法，您可以执行以下操作：

- 为用户强制实施多重身份验证。
- 使用强身份验证和授权平台。
- 应用最小特权原则。
- 实现实时访问。

#### <a name="enforce-multi-factor-authentication-for-users"></a>为用户强制实施多重身份验证

使用双重身份验证。 双因素身份验证是当前用于身份验证和授权的标准，因为它可以避免身份验证的用户名和密码类型固有的安全漏洞。 应将对 Azure 管理接口（Azure 门户/远程 PowerShell）和面向客户的服务的访问权限设计并配置为使用[Azure 多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)。

#### <a name="use-strong-authentication-and-authorization-platforms"></a>使用强身份验证和授权平台

使用平台提供的身份验证和授权机制，而不要使用自定义代码。 这是因为开发自定义身份验证代码可能容易出错。 通常会广泛检查商业代码（例如，通过 Microsoft）的安全性。 [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用于标识和访问管理的 Azure 解决方案。 这些 Azure AD 工具和服务可帮助进行安全的开发：

- [Azure AD 标识平台（开发人员 Azure AD）](../../active-directory/develop/about-microsoft-identity-platform.md)是一项云标识服务，开发人员使用它来构建可安全登录用户的应用程序。 Azure AD 为正在开发多租户应用的单租户、业务线（LOB）应用和开发人员提供开发人员提供帮助。 除了基本登录，使用 Azure AD 构建的应用还可以调用 Microsoft Api 以及在 Azure AD 平台上构建的自定义 Api。 Azure AD 标识平台支持行业标准协议，如 OAuth 2.0 和 OpenID Connect。

- [Azure Active Directory B2C （Azure AD B2C）](../../active-directory-b2c/index.yml)是一项标识管理服务，可用于自定义和控制客户使用应用程序时的注册、登录和管理配置文件的方式。 这包括为 iOS、Android 和 .NET 开发的应用程序，等等。 Azure AD B2C 在保护客户标识时启用这些操作。

#### <a name="apply-the-principle-of-least-privilege"></a>应用最小特权原则

[最小特权](https://en.wikipedia.org/wiki/Principle_of_least_privilege)的概念意味着向用户提供完成其工作所需的准确级别的访问和控制。

软件开发人员是否需要域管理员权限？ 管理助手是否需要访问其个人计算机上的管理控制？ 评估对软件的访问权限没有什么不同。 如果你使用[基于角色的访问控制（RBAC）](../../role-based-access-control/overview.md)来向用户授予应用程序中不同的功能和颁发机构，则不会为每个人授予所有内容的访问权限。 通过限制对每个角色所需的访问权限，可以限制出现安全问题的风险。

确保你的应用程序在其访问模式中强制实施[最小特权](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications)。

> [!NOTE]
> 最小特权规则需要应用于软件和创建软件的人。 如果为软件开发人员提供过多的访问权限，软件开发人员可能会面临巨大的风险。 如果开发人员有恶意的意图，或者对其进行了太多的访问，则结果可能很严重。 建议在整个开发生命周期中将最低权限规则应用到开发人员。

#### <a name="implement-just-in-time-access"></a>实现实时访问

实现*实时*（JIT）访问，进一步降低权限的暴露时间。 使用[Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-take-control-of-admin-activity)执行以下操作：

- 向用户授予仅 JIT 所需的权限。
- 分配时限更短的角色，确信权限会自动撤消。

### <a name="require-re-authentication-for-important-transactions"></a>重要事务需要重新进行身份验证

[跨站点请求伪造](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1)（也称为*XSRF*或*CSRF*）是一种针对 web 托管应用的攻击，恶意 web 应用会影响客户端浏览器和信任该浏览器的 web 应用之间的交互。 跨站点请求伪造攻击是可能的，因为 web 浏览器会自动向网站发出每个请求的身份验证令牌。
这种形式的攻击也称为*一键式攻击*或*会话 riding* ，因为攻击利用用户以前的经过身份验证的会话。

防御这种攻击的最佳方式是，要求用户提供只有用户可以在每个重要事务之前提供的内容，例如购买、帐户停用或密码更改。 你可能会要求用户重新输入其密码、完成 captcha，或者只提交用户所拥有的机密令牌。 最常见的方法是机密令牌。

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>使用密钥管理解决方案来保护密钥、凭据和其他机密

丢失密钥和凭据是一个常见问题。 唯一比丢失密钥和凭据更遭糕的事情是让未经授权的一方获取这些密钥和凭据的访问权限。 攻击者可以利用自动和手动技术来查找存储在代码存储库（如 GitHub）中的密钥和机密。 请勿在这些公共代码存储库中或任何其他服务器上放置密钥和机密。

请始终将密钥、证书、机密和连接字符串放在密钥管理解决方案中。 你可以使用集中式解决方案，其中的密钥和机密存储在硬件安全模块（Hsm）中。 Azure 通过[Azure Key Vault](../../key-vault/general/overview.md)提供云中的 HSM。

Key Vault 是*机密存储*：这是用于存储应用程序机密的集中式云服务。 Key Vault 通过将应用程序机密保存在单个中心位置，并提供安全访问、权限控制和访问日志记录，使机密数据保持安全。

机密存储在单个*保管库*中。 每个保管库都有其自己的配置和安全策略来控制访问。 你可以通过 REST API 或通过适用于大多数编程语言的客户端 SDK 获取数据。

> [!IMPORTANT]
> Azure Key Vault 旨在存储服务器应用程序的配置密码。 它不用于存储属于应用用户的数据。 这反映在其性能特征、API 和成本模型中。
>
> 用户数据应存储在其他位置，例如在具有透明数据加密（TDE）的 Azure SQL 数据库实例或使用 Azure 存储服务加密的存储帐户中。 应用程序用来访问这些数据存储的机密可以保留在 Azure Key Vault 中。

### <a name="protect-sensitive-data"></a>保护敏感数据

保护数据是安全策略的重要组成部分。
对你的数据进行分类并识别你的数据保护需求可帮助你在设计应用时考虑数据安全。 按敏感度对存储的数据进行分类（分类）和业务影响有助于开发人员确定与数据相关的风险。

在设计数据格式时，将所有适用的数据标记为敏感数据。 确保应用程序将适用的数据视为敏感数据。 这些做法可帮助保护敏感数据：

- 使用加密。
- 避免对机密（如密钥和密码）进行硬编码。
- 确保访问控制和审核已就位。

#### <a name="use-encryption"></a>使用加密

保护数据应该是安全策略的重要组成部分。
如果数据存储在数据库中，或者在位置之间来回移动，请使用[静态数据](../fundamentals/encryption-atrest.md)加密（在数据库中）和[传输中的数据](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)加密（从用户、数据库、API 或服务终结点进行传输）。 建议始终使用 SSL/TLS 协议来交换数据。 确保使用最新版本的 TLS 进行加密（目前为版本1.2）。

#### <a name="avoid-hard-coding"></a>避免进行硬编码

某些东西决不会在软件中进行硬编码。 例如，主机名或 IP 地址、Url、电子邮件地址、用户名、密码、存储帐户密钥和其他加密密钥。 请考虑在代码的注释部分（包括代码的注释部分），实现在代码中可以或不能进行硬编码的要求。

在代码中放置注释时，请确保不保存任何敏感信息。 这包括你的电子邮件地址、密码、连接字符串、你的应用程序的有关应用程序的信息，这些信息只是组织中的某个人所知，任何其他可能会让攻击者利用它来利用你的应用程序或组织。

基本上，假设开发项目中的所有内容都将在部署时成为公共知识。 避免在项目中包含任何类型的敏感数据。

前面讨论[Azure Key Vault](../../key-vault/general/overview.md)。 你可以使用 Key Vault 来存储密钥和密码等机密，而不是对其进行硬编码。 将 Key Vault 与 Azure 资源的托管标识结合使用时，Azure web 应用可轻松安全地访问机密配置值，而无需在源代码管理或配置中存储任何机密。 若要了解详细信息，请参阅[通过 Azure Key Vault 管理服务器应用中的机密](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)。

### <a name="implement-fail-safe-measures"></a>实现故障安全措施

您的应用程序必须能够以一致的方式处理执行期间发生的[错误](https://docs.microsoft.com/dotnet/standard/exceptions/)。 应用程序应该捕获所有错误，并失败或关闭。

你还应确保记录的错误具有足够的用户上下文来识别可疑或恶意活动。 日志应该留出足够的时间，以允许延迟取证分析。 日志的格式应该可由日志管理解决方案轻松使用。 确保触发与安全相关的错误警报。 日志记录和监视不足，使得攻击者能够进一步攻击系统并保持持久性。

### <a name="take-advantage-of-error-and-exception-handling"></a>利用错误和异常处理

实现正确的错误和[异常处理](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions)是防御性编码的重要组成部分。 错误和异常处理对于使系统可靠和安全至关重要。 错误处理中的错误可能导致各种安全漏洞，例如向攻击者泄露信息以及帮助攻击者了解有关平台和设计的详细信息。

请确保：

- 可以通过集中式方式处理异常，以避免代码中的重复[try/catch 块](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions)。

- 所有意外的行为都在应用程序中进行处理。

- 向用户显示的消息不会泄露重要数据，但会提供足够的信息来解释此问题。

- 记录了异常，并为取证或事件响应团队提供了足够的信息来调查。

[Azure 逻辑应用](../../logic-apps/logic-apps-overview.md)提供了用于处理由依赖系统引起的[错误和异常](../../logic-apps/logic-apps-exception-handling.md)的一流体验。 可以使用逻辑应用创建工作流，以自动执行跨企业和组织集成应用、数据、系统和服务的任务和流程。

### <a name="use-logging-and-alerting"></a>使用日志记录和警报

[记录](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)安全调查的安全问题，并触发有关问题的警报，以确保用户及时了解问题。 在所有组件中启用审核与日志记录。 审核日志应该捕获用户上下文并标识所有重要事件。

检查是否不记录用户提交到网站的任何敏感数据。 敏感数据的示例包括：

- 用户凭据
- 身份证号或其他识别信息
- 信用卡号或其他财务信息
- 运行状况信息
- 可用于对加密信息进行解密的私钥或其他数据
- 可以用来增强应用程序攻击效果的系统信息或应用程序信息

确保应用程序监视用户管理事件，例如成功和失败的用户登录、密码重置、密码更改、帐户锁定和用户注册。 这些事件的日志记录有助于检测并响应潜在的可疑行为。 它还允许收集操作数据，如访问应用程序的人员。

## <a name="next-steps"></a>后续步骤
在下面的文章中，我们建议可以帮助你开发和部署安全应用程序的安全控制和活动。

- [开发安全应用程序](secure-develop.md)
- [部署安全的应用程序](secure-deploy.md)
