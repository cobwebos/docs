---
title: 在 Microsoft Azure 上设计安全应用程序
description: 本文讨论了在 Web 应用程序项目的需求和设计阶段需要考虑的最佳做法。
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
ms.openlocfilehash: c9e3cfa689f2e528f4d20e796017ae9d91c29fe2
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461712"
---
# <a name="design-secure-applications-on-azure"></a>在 Azure 上设计安全应用程序
在本文中，我们将介绍在为云设计应用程序时要考虑的安全活动和控制措施。 培训资源以及 Microsoft[安全开发生命周期 （SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的要求和设计阶段需要考虑的安全问题和概念。 目标是帮助您定义可用于设计更安全应用程序的活动和 Azure 服务。

本文将介绍以下 SDL 阶段：

- 培训
- 要求
- 设计

## <a name="training"></a>培训
在开始开发云应用程序之前，请花时间了解 Azure 上的安全性和隐私。 通过执行此步骤，可以减少应用程序中可利用漏洞的数量和严重性。 您将更准备对不断变化的威胁形势做出适当的反应。

在培训阶段使用以下资源来熟悉开发人员可用的 Azure 服务以及 Azure 上的安全最佳实践：

  - [Azure 开发人员指南](https://azure.microsoft.com/campaigns/developer-guide/)演示如何开始使用 Azure。 本指南显示可以使用哪些服务来运行应用程序、存储数据、整合智能、构建 IoT 应用以及以更高效、更安全的方式部署解决方案。

  - [Azure 开发人员入门指南](../../guides/developer/azure-developer-guide.md)为希望开始使用 Azure 平台以满足其开发需求的开发人员提供了重要信息。

  - [SDK 和工具](https://docs.microsoft.com/azure/index?pivot=sdkstools)描述了 Azure 上可用的工具。

  - [Azure DevOps 服务](https://docs.microsoft.com/azure/devops/)提供开发协作工具。 这些工具包括高性能管道、免费 Git 存储库、可配置看板以及广泛的自动化和基于云的负载测试。
    [DevOps 资源中心](https://docs.microsoft.com/azure/devops/learn/)整合了我们的资源，用于学习 DevOps 实践、Git 版本控制、敏捷方法、我们如何在 Microsoft 使用 DevOps 以及如何评估自己的 DevOps 进度。

  - [在推送到生产之前要考虑的 5 个安全项](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca)将介绍如何帮助保护 Azure 上的 Web 应用程序，并保护您的应用免受最常见和危险的 Web 应用程序攻击。

  - [Azure 的安全 DevOps 工具包](https://azsk.azurewebsites.net/index.html)是脚本、工具、扩展和自动化的集合，可满足使用广泛自动化的 DevOps 团队的全面 Azure 订阅和资源安全需求。 Azure 的安全 DevOps 工具包可以向您展示如何将安全性顺利集成到本机 DevOps 工作流中。 该工具包解决了安全验证测试 （SVT） 等工具，这些工具可帮助开发人员编写安全代码，并在编码和早期开发阶段测试其云应用程序的安全配置。

  - [Azure 解决方案的安全最佳实践](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions)提供了一系列安全最佳实践，用于使用 Azure 设计、部署和管理云解决方案。

## <a name="requirements"></a>要求
需求定义阶段是定义应用程序是什么以及应用程序发布后将执行哪些操作的关键步骤。 需求阶段也是考虑将构建到应用程序中的安全控制的时间。 在此阶段，您还将开始在整个 SDL 中执行的步骤，以确保释放和部署安全应用程序。

### <a name="consider-security-and-privacy-issues"></a>考虑安全和隐私问题
此阶段是考虑基本安全和隐私问题的最佳时机。 在项目开始时定义可接受的安全和隐私级别有助于团队：

- 了解与安全问题相关的风险。
- 在开发过程中识别和修复安全错误。
- 在整个项目中应用既定的安全和隐私级别。

编写应用程序的要求时，请务必考虑有助于保护应用程序和数据安全的安全控制。

### <a name="ask-security-questions"></a>提出安全问题
提出安全问题，如：

  - 我的应用程序是否包含敏感数据？

  - 我的应用程序是否收集或存储需要我遵守行业标准和合规计划的数据，如[联邦金融机构审查委员会 （FFIEC）](../blueprints/ffiec-analytics-overview.md)或[支付卡行业数据安全标准 （PCI DSS）？](../blueprints/pcidss-analytics-overview.md)

  - 我的应用程序是否收集或包含敏感个人或客户数据，这些数据可以单独使用或与其他信息一起使用，以识别、联系或定位一个人？

  - 我的应用程序是否收集或包含可用于访问个人医疗、教育、财务或就业信息的数据？ 识别数据在需求阶段的敏感性可帮助您对数据进行分类，并确定将用于应用程序的数据保护方法。

  - 我的数据存储在何处以及如何存储？ 考虑如何监视应用程序用于任何意外更改（如响应时间变慢）的存储服务。 您是否能够影响日志记录以收集更详细的数据并深入分析问题？

  - 我的申请是向公众开放（在互联网上）还是仅在内部？ 如果您的应用程序可供公众使用，您如何保护可能收集的数据被错误使用？ 如果应用程序仅在内部可用，请考虑组织中谁应有权访问该应用程序以及他们应该具有访问权限的时间。

  - 在开始设计应用程序之前，您是否了解您的身份模型？ 您如何确定用户是谁，他们称他们是谁，以及用户有权做什么？

  - 我的应用程序是否执行敏感或重要的任务（如转账、开门或送药）？
    考虑如何验证执行敏感任务的用户是否有权执行该任务，以及如何验证此人是否属于他们所说的人。 授权 （AuthZ） 是授予经过身份验证的安全主体执行某些操作的权限的行为。 身份验证 （AuthN） 是向一方质疑合法凭据的行为。

  - 我的应用程序是否执行任何危险的软件活动，例如允许用户上传或下载文件或其他数据？ 如果应用程序确实执行了危险的活动，请考虑应用程序将如何保护用户不处理恶意文件或数据。

### <a name="review-owasp-top-10"></a>查看 OWASP 前 10 名
考虑审查[<span class="underline">OWASP 十大应用程序安全风险</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)。
OWASP 前 10 名解决了 Web 应用程序面临的严重安全风险。
了解这些安全风险可以帮助您做出要求和设计决策，将应用程序中的这些风险降至最低。

考虑安全控制以防止违规很重要。
但是，您还希望假定会发生[违规](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops)。 假设违规有助于提前回答一些有关安全性的重要问题，因此不必在紧急情况下回答：

  - 如何检测攻击？

  - 如果发生攻击或违规，我该怎么办？

  - 如何从攻击（如数据泄漏或篡改）中恢复？

## <a name="design"></a>设计

设计阶段对于为设计和功能规范建立最佳实践至关重要。 对于执行风险分析，帮助缓解整个项目的安全和隐私问题，这一点也至关重要。

当您有安全要求并使用安全设计概念时，可以避免或尽量减少出现安全漏洞的机会。 安全缺陷是应用程序设计中的一种疏忽，可能允许用户在应用程序发布后执行恶意或意外操作。

在设计阶段，还要考虑如何在层中应用安全性;一级的防守不一定足够。 如果攻击者通过 Web 应用程序防火墙 （WAF），会发生什么情况？ 您希望有另一个安全控制来防御该攻击。

考虑到这一点，我们讨论了以下安全设计概念以及您在设计安全应用程序时应解决的安全控制：

- 使用安全编码库和软件框架。
- 扫描易受攻击的组件。
- 在应用程序设计期间使用威胁建模。
- 减少攻击面。
- 采用身份策略作为主要安全边界。
- 要求对重要事务重新认证。
- 使用密钥管理解决方案保护密钥、凭据和其他机密。
- 保护敏感数据。
- 实施故障安全措施。
- 利用错误和异常处理。
- 使用日志记录和警报。

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>使用安全编码库和软件框架

对于开发，请使用安全编码库和具有嵌入式安全性的软件框架。 开发人员可以使用现有的、经过验证的功能（加密、输入卫生、输出编码、密钥或连接字符串，以及任何被视为安全控制的功能），而不是从头开始开发安全控制。 这有助于防止与安全相关的设计和实现缺陷。

确保您使用的是框架的最新版本以及框架中提供的所有安全功能。 Microsoft 为所有开发人员提供了一[套全面的开发工具](https://azure.microsoft.com/product-categories/developer-tools/)，用于任何平台或语言，以交付云应用程序。 您可以通过从各种[SDK](https://azure.microsoft.com/downloads/)中选择使用您选择的语言编写代码。
您可以利用具有高级调试功能和内置 Azure 支持的功能齐全的集成开发环境 （II） 和编辑器。

Microsoft 提供了各种[语言、框架和工具，](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all)可用于在 Azure 上开发应用程序。 例如[，Azure 为 .NET 和 .NET 核心开发人员](https://docs.microsoft.com/dotnet/azure/)。 对于我们提供的每种语言和框架，您将找到快速入门、教程和 API 引用，以帮助您快速入门。

Azure 提供了可用于托管网站和 Web 应用程序的各种服务。 这些服务允许您使用您最喜爱的语言进行开发，无论是 .NET、.NET Core、Java、Ruby、Node.js、PHP 还是 Python。
[Azure 应用服务 Web 应用](../../app-service/overview.md)（Web 应用）是这些服务之一。

Web 应用将 Microsoft Azure 的强大功能添加到应用程序中。 它包括安全性、负载平衡、自动缩放和自动化管理。 您还可以利用 Web 应用中的 DevOps 功能，例如包管理、暂存环境、自定义域、SSL/TLS 证书以及 Azure DevOps、GitHub、Docker Hub 和其他源的持续部署。

Azure 提供了可用于托管网站和 Web 应用程序的其他服务。 大多数情况下，Web 应用是最佳选择。 对于微服务体系结构，请考虑[Azure 服务结构](https://azure.microsoft.com/documentation/services/service-fabric)。
如果需要更好地控制运行代码的 VM，请考虑使用 [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)。
有关如何在这些 Azure 服务之间进行选择的详细信息，请参阅 Azure[应用服务、虚拟机、服务结构和云服务的比较](/azure/architecture/guide/technology-choices/compute-decision-tree)。

### <a name="apply-updates-to-components"></a>对组件应用更新

为了防止漏洞，应持续清点客户端和服务器端组件（例如，框架和库）及其更新依赖项。 新的漏洞和更新的软件版本会不断发布。 确保您有一个持续的计划来监视、会审，并将更新或配置更改应用于您使用的库和组件。

有关工具建议，请参阅有关[使用具有已知漏洞的组件](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities)[的打开 Web 应用程序安全项目 （OWASP）](https://www.owasp.org/index.php/Main_Page)页面。 您还可以订阅与您使用的组件相关的安全漏洞的电子邮件警报。

### <a name="use-threat-modeling-during-application-design"></a>在应用程序设计期间使用威胁建模

威胁建模是识别业务和应用程序的潜在安全威胁，然后确保适当的缓解措施到位的过程。 SDL 规定，团队应在设计阶段进行威胁建模，当解决潜在问题相对容易且经济高效时。 在设计阶段使用威胁建模可以大大降低开发总成本。

为了帮助促进威胁建模过程，我们设计了[SDL 威胁建模工具](threat-modeling-tool.md)，并考虑到了非安全专家。 此工具通过提供有关如何创建和分析威胁模型的明确指导，使所有开发人员都更容易进行威胁建模。

跨所有信任边界对应用程序设计和枚举[STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)威胁（欺骗、篡改、拒绝、信息披露、拒绝服务和特权提升）进行建模已证明是及早捕获设计错误的有效方法。 下表列出了 STRIDE 威胁，并提供了一些使用 Azure 提供的功能的示例缓解措施。 这些缓解措施并非在每种情况下都起作用。

| 威胁 | 安全属性 | 潜在的 Azure 平台缓解 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 欺骗               | 身份验证        | [需要 HTTPS 连接](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio)。 |
| 篡改              | 完整性             | 验证 SSL/TLS 证书。 使用 SSL/TLS 的应用程序必须完全验证其连接到的实体的 X.509 证书。 使用 Azure 密钥保管库证书[来管理 x509 证书](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)。 |
| 否认性            | 不可否认性       | 启用 Azure [监视和诊断](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。|
| 信息泄露 | 机密性       | 在静止和[传输](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)[中](../fundamentals/encryption-atrest.md)加密敏感数据。 |
| 拒绝服务      | 可用性          | 监视性能指标，以寻找潜在的拒绝服务条件。 实现连接筛选器。 [Azure DDoS 保护](../../virtual-network/ddos-protection-overview.md#next-steps)与应用程序设计最佳实践相结合，可防御 DDoS 攻击。|
| 特权提升 | 授权         | 使用 Azure<span class="underline"> </span>活动目录[特权标识管理](../../active-directory/privileged-identity-management/pim-configure.md)。|

### <a name="reduce-your-attack-surface"></a>减少攻击面

攻击面是可能发生潜在漏洞的总和。 在本文中，我们重点介绍了应用程序的攻击面。
重点是保护应用程序免受攻击。 最小化攻击面的一种简单和快速的方法是从应用程序中删除未使用的资源和代码。 应用程序越小，攻击面越小。 例如，删除：

- 尚未发布的功能的代码。
- 调试支持代码。
- 未使用的网络接口和协议或已弃用的网络接口和协议。
- 虚拟机和未使用的其他资源。

定期清理资源并确保删除未使用的代码是确保恶意参与者攻击的机会减少的好方法。

减少攻击面的更详细和深入的方法是完成攻击面分析。 攻击面分析可帮助您映射系统需要检查和测试安全漏洞的部分。

攻击表面分析的目的是了解应用程序中的风险区域，以便开发人员和安全专家了解应用程序的哪些部分可以攻击。 然后，您可以找到最小化此潜力的方法，跟踪攻击面的更改时间以及方式，以及从风险角度意味着什么。

攻击面分析可帮助您识别：

- 需要查看和测试安全漏洞的函数和部分。
- 需要纵深防御的高风险代码区域（需要防御的系统部分）。
- 当您更改攻击面并且需要刷新威胁评估时。

减少攻击者利用潜在弱点或漏洞的机会需要您彻底分析应用程序的整体攻击面。 它还包括禁用或限制对系统服务的访问、应用最小特权原则以及尽可能使用分层防御。

我们讨论在 SDL 的验证阶段[进行攻击表面审查](secure-develop.md#conduct-attack-surface-review)。

> [!NOTE]
> **威胁建模和攻击面分析之间的区别是什么？**
威胁建模是识别应用程序的潜在安全威胁并确保针对威胁的适当缓解措施的过程。 攻击面分析可识别可攻击的高风险代码区域。 它涉及找到保护应用程序高风险区域的方法，并在部署应用程序之前查看和测试这些代码区域。

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>采用标识用作主要安全边界的策略

在设计云应用程序时，将安全周边焦点从以网络为中心的方法扩展到以身份为中心的方法非常重要。 从历史上看，主要的本地安全边界是组织的网络。 大多数本地安全设计都使用网络作为主要安全支点。 对于云应用程序，将标识视为主要安全边界，可以更好地为您提供服务。

开发以身份为中心的方法来开发 Web 应用程序可以执行的事项：

- 为用户强制实施多重身份验证。
- 使用强身份验证和授权平台。
- 应用最低特权原则。
- 实现实时访问。

#### <a name="enforce-multi-factor-authentication-for-users"></a>为用户强制实施多重身份验证

使用双重身份验证。 双重身份验证是当前身份验证和授权的标准，因为它避免了用户名和密码类型的身份验证固有的安全漏洞。 对 Azure 管理接口（Azure 门户/远程 PowerShell）和面向客户的服务的访问应设计和配置为使用[Azure 多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)。

#### <a name="use-strong-authentication-and-authorization-platforms"></a>使用强身份验证和授权平台

使用平台提供的身份验证和授权机制，而不要使用自定义代码。 这是因为开发自定义身份验证代码容易出错。 商业代码（例如，来自 Microsoft）经常经过广泛审查，以进行安全性检查。 [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用于标识和访问管理的 Azure 解决方案。 这些 Azure AD 工具和服务有助于安全开发：

- [Azure AD 标识平台（面向开发人员的 Azure AD）](../../active-directory/develop/about-microsoft-identity-platform.md)是一种云标识服务，开发人员用于构建安全登录用户的应用。 Azure AD 可帮助构建单租户、业务线 （LOB） 应用的开发人员以及希望开发多租户应用的开发人员。 除了基本登录之外，使用 Azure AD 构建的应用还可以调用在 Azure AD 平台上构建的 Microsoft API 和自定义 API。 Azure AD 标识平台支持行业标准协议，如 OAuth 2.0 和 OpenID 连接。

- [Azure 活动目录 B2C（Azure AD B2C）](../../active-directory-b2c/index.yml)是一种标识管理服务，可用于自定义和控制客户在使用应用程序时注册、登录和管理其配置文件的方式。 这包括为 iOS、Android 和 .NET 等开发的应用程序。 Azure AD B2C 支持这些操作，同时保护客户标识。

#### <a name="apply-the-principle-of-least-privilege"></a>应用最低特权原则

[最低特权](https://en.wikipedia.org/wiki/Principle_of_least_privilege)的概念意味着为用户提供精确的访问和控制水平，他们需要做他们的工作，仅此而已。

软件开发人员是否需要域管理员权限？ 行政助理是否需要访问其个人计算机上的管理控制？ 评估对软件的访问也没有什么不同。 如果使用[基于角色的访问控制 （RBAC）](../../role-based-access-control/overview.md)为用户提供不同的应用程序中的能力和权限，则不会授予每个人对一切的访问权限。 通过限制对每个角色所需内容的访问，可以限制发生安全问题的风险。

确保应用程序在其访问模式中强制实施[最少的权限](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications)。

> [!NOTE]
>  最低特权规则需要适用于软件和创建软件的用户。 如果软件开发人员获得过多访问权限，则可能会给 IT 安全带来巨大的风险。 如果开发人员有恶意或获得过多访问权限，后果可能非常严重。 我们建议在整个开发生命周期中将最低特权规则应用于开发人员。

#### <a name="implement-just-in-time-access"></a>实现实时访问

实现*实时*（JIT） 访问以进一步降低特权的曝光时间。 使用[Azure AD 特权标识管理](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-build-visibility-and-take-full-control-of-admin-activity)：

- 向用户授予他们只需要 JIT 的权限。
- 分配时限更短的角色，确信权限会自动撤消。

### <a name="require-re-authentication-for-important-transactions"></a>要求对重要事务重新认证

[跨站点请求伪造](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1)（也称为*XSRF*或*CSRF）* 是针对 Web 托管应用的攻击，其中恶意 Web 应用影响客户端浏览器与信任该浏览器的 Web 应用之间的交互。 跨站点请求伪造攻击是可能的，因为 Web 浏览器会在每次请求发送到网站时自动发送某些类型的身份验证令牌。
这种利用形式也称为*一键式攻击*或*会话使用*，因为攻击利用了用户以前经过身份验证的会话。

抵御此类攻击的最佳方法是向用户询问只有在每次重要交易之前才能提供的东西，例如购买、帐户停用或密码更改。 您可以要求用户重新输入其密码、完成密码或提交只有用户才会拥有的秘密令牌。 最常见的方法是机密令牌。

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>使用密钥管理解决方案保护密钥、凭据和其他机密

丢失密钥和凭据是一个常见问题。 唯一比丢失密钥和凭据更遭糕的事情是让未经授权的一方获取这些密钥和凭据的访问权限。 攻击者可以利用自动化和手动技术来查找存储在 GitHub 等代码存储库中的密钥和机密。 不要在这些公共代码存储库或任何其他服务器上放置密钥和机密。

始终将密钥、证书、机密和连接字符串放在密钥管理解决方案中。 您可以使用集中式解决方案，其中密钥和机密存储在硬件安全模块 （HSM） 中。 Azure 在云中提供具有[Azure 密钥保管库](../../key-vault/general/overview.md)的 HSM。

密钥保管库是一个秘密*存储*：它是用于存储应用程序机密的集中云服务。 密钥保管库通过将应用程序机密保存在单个中心位置并提供安全访问、权限控制和访问日志记录来保护您的机密数据安全。

机密存储在单独的*保管库中*。 每个保管库都有自己的配置和安全策略来控制访问。 您可以通过 REST API 或通过适用于大多数编程语言的客户端 SDK 访问数据。

> [!IMPORTANT]
> Azure 密钥保管库旨在存储服务器应用程序的配置机密。 它不用于存储属于应用用户的数据。 这反映在其性能特征、API 和成本模型中。
>
> 用户数据应存储在其他位置，例如，在具有透明数据加密 （TDE） 的 Azure SQL 数据库实例中或使用 Azure 存储服务加密的存储帐户中。 应用程序用于访问这些数据存储的机密可以保存在 Azure 密钥保管库中。

### <a name="protect-sensitive-data"></a>保护敏感数据

保护数据是安全策略的重要组成部分。
对数据进行分类并确定数据保护需求有助于在设计应用时牢记数据安全性。 按敏感性和业务影响对存储的数据进行分类（分类）可帮助开发人员确定与数据相关的风险。

在设计数据格式时，将所有适用的数据标记为敏感。 确保应用程序将适用的数据视为敏感数据。 这些实践可以帮助您保护您的敏感数据：

- 使用加密。
- 避免对密钥和密码等机密进行硬编码。
- 确保访问控制和审核到位。

#### <a name="use-encryption"></a>使用加密

保护数据应该是安全策略的重要组成部分。
如果数据存储在数据库中，或者数据在位置之间来回移动，请使用[静态数据](../fundamentals/encryption-atrest.md)加密（在数据库中）和[传输中的数据](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)加密（在往返用户、数据库、API 或服务终结点的途中）。 我们建议您始终使用 SSL/TLS 协议来交换数据。 确保使用最新版本的 TLS 进行加密（目前，这是版本 1.2）。

#### <a name="avoid-hard-coding"></a>避免硬编码

有些东西不应该在你的软件中硬编码。 一些示例包括主机名或 IP 地址、URL、电子邮件地址、用户名、密码、存储帐户密钥和其他加密密钥。 请考虑围绕代码中可以硬编码或不能硬编码的内容（包括在代码的注释部分）实现要求。

在代码中放置注释时，请确保不保存任何敏感信息。 这包括您的电子邮件地址、密码、连接字符串、仅组织中的人知道的应用程序信息，以及可能让攻击者在攻击应用程序或组织时具有优势的任何其他信息。

基本上，假设开发项目中的所有内容在部署时都是公共知识。 避免在项目中包含任何类型的敏感数据。

早些时候，我们讨论了[Azure 密钥保管库](../../key-vault/general/overview.md)。 您可以使用密钥保管库存储密钥和密码等机密，而不是对其进行硬编码。 将密钥保管库与 Azure 资源的托管标识结合使用时，Azure Web 应用可以轻松安全地访问机密配置值，而无需在源代码管理或配置中存储任何机密。 要了解更多信息，请参阅[使用 Azure 密钥保管库 管理服务器应用中的机密](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)。

### <a name="implement-fail-safe-measures"></a>实施故障安全措施

应用程序必须能够以一致的方式处理执行过程中发生的[错误](https://docs.microsoft.com/dotnet/standard/exceptions/)。 应用程序应捕获所有错误，并不安全或关闭失败。

您还应确保使用足够的用户上下文记录错误，以识别可疑或恶意活动。 日志应保留足够的时间，以便延迟进行取证分析。 日志的格式应易于由日志管理解决方案使用。 确保触发与安全相关的错误的警报。 日志记录和监视不足使攻击者可以进一步攻击系统并保持持久性。

### <a name="take-advantage-of-error-and-exception-handling"></a>利用错误和异常处理

实现正确的错误和[异常处理](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions)是防御性编码的重要组成部分。 错误和异常处理对于使系统可靠和安全至关重要。 错误处理中的错误可能导致不同类型的安全漏洞，例如向攻击者泄露信息并帮助攻击者了解有关您的平台和设计的更多信息。

请确保：

- 以集中方式处理异常，以避免代码中重复[的尝试/捕获块](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions)。

- 所有意外行为都在应用程序内处理。

- 向用户显示的消息不会泄露关键数据，但确实提供了足够的信息来解释问题。

- 记录异常，并提供足够的信息供取证或事件响应团队进行调查。

[Azure 逻辑应用](../../logic-apps/logic-apps-overview.md)为处理由从属系统引起的[错误和异常](../../logic-apps/logic-apps-exception-handling.md)提供了一流的体验。 您可以使用逻辑应用创建工作流，以自动执行跨企业和组织集成应用、数据、系统和服务的任务和流程。

### <a name="use-logging-and-alerting"></a>使用日志记录和警报

[记录](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)您的安全问题以进行安全调查，并触发有关问题的警报，以确保人们及时了解问题。 在所有组件中启用审核与日志记录。 审核日志应捕获用户上下文并标识所有重要事件。

检查用户是否未记录用户提交到您的网站的任何敏感数据。 敏感数据的示例包括：

- 用户凭据
- 社会安全号码或其他识别信息
- 信用卡号或其他财务信息
- 运行状况信息
- 可用于解密加密信息的私钥或其他数据
- 可以用来增强应用程序攻击效果的系统信息或应用程序信息

确保应用程序监视用户管理事件，如成功和失败的用户登录、密码重置、密码更改、帐户锁定和用户注册。 记录这些事件可帮助您检测和响应潜在的可疑行为。 它还允许您收集操作数据，例如访问应用程序的人员。

## <a name="next-steps"></a>后续步骤
在以下文章中，我们建议安全控制和活动，以帮助您开发和部署安全应用程序。

- [开发安全应用程序](secure-develop.md)
- [部署安全应用程序](secure-deploy.md)
