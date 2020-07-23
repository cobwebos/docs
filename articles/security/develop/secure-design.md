---
title: 在 Microsoft Azure 上设计安全的应用程序
description: 本文讨论了在 Web 应用程序项目的要求和设计阶段需要考虑的最佳做法。
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
ms.openlocfilehash: d0cffbd1fa09abef9853e0ef853696c3c8ed353c
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246803"
---
# <a name="design-secure-applications-on-azure"></a>在 Azure 上设计安全的应用程序
本文介绍了在为云设计应用程序时需要考虑的安全活动和控制措施。 介绍了在 Microsoft [安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 的要求和设计阶段需要考虑的培训资源以及安全问题和概念。 目标是帮助你定义可用于设计更安全应用程序的活动和 Azure 服务。

本文介绍了以下 SDL 阶段：

- 培训
- 要求
- 设计

## <a name="training"></a>培训
在开始开发云应用程序之前，请花些时间了解 Azure 上的安全和隐私。 通过执行此步骤，可以降低应用程序中可被利用的漏洞的数量和严重性。 你将能够更好地正确应对不断变化的威胁态势。

在培训阶段使用以下资源自行熟悉可供开发人员使用的 Azure 服务以及 Azure 上的安全最佳做法：

  - [Azure 开发人员指南](https://azure.microsoft.com/campaigns/developer-guide/)介绍了如何开始 azure。 本指南展示了你可使用哪些服务来运行应用程序、存储你的数据、引入智能、构建 IoT 应用，以及以更高效、更安全的方式部署解决方案。

  - [Azure 开发人员入门指南](../../guides/developer/azure-developer-guide.md)为希望使用 Azure 平台进行开发的开发人员提供了基本信息。

  - [SDK 和工具](https://docs.microsoft.com/azure/index?pivot=sdkstools)介绍了 Azure 上提供的工具。

  - [Azure DevOps Services](https://docs.microsoft.com/azure/devops/)提供开发协作工具。 这些工具包括高性能管道、免费的 Git 存储库、可配置的看板以及广泛的自动化和基于云的负载测试。
    [DevOps 资源中心](https://docs.microsoft.com/azure/devops/learn/)结合了我们的资源，以实现学习 DevOps 实践、Git 版本控制、敏捷方法、我们如何在 Microsoft 中使用 DevOps，以及如何评估你自己的 DevOps 进度。

  - [在推送到生产环境前要考虑的前 5 个安全事项](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca)介绍了如何在 Azure 上帮助保护你的 Web 应用程序，并保护你的应用免受最常见和最危险的 Web 应用程序攻击。

  - [适用于 Azure 的 Secure DevOps 工具包](https://azsk.azurewebsites.net/index.html)是一个脚本、工具、扩展和自动化的集合，该集合适用于使用广泛的自动化的 DevOps 团队的综合性 Azure 订阅和资源安全需求。 适用于 Azure 的 Secure DevOps 工具包可以向你展示如何将安全性平稳集成到你的本机 DevOps 工作流中。 该工具包处理 (SVTs) 等安全验证测试工具，可帮助开发人员编写安全代码，并在编码和早期开发阶段测试云应用程序的安全配置。

  - [Azure 解决方案的最佳安全方案](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions)提供一系列安全最佳做法，以便在使用 Azure 设计、部署和管理云解决方案时使用。

## <a name="requirements"></a>要求
要求定义阶段是一个关键步骤，它定义你的应用程序是什么，以及它发布后可用来做什么。 在需求阶段，还要考虑将在你的应用程序中构建的安全控制措施。 在此阶段，你还将开始执行在整个 SDL 中都将采取的步骤，以确保发布并部署安全的应用程序。

### <a name="consider-security-and-privacy-issues"></a>考虑安全和隐私问题
此阶段是考虑基础的安全和隐私问题的最佳时间。 在项目开始时定义可接受的安全和隐私级别有助于团队：

- 了解与安全问题相关的风险。
- 在开发过程中确定和修复安全 bug。
- 在整个项目中应用确定的安全和隐私级别。

在编写对应用程序的要求时，请确保考虑有助于保护应用程序和数据安全的安全控制措施。

### <a name="ask-security-questions"></a>询问安全问题
询问安全问题，例如：

  - 我的应用程序是否包含敏感数据？

  - 我的应用程序是收集还是存储需要遵守行业标准和合规性计划的数据（如[联邦金融机构检查委员会） (FFIEC) ](../blueprints/ffiec-analytics-overview.md)还是[支付卡行业数据安全标准 (PCI DSS) ](../blueprints/pcidss-analytics-overview.md)？

  - 我的应用程序是否收集或包含敏感的个人或客户数据，这些数据是否可以用来（单独使用或与其他信息一起使用）识别、联系或定位个人？

  - 我的应用程序是否收集或包含可用于访问个人医疗、教育、财务或就业信息的数据？ 在要求阶段确定数据的敏感性有助于对数据进行分类，并确定将用于应用程序的数据保护方法。

  - 我的数据存储在哪里？如何存储？ 考虑如何监视应用程序使用的存储服务是否存在任何意外变化（例如响应时间变长）。 你是否能够影响日志记录来收集更详细的数据并深入地分析问题？

  - 我的应用程序是可供公众使用（在 Internet 上）还是仅供内部使用？ 如果你的应用程序可供公众使用，你如何保护可能会被收集的数据，避免其被不当使用？ 如果你的应用程序仅供内部使用，请考虑组织中谁应该有权访问该应用程序以及他们应该有权访问多长时间。

  - 在开始设计应用程序之前，你是否了解你的标识模型？ 你将如何确定用户的身份如其所言以及用户被授权执行什么操作？

  - 我的应用程序是否执行敏感或重要的任务（例如转账、打开门锁或送药）？
    考虑如何验证执行敏感任务的用户是否被授权执行该任务，以及如何验证用户的身份如其所言。 授权 (AuthZ) 是指准许经过身份验证的安全主体执行某项操作的措施。 身份验证 (AuthN) 是要求参与方提供合法凭据的措施。

  - 我的应用程序是否执行任何有风险的软件活动，例如允许用户上传或下载文件或其他数据？ 如果你的应用程序确实执行有风险的活动，请考虑如何让应用程序保护用户，防止用户处理恶意文件或数据。

### <a name="review-owasp-top-10"></a>查看 OWASP 前 10 大安全风险
考虑查看 [<span class="underline">OWASP 前 10 大应用程序安全风险</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)。
“OWASP 前 10 大安全风险”介绍了 Web 应用程序的重大安全风险。
了解这些安全风险可以帮助你制定要求和设计决策，从而将应用程序中的这些风险降到最低。

考虑采取安全控制措施来防止违规，这很重要。
但是，您还需要[假设发生了破坏](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops)。 假设会存在违规，这有助于你提前应对一些重要的安全问题，不致于仓促上阵：

  - 我将如何检测攻击？

  - 如果存在攻击或违规，我该怎么办？

  - 在遭受攻击（例如数据泄露或篡改）后我将如何进行恢复？

## <a name="design"></a>设计

设计阶段对于制定设计和功能规范的最佳做法至关重要。 它对于执行风险分析也至关重要，风险分析有助于减少整个项目中的安全和隐私问题。

如果你有安全要求并使用安全设计概念，则可以避免安全缺陷或尽量减少其出现。 安全缺陷是应用程序设计中的疏漏。在应用程序发布后，用户可能会利用它来执行恶意的或意外的操作。

在设计阶段，还要考虑如何在多个层次中应用安全措施；一层防御不一定够用。 如果攻击者穿过了你的 Web 应用程序防火墙 (WAF)，将会发生什么情况？ 你需要另一种安全控制措施来防御该攻击。

考虑到这一点，我们将讨论以下安全设计概念，以及在设计安全的应用程序时应解决的安全控制问题：

- 使用安全的编码库和软件框架。
- 扫描易受攻击的组件。
- 在应用程序设计期间使用威胁建模。
- 减少受攻击面。
- 采用以标识作为主要安全外围的策略。
- 要求对重要事务反复进行身份验证。
- 使用密钥管理解决方案来保护密钥、凭据和其他机密。
- 保护敏感数据。
- 实施故障保护措施。
- 利用错误和异常处理。
- 使用日志记录和警报。

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>使用安全的编码库和软件框架

对于开发，请使用安全的编码库和具有内嵌安全性的软件框架。 开发人员可以使用现有的、经过证明的功能（加密、输入清理、输出编码、密钥或连接字符串以及任何其他会被视为安全控制措施的项目），不需从头开发安全控制措施。 这有助于避免与安全相关的设计和实现缺陷。

请确保使用框架及其提供的所有安全功能的最新版本。 Microsoft 为所有开发人员提供了一[套全面的开发工具](https://azure.microsoft.com/product-categories/developer-tools/)，可使用任何平台或语言提供云应用程序。 你可以从各种 [SDK](https://azure.microsoft.com/downloads/) 中进行选择，以便使用所选语言进行编码。
你可以利用功能齐全的集成开发环境 (IDE) 和具有高级调试功能和内置 Azure 支持的编辑器。

Microsoft 提供了各种[语言、框架和工具](https://docs.microsoft.com/azure/index?pivot=sdkstools&panel=sdkstools-all)，你可以使用它们在 Azure 上开发应用程序。 一个示例是[面向 .NET 和 .NET Core 开发人员的 Azure](https://docs.microsoft.com/dotnet/azure/)。 对于我们提供的每种语言和框架，你可以通过快速入门、教程和 API 参考来快速入门。

Azure 提供了各种可用来托管网站和 Web 应用程序的服务。 这些服务允许你使用自己喜欢的语言进行开发，无论该语言是 .NET、NET Core、Java、Ruby、Node.js、PHP 还是 Python。
[Azure 应用服务 Web 应用](../../app-service/overview.md)（Web 应用）是其中的服务之一。

Web 应用将 Microsoft Azure 的功能添加到了应用程序中。 它包括安全性、负载均衡、自动缩放和自动化管理。 你还可以利用 Web 应用中的 DevOps 功能，例如包管理、过渡环境、自定义域、SSL/TLS 证书，以及从 Azure DevOps、GitHub、Docker 中心和其他源进行的持续部署。

Azure 提供了可用来托管网站和 Web 应用程序的其他服务。 大多数情况下，Web 应用是最佳选择。 对于微服务体系结构，请考虑使用 [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。
如果需要更好地控制运行代码的 VM，请考虑使用 [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)。
有关如何在这些 Azure 服务之间进行选择的详细信息，请参阅[Azure App Service、虚拟机、Service Fabric 和云服务的比较](/azure/architecture/guide/technology-choices/compute-decision-tree)。

### <a name="apply-updates-to-components"></a>为组件应用更新

为了防止出现漏洞，应持续清点你的客户端和服务器端组件（例如，框架和库）及其依赖项，看是否存在更新。 随着新漏洞的出现，我们会不断发布更新的软件版本。 请务必制定一个日常计划，以便监视、会审和应用针对你所使用的库和组件进行的更新或配置更改。

请参阅[开放 Web 应用程序安全项目 (OWASP)](https://www.owasp.org/index.php/Main_Page) 页面上的[使用具有已知漏洞的组件](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities)，以便获取工具建议。 你还可以订阅电子邮件警报，以便了解与所用组件有关的安全漏洞。

### <a name="use-threat-modeling-during-application-design"></a>在应用程序设计期间使用威胁建模

威胁建模是一个过程，它需要先识别对业务和应用程序的潜在安全威胁，然后确保实施适当的缓解措施。 SDL 规定，团队应在设计阶段进行威胁建模，在此阶段解决潜在问题相对简单且经济高效。 在设计阶段使用威胁建模可以极大地降低总体开发成本。

考虑到非安全专家的情况，我们设计了 [SDL 威胁建模工具](threat-modeling-tool.md)来简化威胁建模过程。 此工具提供有关如何创建和分析威胁模型的明确指导，使所有开发人员更容易进行威胁建模。

在所有信任边界之间对应用程序设计和枚举[STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)威胁进行建模，从而在所有信任边界中进行了一种有效的方法来捕获设计错误。 下表列出了 STRIDE 威胁，并给出了一些使用 Azure 提供的功能的示例缓解措施。 这些缓解措施并非在每种情况下都起作用。

| 威胁 | 安全属性 | 潜在的 Azure 平台缓解措施 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 欺骗               | 身份验证        | [要求使用 HTTPS 连接](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio)。 |
| 篡改              | 完整性             | 验证 SSL/TLS 证书。 使用 SSL/TLS 的应用程序必须全面验证它们连接到的实体的 X.509 证书。 使用 Azure Key Vault 证书来[管理 x509 证书](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-certificates)。 |
| 否认性            | 不可否认性       | 启用 Azure [监视和诊断](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。|
| 信息泄露 | 机密性       | 加密[静态](../fundamentals/encryption-atrest.md)和[传输中的](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)敏感数据。 |
| 拒绝服务      | 可用性          | 监视性能指标，以了解潜在的拒绝服务情况。 实现连接筛选器。 [Azure DDoS 保护](../../virtual-network/ddos-protection-overview.md#next-steps)与应用程序设计最佳做法相结合，提供对 DDoS 攻击的防御。|
| 权限提升 | 授权         | 使用 Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)。|

### <a name="reduce-your-attack-surface"></a>减少受攻击面

受攻击面是指可能出现潜在漏洞的位置的总数。 在本文中，我们重点介绍应用程序的受攻击面。
重点是保护应用程序，使其免受攻击。 从应用程序中删除未使用的资源和代码是最小化受攻击面的一种简单而快速的方法。 应用程序越小，受攻击面就越小。 例如，删除以下项：

- 尚未发布的功能的代码。
- 调试支持代码。
- 未使用或已弃用的网络接口和协议。
- 未使用的虚拟机和其他资源。

定期清理资源并确保删除未使用的代码是很好的方法，可以确保减少恶意行动者发起攻击的机会。

减少受攻击面的更详细和深入的方法来是完成受攻击面分析。 受攻击面分析可帮助你映射需要检查和测试安全漏洞的系统部件。

受攻击面分析的目的是了解应用程序中的风险区域，以便开发人员和安全专家了解应用程序的哪些部分可能会受到攻击。 然后，你可以找到方法来尽可能降低这种可能性，跟踪受攻击面何时变化以及如何变化，并从风险角度来解读这意味着什么。

受攻击面分析可帮助你确定：

- 需要检查和测试安全漏洞的系统功能和部件。
- 需要纵深防御保护的高风险代码区域（需要防御的系统部件）。
- 你何时改变了受攻击面并需要刷新威胁评估。

若要减少攻击者利用潜在弱点或漏洞的机会，需要彻底分析应用程序的整体受攻击面。 它还包括禁止或限制对系统服务的访问、应用最小特权原则以及尽可能采用分层防御。

我们将讨论在 SDL 的验证阶段[执行受攻击面审查](secure-develop.md#conduct-attack-surface-review)。

> [!NOTE]
> **威胁建模与受攻击面分析有何区别？**
威胁建模是一个过程，它需要识别应用程序的潜在安全威胁，并确保实施适当的威胁缓解措施。 受攻击面分析识别代码中易受攻击的高风险区域。 它包括找到保护应用程序高风险区域的方法，以及在部署应用程序之前检查和测试这些代码区域。

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>采用标识用作主要安全边界的策略

设计云应用程序时，从以网络为中心的方式到以以身份为中心的方法，以以网络为中心的方式重点，这一点很重要。 过去，主要的本地安全外围是组织的网络。 大多数本地安全设计使用网络作为主要的安全枢纽。 对于云应用程序，可将标识视为主要安全外围，从而改善安全性。

若要制定以标识为中心的方法来开发 Web 应用程序，你可以执行的事项如下：

- 对用户实施多重身份验证。
- 使用强身份验证和授权平台。
- 应用最低权限原则。
- 实施实时访问。

#### <a name="enforce-multi-factor-authentication-for-users"></a>对用户实施多重身份验证

使用双重身份验证。 双重身份验证是最新的身份验证和授权标准，因为它避免了用户名与密码类型的身份验证所固有的安全漏洞。 需要访问 Azure 管理界面（Azure 门户/远程 PowerShell）和面向客户的服务的应用程序应设计并配置为使用 [Azure 多重身份验证](../../active-directory/authentication/concept-mfa-howitworks.md)。

#### <a name="use-strong-authentication-and-authorization-platforms"></a>使用强身份验证和授权平台

使用平台提供的身份验证和授权机制，而不要使用自定义代码。 这是因为开发自定义身份验证代码可能容易出错。 商业代码（例如 Microsoft 编写的代码）通常会接受广泛的安全性评审。 [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用于标识和访问管理的 Azure 解决方案。 这些 Azure AD 工具和服务可帮助进行安全的开发：

- [Microsoft 标识平台](/azure/active-directory/develop/)是一组组件，开发人员可以使用这些组件来构建安全登录用户的应用程序。 该平台可帮助开发人员构建单租户、业务线 (LOB) 应用和开发多租户应用的开发人员。 除了基本登录，使用 Microsoft 标识平台构建的应用还可以调用 Microsoft Api 和自定义 Api。 Microsoft 标识平台支持 OAuth 2.0 和 OpenID Connect 这样的行业标准协议。

- [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) 是一项标识管理服务，可用来自定义和控制客户在使用应用程序时的注册、登录和管理配置文件的方式。 这包括面向 iOS、Android 和 .NET 等系统开发的应用程序。 使用 Azure AD B2C，可以在执行这些操作的同时保护客户标识。

#### <a name="apply-the-principle-of-least-privilege"></a>应用最低权限原则

[最小特权](https://en.wikipedia.org/wiki/Principle_of_least_privilege)的概念意味着向用户提供完成其工作所需的准确级别的访问和控制。

软件开发人员是否需要域管理员权限？ 管理助理是否需要访问其个人电脑上的管理控制措施？ 评估对软件的访问没有什么不同。 如果在应用程序中使用[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 为用户提供不同的能力和授权，则不会让每个人都可以访问所有内容。 通过将访问权限限定为每个角色必需的权限，可以限制出现安全问题的风险。

确保你的应用程序在其整个访问模式中强制实施[最低权限](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications)。

> [!NOTE]
> 最低权限规则需要应用于软件和创建软件的人员。 如果为软件开发人员提供过多的访问权限，他们可能会给 IT 安全带来巨大的风险。 如果开发人员有恶意或被授予了过多的访问权限，后果可能很严重。 建议在整个开发生命周期中对开发人员应用最低权限规则。

#### <a name="implement-just-in-time-access"></a>实现实时访问

实现*实时* (JIT) 访问，进一步降低权限的暴露时间。 使用[Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#stage-3-take-control-of-admin-activity)执行以下操作：

- 向用户授予仅 JIT 所需的权限。
- 分配时限更短的角色，确信权限会自动撤消。

### <a name="require-re-authentication-for-important-transactions"></a>要求对重要事务反复进行身份验证

[跨站点伪造请求](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1)（也称为 XSRF 或 CSRF ）是一种针对 Web 托管型应用的攻击。在此类攻击中，恶意 Web 应用会影响客户端浏览器与信任该浏览器的 Web 应用之间的交互。 可能出现跨站点伪造请求攻击是因为 Web 浏览器会随每个请求自动向网站发送某些类型的身份验证令牌。
这种形式的攻击也称为一键攻击或会话叠置，因为攻击利用了用户先前经过身份验证的会话。

防御此类攻击的最佳方法是在每次执行重要事务（例如购买、帐户停用或密码更改）之前都向用户请求只有该用户才能提供的内容。 你可以要求用户重新输入密码、完成验证码，或者提交只有该用户才会有的机密令牌。 最常用的方法是机密令牌。

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>使用密钥管理解决方案来保护密钥、凭据和其他机密

丢失密钥和凭据是一个常见问题。 唯一比丢失密钥和凭据更遭糕的事情是让未经授权的一方获取这些密钥和凭据的访问权限。 攻击者可以利用自动和手动技术来查找存储在代码存储库（例如 GitHub）中的密钥和机密。 请勿在这些公用代码存储库中或任何其他服务器上放置密钥和机密。

请始终将密钥、证书、机密和连接字符串放置在密钥管理解决方案中。 可以使用集中式解决方案，将密钥和机密存储在硬件安全模块 (HSM) 中。 Azure 在云中提供了包含 [Azure Key Vault](../../key-vault/general/overview.md) 的 HSM。

Key Vault 是一个机密存储：一个用于存储应用程序机密的集中式云服务。 Key Vault 可以保护机密数据，其方法是：将应用程序机密保存在单个集中位置，并提供安全的访问、权限控制和访问日志记录。

机密存储在各个保管库中。 每个保管库都使用其自己的配置和安全策略来控制访问。 你可以通过 REST API 或通过可用于大多数编程语言的客户端 SDK 来访问数据。

> [!IMPORTANT]
> Azure Key Vault 设计用来为服务器应用程序存储配置机密。 它不用于存储属于应用用户的数据。 这反映在其性能特征、API 和成本模型中。
>
> 用户数据应存储在其他位置，例如具有透明数据加密 (TDE) 的 Azure SQL 数据库实例或使用 Azure 存储服务加密的存储帐户。 应用程序用来访问这些数据存储的机密可以保留在 Azure Key Vault 中。

### <a name="protect-sensitive-data"></a>保护敏感数据

保护数据是安全策略的重要组成部分。
对数据进行分类并确定数据保护需求有助于你在设计应用时始终考虑数据安全。 按敏感度和业务影响对存储的数据进行分类有助于开发人员确定与数据相关的风险。

在设计数据格式时，请将所有适用的数据标记为敏感数据。 确保应用程序将适用的数据视为敏感数据。 这些做法有助于保护敏感数据：

- 使用加密。
- 避免对机密（例如密钥和密码）进行硬编码。
- 确保实施访问控制和审核。

#### <a name="use-encryption"></a>使用加密

保护数据应当是安全策略的重要组成部分。
如果数据存储在数据库中，或者在不同位置之间来回移动，请对[静态数据](../fundamentals/encryption-atrest.md)（在数据库中）进行加密，并对[传输中的数据](../fundamentals/data-encryption-best-practices.md#protect-data-in-transit)（在往返于用户、数据库、API 或服务终结点的路上）进行加密。 建议始终使用 SSL/TLS 协议来交换数据。 确保使用最新版本的 TLS 进行加密（目前为版本 1.2）。

#### <a name="avoid-hard-coding"></a>避免硬编码

某些东西绝不应当硬编码到软件中。 例如，主机名或 IP 地址、URL、电子邮件地址、用户名、密码、存储帐户密钥和其他加密密钥。 请考虑实施相关要求来规定哪些东西可以硬编码到代码中（包括代码的注释部分），哪些东西不能硬编码。

在代码中放置注释时，请确保不要保存任何敏感信息。 这包括你的电子邮件地址、密码、连接字符串；只有组织中的某个人才能知道的有关你的应用程序的信息；以及攻击者可能会用来攻击你的应用程序或组织的任何其他信息。

大致假设开发项目中的所有内容在部署时都将是公开知识。 避免在项目中包括任何类型的敏感数据。

前面，我们讨论了 [Azure Key Vault](../../key-vault/general/overview.md)。 你可以使用 Key Vault 来存储密钥和密码等机密，而不是对它们进行硬编码。 将 Key Vault 与 Azure 资源的托管标识结合使用时，Azure Web 应用可轻松安全地访问机密配置值，无需在源代码管理或配置中存储任何机密。 若要了解更多信息，请参阅[使用 Azure Key Vault 管理服务器应用中的机密](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)。

### <a name="implement-fail-safe-measures"></a>实施故障保护措施

应用程序必须能够以一致的方式处理执行过程中出现的[错误](https://docs.microsoft.com/dotnet/standard/exceptions/)。 应用程序应该捕获所有错误，实施故障保护或故障关闭措施。

你还应当确保为错误记录足够的用户上下文，以便识别可疑的或恶意的活动。 日志应当保留足够长的时间，以便延后进行法证分析。 日志的格式应该可由日志管理解决方案轻松使用。 如果出现与安全相关的错误，请确保触发警报。 如果日志记录和监视措施不足，攻击者就会进一步攻击系统并将其持久化。

### <a name="take-advantage-of-error-and-exception-handling"></a>利用错误和异常处理

进行正确的错误和[异常处理](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions)是防御性编码的重要组成部分。 错误和异常处理对于确保系统的可靠性和安全性至关重要。 错误处理中的失误可能会导致各种类型的安全漏洞，例如向攻击者泄漏信息，以及帮助攻击者了解你的平台和设计的详细信息。

请确保：

- 集中处理异常，避免在代码中使用重复的 [try/catch 块](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions)。

- 所有的意外行为都在应用程序中进行处理。

- 向用户显示的消息不会泄露重要数据，但会提供足够的信息来解释问题。

- 记录异常，并为法证或事件响应团队提供足够的信息进行调查。

[Azure 逻辑应用](../../logic-apps/logic-apps-overview.md)提供了一流的体验来[处理由相关系统导致的错误和异常](../../logic-apps/logic-apps-exception-handling.md)。 你可以使用逻辑应用来创建工作流，以便自动完成用于跨企业和组织集成应用、数据、系统和服务的任务和流程。

### <a name="use-logging-and-alerting"></a>使用日志记录和警报

[记录](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)你的安全问题以便进行安全调查，并触发相关问题的警报，以确保人们及时了解问题。 在所有组件中启用审核与日志记录。 审核日志应该捕获用户上下文并标识所有重要事件。

确保不会记录用户提交到站点的任何敏感数据。 敏感数据的示例包括：

- 用户凭据
- 身份证号或其他身份信息
- 信用卡号或其他财务信息
- 运行状况信息
- 私钥，或者其他可用于解密已加密信息的数据
- 可以用来增强应用程序攻击效果的系统信息或应用程序信息

确保应用程序监视用户管理事件，例如用户登录成功或失败、密码重置、密码更改、帐户锁定和用户注册。 这些事件的日志记录可帮助检测潜在的可疑行为并对其做出反应。 此外，还可通过它收集操作数据；例如谁正在访问应用程序。

## <a name="next-steps"></a>后续步骤
下面的文章中推荐了一些安全控制措施和活动，可帮助你开发和部署安全的应用程序。

- [开发安全的应用程序](secure-develop.md)
- [部署安全的应用程序](secure-deploy.md)
