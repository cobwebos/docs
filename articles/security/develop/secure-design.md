---
title: Microsoft Azure 上设计安全的应用程序
description: 本文介绍在你的 web 应用程序项目的要求和设计阶段期间考虑的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: b637f547de06d9347a1c74ad7da560be97f16881
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144433"
---
# <a name="design-secure-applications-on-azure"></a>在 Azure 上设计安全的应用程序
在本文中，我们介绍的安全活动和设计的云应用程序时要考虑的控件。 培训资源以及安全问题和概念，可在要求考虑和设计阶段的 Microsoft[安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)介绍。 旨在帮助您定义活动和可用于设计更安全的应用程序的 Azure 服务。

本文介绍以下 SDL 几个阶段：

- 培训
- 要求
- 设计

## <a name="training"></a>培训
在开始开发云应用程序之前，花时间来了解安全和 Azure 上的隐私。 通过执行此步骤中，可以在应用程序中减少数量及严重程度的可利用漏洞。 您可以更多好，以对不断变化的威胁环境的相应地做出反应。

在培训阶段使用以下资源可供开发人员的 Azure 服务和 Azure 上的安全最佳实践了解了相关：

  - [到 Azure 的开发人员指南](https://azure.microsoft.com/campaigns/developer-guide/)演示如何开始使用 Azure。 本指南介绍了哪些服务可用来运行你的应用程序、 将数据存储、 合并智能、 构建 IoT 应用程序和更高效且安全的方式部署你的解决方案。

  - [Azure 开发人员入门的指南](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide)提供了面向开发人员想要开始使用 Azure 平台，为其开发需求的基本信息。

  - [Sdk 和工具](https://docs.microsoft.com/azure/index#pivot=sdkstools)介绍了 Azure 提供的工具。

  - [Azure DevOps 服务](https://docs.microsoft.com/azure/devops/?view=vsts#pivot=index&panel=indexA)提供协作工具的开发。 这些工具包括高性能管道、 免费 Git 存储库、 可配置看板和广泛的自动化和基于云的负载测试。
    [DevOps 资源中心](https://docs.microsoft.com/azure/devops/learn/)结合了我们了解 DevOps 做法、 Git 版本控制、 敏捷方法、 如何使用 microsoft 的 DevOps 和如何可以评估你自己的 DevOps 进度的资源。

  - [前 5 个安全项目推送到生产环境之前，请考虑](https://docs.microsoft.com/learn/modules/top-5-security-items-to-consider/index?WT.mc_id=Learn-Blog-tajanca)演示如何帮助保护 Azure 上的 web 应用程序和保护您的应用程序针对最常见和最危险的 web 应用程序攻击。

  - [安全 DevOps 套件，适用于 Azure](https://azsk.azurewebsites.net/index.html)是适用于全面的 Azure 订阅和资源安全需要的 DevOps 团队使用全面的自动化的脚本、 工具、 扩展和自动化的集合。 Azure 的安全 DevOps 套件可以显示如何顺利地将安全集成到本机的 DevOps 工作流。 该工具包解决工具等安全验证测试 (SVTs)，可帮助开发人员编写安全代码和在编码和早期开发阶段中测试其云应用程序的安全配置。

  - [Azure 解决方案的最佳安全方案](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions)提供了一系列安全使用，因为设计、 部署和管理云解决方案通过使用 Azure 的最佳做法。

## <a name="requirements"></a>要求
要求定义阶段是定义什么是应用程序和它执行的操作时释放该地址中的一个重要步骤。 在需求阶段也是时间来考虑安全控制，您将生成到应用程序。 在此阶段，也开始将整个 SDL 中执行，以确保发布和部署安全的应用程序的步骤。

### <a name="consider-security-and-privacy-issues"></a>请考虑安全和隐私问题
此阶段是考虑项基本的安全和隐私问题的最佳时间。 定义可接受级别的安全和隐私的项目开始时可帮助团队：

- 了解安全问题与相关联的风险。
- 确定并修复安全错误，在开发过程。
- 将已建立的安全和隐私整个项目级别应用。

编写时要求为应用程序，请务必考虑安全控制，可以帮助确保您的应用程序和数据安全。

### <a name="ask-security-questions"></a>安全提问
要求安全问题，例如：

  - 我的应用程序是否包含敏感数据？

  - 不会收集我的应用程序或存储数据，我要遵守行业标准和法规遵从性计划等需要[联邦金融机构委员会 (FFIEC)](https://docs.microsoft.com/azure/security/blueprints/ffiec-analytics-overview)或[支付卡行业数据安全标准 (PCI DSS)](https://docs.microsoft.com/azure/security/blueprints/pcidss-analytics-overview)？

  - 不会收集我的应用程序或包含敏感的个人或客户数据可用，自行或与其他信息来标识，请联系，或找到一个人吗？

  - 我的应用程序收集或包含可用于访问个人的医疗、 教育、 金融、 数据或雇用信息？ 在需求阶段确定数据的敏感性，可帮助你对数据进行分类和标识将用于您的应用程序的数据保护方法。

  - 我的数据存储在何处以及如何？ 请考虑如何将监视应用程序使用的任何意外的更改 （如响应时间变长） 的存储服务。 你将能够影响日志记录收集更详细的数据和分析深度中的问题？

  - 我的应用程序是否将使用 （在 internet 上) 公开提供的或在内部仅？ 如果应用程序是公开提供的如何保护可能会以错误方式使用收集的数据？ 如果你的应用程序仅提供在内部，请考虑你的组织中的哪些人应该有权访问应用程序和多长时间，它们应具有访问权限。

  - 在开始设计应用程序之前，您是否了解标识模型？ 您如何将确定用户是他们所说的人和哪些用户有权执行操作？

  - 我的应用程序是否执行敏感或重要任务 （如进行资金划转、 解锁门，或提供事故）？
    请考虑如何将验证执行敏感任务的用户有权执行的任务并将身份验证的方式的人就是他们所说的人。 授权 （授权） 是授予经过身份验证的安全主体权限来执行某些操作的操作。 身份验证 （身份验证） 是具有挑战性合法凭据的参与方的操作。

  - 我的应用程序是否执行任何有风险的软件活动，如允许用户上传或下载文件或其他数据？ 如果你的应用程序会执行有风险的活动，请考虑你的应用程序如何将保护用户免受恶意文件或数据处理。

### <a name="review-owasp-top-10"></a>查看 OWASP 前 10 个
请考虑查看[ <span class="underline">OWASP Top 10 应用程序安全风险</span>](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)。
OWASP 前 10 个地址的 web 应用程序的关键安全风险。
了解这些安全风险可以帮助你做出最大程度减少在应用程序中的这些风险的要求和设计决策。

考虑安全控制来防止违反很重要。
但是，您还希望[假设性违规](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/security-in-devops)会发生。 假设出现违规问题可帮助回答有关安全性的一些重要问题，请提前使他们无需在紧急情况下响应：

  - 如何将检测攻击？

  - 如果出现攻击或违反，我将做什么？

  - 我要如何从与数据泄露或篡改攻击中恢复？

## <a name="design"></a>设计

在设计阶段建立设计和功能规范的最佳做法至关重要。 它也是执行风险分析，可帮助减少在整个项目的安全和隐私问题的关键。

当有安全要求，并且使用安全设计概念时，可以避免或最大程度减少安全漏洞的机会。 安全漏洞是疏忽可能允许用户以发布你的应用程序之后执行恶意或意外操作的应用程序的设计中。

在设计阶段，还要考虑如何将应用层; 中的安全性一个级别的防御并不一定足够。 如果攻击者获取你的 web 应用程序防火墙 (WAF) 之后，会发生什么情况？ 你想到位，以针对该攻击的另一个安全控件。

这一点，我们将讨论以下安全设计概念和设计安全的应用程序时应满足安全控制：

- 使用安全的编码库和一个软件框架。
- 扫描易受攻击的组件。
- 使用威胁建模应用程序设计过程中。
- 减少攻击面。
- 采用标识的策略作为主要安全边界。
- 需要重新进行身份验证的重要事务。
- 使用密钥管理解决方案来保护密钥、 凭据和其他机密。
- 保护敏感数据。
- 实现防故障的度量值。
- 利用的错误和异常处理。
- 使用日志记录和警报。

### <a name="use-a-secure-coding-library-and-a-software-framework"></a>使用安全的编码库和一个软件框架，

对于开发，使用安全的编码库和具有嵌入安全的软件框架。 开发人员可以使用现有的、 经过验证而不是开发从零开始的安全控制的功能 （加密、 输入保持安全、 输出编码、 密钥或连接字符串和任何其他将被视为安全控件的内容）。 这有助于防止出现与安全相关的设计和实现缺陷的范畴。

请确保使用您的框架和框架中可用的安全功能的最新版本。 Microsoft 提供了一个全面[的开发工具集](https://azure.microsoft.com/product-categories/developer-tools/)面向所有开发人员，致力于任何平台或语言，提供云应用程序。 可以通过选择来自各种代码与所选的语言[Sdk](https://azure.microsoft.com/downloads/)。
您可以充分利用全功能集成的开发环境 (Ide) 和具有高级调试功能及内置 Azure 支持的编辑器。

Microsoft 提供了多种[语言、 框架和工具](https://docs.microsoft.com/azure/index#pivot=sdkstools&panel=sdkstools-all)，可以使用 Azure 上开发应用程序。 例如，[面向.NET 和.NET Core 开发人员的 Azure](https://docs.microsoft.com/dotnet/azure/)。 每种语言和框架，我们提供了，您会发现快速入门、 教程和 API 参考，有助于你快速入门。

Azure 提供各种服务可以使用托管网站和 web 应用程序。 这些服务让你开发你喜爱的语言，无论是.NET、.NET Core、 Java、 Ruby、 Node.js、 PHP 或 Python。
[Azure 应用服务 Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview)（Web 应用） 是其中一种服务。

Web 应用将 Microsoft Azure 的强大功能添加到你的应用程序。 它包括安全性、 负载均衡、 自动缩放和自动的管理。 如包管理、 过渡环境、 自定义域、 SSL/TLS 证书和从 Azure DevOps、 GitHub、 Docker 中心和其他源的连续部署，可以在 Web 应用中还充分利用 DevOps 功能。

Azure 提供托管网站和 web 应用程序可以使用其他服务。 大多数情况下，Web 应用是最佳选择。 对于微服务体系结构，请考虑[Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)。
如果需要更好地控制运行代码的 VM，请考虑使用 [Azure 虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)。
有关如何选择这些 Azure 服务之间的详细信息，请参阅[Azure 应用服务、 虚拟机、 Service Fabric 和云服务的比较](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm)。

### <a name="apply-updates-to-components"></a>将更新应用于组件

若要防止安全漏洞，应持续清点客户端和服务器端组件 （例如，框架和库） 和更新及其依赖项。 持续发布新的漏洞和更新的软件版本。 请确保您具有正在进行计划以监视、 会审和应用更新或配置更改为库和你使用的组件。

请参阅[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page)页上[组件使用的已知漏洞](https://www.owasp.org/index.php/Top_10-2017_A9-Using_Components_with_Known_Vulnerabilities)工具的建议。 您还可以订阅与你使用的组件相关的安全漏洞的电子邮件警报。

### <a name="use-threat-modeling-during-application-design"></a>在应用程序设计期间使用威胁建模

威胁建模是确定潜在的安全威胁，您的业务和应用程序，然后确保适当的缓解措施到位的过程。 SDL 指定团队应参与威胁建模设计阶段，解决潜在问题时相对容易且经济高效。 使用威胁建模的设计阶段可以极大地降低总开发成本。

为了帮助做到威胁建模过程，我们设计[SDL 威胁建模工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)记住的非安全专家进行交流。 此工具的方便威胁建模对所有开发人员通过提供有关如何创建和分析威胁模型的明确指导。

建模应用程序设计和枚举[STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)威胁 — 欺骗、 篡改、 否认、 信息泄露、 拒绝服务和特权提升 — 通过所有信任边界已证明有效的方法若要在早期捕捉设计错误。 下表列出 STRIDE 威胁，并提供一些使用 Azure 提供的功能的示例缓解措施。 这些缓解措施并非在每种情况下都起作用。

| 威胁 | 安全属性 | 潜在的 Azure 平台缓解措施 |
| ---------------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 欺骗               | Authentication        | [需要 HTTPS 连接](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl?view=aspnetcore-2.1&tabs=visual-studio)。 |
| 篡改              | 完整性             | 验证 SSL/TLS 证书。 使用 SSL/TLS 的应用程序必须全面验证它们连接到的实体的 X.509 证书。 使用 Azure 密钥保管库证书对[管理 x509 证书](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)。 |
| 否认性            | 不可否认性       | 启用 Azure [监视和诊断](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)。|
| 信息泄露 | 保密性       | 加密敏感数据[静态](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)并[在传输过程中](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit)。 |
| 拒绝服务      | 可用性          | 监视性能指标的潜在拒绝服务状况。 实现连接筛选器。 [Azure DDoS 防护](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview#next-steps)，与应用程序设计最佳做法相结合，提供对 DDoS 攻击的防御。|
| 特权提升 | 授权         | 使用 Azure Active Directory <span class="underline"> </span> [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)。|

### <a name="reduce-your-attack-surface"></a>减少攻击面

攻击面是总计之和可能发生的潜在漏洞。 在本白皮书中，我们专注于应用程序的攻击面。
重点是保护应用程序免受攻击。 最大程度减少受攻击面既简单又快速方法是从你的应用程序中删除未使用的资源和代码。 较小应用程序、 较小攻击面。 例如，删除：

- 功能尚未尚未发布的代码。
- 调试支持代码。
- 网络接口和协议，不会使用或弃用的。
- 虚拟机和不使用的其他资源。

执行定期清理您的资源和确保删除未使用的代码以确保不存在恶意执行组件攻击的可能性更小的好方法。

更详细、 更深入的方式，以减少攻击面是完成攻击面分析。 攻击面分析可帮助您将需要进行审查和测试存在安全漏洞的系统的组成部分。

攻击面分析的目的是了解应用程序中的风险区域，使开发人员和安全专家了解应用程序的哪些部分是易受攻击。 然后，可在何时以及如何更改的受攻击面，并从风险角度来看这意味着找到方法来最大程度减少此潜在，跟踪。

攻击面分析可帮助您识别：

- 函数和系统的部分需要查看并测试安全漏洞。
- 高风险区域的代码需要深度防御保护 （需要保护系统的部分）。
- 当您更改的受攻击面，并需要刷新威胁评估。

减少攻击者能够利用潜在的弱位置或漏洞的机会需要您全面分析应用程序的整体受攻击面。 它还包括禁用或限制对系统服务，将最低权限原则应用的访问，并尽可能采用分层防御。

我们将讨论[开展攻击面检查](secure-develop.md#conduct-attack-surface-review)SDL 的验证阶段。

> [!NOTE]
> **威胁建模和攻击面分析之间的区别是什么？**
威胁建模是确定你的应用程序的潜在安全威胁并确保正确防御威胁的缓解措施到位的过程。 攻击面分析发现代码是易受攻击的高风险的领域。 它涉及寻找各种方法来保护高风险区域的应用程序以及查看和部署应用程序之前测试代码的那些方面。

### <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>采用标识用作主要安全边界的策略

在设计云应用程序时，务必展开标识为中心的方法将安全外围重点从以网络为中心的方法。 从历史上看，主要的本地安全边界就是组织的网络。 大多数本地安全设计使用网络作为主要安全透视。 对于云应用程序，您更好的考虑标识用作主要安全边界。

您可以执行以下操作来开发开发 web 应用程序标识为中心的方法：

- 强制执行多重身份验证的用户。
- 使用强身份验证和授权平台。
- 应用最小特权原则。
- 实现中实时访问。

#### <a name="enforce-multi-factor-authentication-for-users"></a>对用户实施多重身份验证

使用双重身份验证。 双因素身份验证是进行身份验证和授权的当前标准，因为它避免了用户名和密码身份验证类型中固有的安全漏洞。 应设计并配置为使用 Azure 管理界面 （Azure 门户/远程 PowerShell） 和面向客户的服务的访问[Azure 多重身份验证](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication)。

#### <a name="use-strong-authentication-and-authorization-platforms"></a>使用强身份验证和授权平台

使用平台提供的身份验证和授权机制，而不要使用自定义代码。 这是因为开发自定义身份验证代码可以很容易出错。 商业代码 （例如，来自 Microsoft) 通常广泛过安全审查。 [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) 是用于标识和访问管理的 Azure 解决方案。 这些 Azure AD 工具和服务帮助进行安全开发：

- [Azure AD 标识平台 (Azure AD 开发人员)](https://docs.microsoft.com/azure/active-directory/develop/about-microsoft-identity-platform)是一种云标识服务，开发人员用于构建安全地登录用户的应用程序。 Azure AD 可帮助生成单租户业务线 (LOB) 应用程序的开发人员和开发人员想要开发多租户应用程序。 除了基本登录，使用 Azure AD 生成的应用程序可以调用 Microsoft Api 和在 Azure AD 平台构建的自定义 Api。 Azure AD 标识平台支持 OAuth 2.0 和 OpenID Connect 等行业标准协议。

- [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/)是标识管理服务，可用于自定义和控制客户如何注册、 登录，以及在使用您的应用程序时管理其配置文件。 这包括应用程序针对 iOS、 Android 和.NET 开发的其他项中。 Azure AD B2C 保护客户标识的同时使这些操作。

#### <a name="apply-the-principle-of-least-privilege"></a>应用最小特权原则

这一概念[最小特权](https://en.wikipedia.org/wiki/Principle_of_least_privilege)意味着向用户提供访问和控制完成其作业而不安装其他所需的精确一些的级别。

软件开发人员需要域管理员权限？ 行政助理将需要访问其个人计算机上的管理控制？ 评估的软件没有任何差别。 如果您使用[基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)为了让应用程序中的用户不同的功能和颁发机构，不会让所有人获得访问权限的所有内容。 通过限制为每个角色所需的访问权限，您可以限制出现安全问题的风险。

确保你的应用程序强制实施[最小特权](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models#in-applications)整个其访问模式。

> [!NOTE]
>  最小特权的规则需要将应用于该软件和创建软件的人员。 如果它们指定了过多的访问权限，软件开发人员可以是到 IT 安全极大的风险。 如果开发人员具有恶意企图或有太多的访问权限，则可能会严重后果。 我们建议的最低权限的规则应用于开发人员在开发生命周期。

#### <a name="implement-just-in-time-access"></a>实现中实时访问

实现*中实时*(JIT) 访问权限来进一步降低特权的曝露时间。 使用[Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#stage-3-build-visibility-and-take-full-control-of-admin-activity)到：

- 为用户提供所需仅 JIT 的权限。
- 分配时限更短的角色，确信权限会自动撤消。

### <a name="require-re-authentication-for-important-transactions"></a>需要重新进行身份验证的重要事务

[跨站点请求伪造](https://docs.microsoft.com/aspnet/core/security/anti-request-forgery?view=aspnetcore-2.1)(也称为*XSRF*或*CSRF*) 是针对恶意 web 应用在其中会影响客户端浏览器和 web 之间的交互的 web 托管的应用程序的攻击信任该浏览器的应用。 跨站点请求伪造攻击是可能的因为 web 浏览器将自动随每个请求某些类型的身份验证令牌发送到网站。
利用此形式是也称为*一键式攻击*或*会话行进*因为此攻击利用用户的身份验证会话。

防范这种攻击的最好办法是向用户请求仅该用户可以提供每个重要的事务，例如购买、 帐户停用或更改密码之前的内容。 您可能会要求用户重新输入其密码，请完成验证码，或提交仅用户可以使用的机密令牌。 最常用的方法是机密令牌。

### <a name="use-a-key-management-solution-to-secure-keys-credentials-and-other-secrets"></a>使用密钥管理解决方案来保护密钥、 凭据和其他机密

丢失密钥和凭据是一个常见问题。 唯一比丢失密钥和凭据更遭糕的事情是让未经授权的一方获取这些密钥和凭据的访问权限。 攻击者可以利用自动和手动方法来查找密钥和 GitHub 等代码存储库中存储的机密。 不要将密钥和机密，这些公共代码存储库中或任何其他服务器上。

始终将你的密钥、 证书、 机密和连接字符串放置在密钥管理解决方案。 可以使用集中式的解决方案中的密钥和机密存储在硬件安全模块 (Hsm)。 Azure 为您提供在云中使用 HSM [Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)。

密钥保管库是*机密存储*： 它是用于存储应用程序机密的集中式的云服务。 密钥保管库将保持您的机密数据安全通过统一的中心位置中使应用程序机密，并提供安全访问、 权限控制和访问日志记录。

机密存储在个人*保管库*。 每个保管库具有其自己的配置和安全策略来控制访问。 获取到你的数据通过 REST API 或客户端适用于大多数编程语言的 SDK。

> [!IMPORTANT]
> Azure 密钥保管库用于存储配置为服务器应用程序机密。 它不是用于存储数据的应用程序用户。 这反映在其性能特征、 API 和成本模型。
>
> 正如 Azure SQL 数据库实例中具有透明数据加密 (TDE) 或使用 Azure 存储服务加密的存储帐户中，应在其他位置，存储用户数据。 可以在 Azure 密钥保管库中保留您的应用程序用于访问这些数据存储的机密。

### <a name="protect-sensitive-data"></a>保护敏感数据

保护数据是安全策略的一个重要部分。
数据分类和标识您的数据保护需求与数据的安全考虑设计您的应用程序的帮助。 分类 （分类） 存储数据的敏感性和业务影响可帮助开发人员确定与数据相关联的风险。

当您设计数据格式时，为敏感标签适用的所有数据。 请确保该应用程序将适用的数据处理为敏感。 这些实践可帮助你保护敏感数据：

- 使用加密。
- 避免进行硬编码的机密密钥和密码等。
- 请确保访问控制和审核就位。

#### <a name="use-encryption"></a>使用加密

保护数据应在安全策略的一个重要部分。
如果你的数据存储在数据库中或在不同位置间来回移动，请使用的加密[静态数据](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)（在数据库中时） 和加密[传输中的数据](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices#protect-data-in-transit)（在与该用户，其方式数据库、 API 或服务终结点）。 我们建议始终使用 SSL/TLS 协议来交换数据。 确保使用最新版本的 TLS 进行加密 （目前，这是版本 1.2）。

#### <a name="avoid-hard-coding"></a>避免硬编码

永远不应硬编码在软件中的一些事项。 一些示例包括主机名或 IP 地址、 Url、 电子邮件地址、 用户名、 密码、 存储帐户密钥和其他加密密钥。 请考虑实施要求可以或不能为硬编码在代码中，你的代码的注释部分中包括的内容。

将注释放在代码中后，请确保未保存任何敏感信息。 这包括在电子邮件地址、 密码、 连接字符串、 有关可能会给攻击者攻击你的应用程序或组织中的优势仅将知道你的组织，和任何其他内容中的某个其他应用程序信息.

基本上，假设你的开发项目中的所有内容将在部署时为公共知识。 避免在项目中包括的任何类型的敏感数据。

更早版本，我们讨论了[Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)。 可以使用密钥保管库存储机密信息，例如密钥和密码而不是它们进行硬编码。 当 Azure 资源管理的标识与结合使用密钥保管库时，Azure web 应用可以访问机密配置值轻松安全地但不存储在源代码管理或配置中的任何机密。 若要了解详细信息，请参阅[管理你的服务器应用与 Azure 密钥保管库中的机密](https://docs.microsoft.com/learn/modules/manage-secrets-with-azure-key-vault/)。

### <a name="implement-fail-safe-measures"></a>实现防故障的度量值

你的应用程序必须能够处理[错误](https://docs.microsoft.com/dotnet/standard/exceptions/)过程中发生的执行以一致的方式。 应用程序应该捕捉所有错误并失败安全的或已关闭。

您还应确保具有足够的用户上下文，以识别可疑网站或恶意活动记录错误。 日志应保留足够的时间，以允许延迟取证分析。 可以通过日志管理解决方案轻松使用的格式应为日志。 请确保触发与安全相关的错误的警报。 没有足够的日志记录和监视使得攻击者能够进一步攻击系统和维护暂留。

### <a name="take-advantage-of-error-and-exception-handling"></a>利用的错误和异常处理

实现正确的错误和[异常处理](https://docs.microsoft.com/dotnet/standard/exceptions/best-practices-for-exceptions)是防御性编码的一个重要部分。 错误和异常处理是至关重要的系统可靠且安全。 不同类型的安全漏洞，如泄露给攻击者的信息和帮助了解有关你的平台和设计的详细信息的攻击者可能会导致错误处理中的错误。

请确保：

- 集中式的方式，以避免在处理异常重复[try/catch 块](https://docs.microsoft.com/dotnet/standard/exceptions/how-to-use-the-try-catch-block-to-catch-exceptions)在代码中。

- 在应用程序内处理所有的意外的行为。

- 向用户显示的消息不会泄漏关键数据，但提供足够的信息来解释该问题。

- 记录异常和它们提供足够的信息可供取证或事件响应团队调查。

[Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)提供的一流的体验[处理错误和异常](https://docs.microsoft.com/azure/logic-apps/logic-apps-exception-handling)，由依赖系统导致的。 逻辑应用可用于创建工作流以自动执行任务和跨企业和组织集成应用、 数据、 系统和服务的进程。

### <a name="use-logging-and-alerting"></a>使用日志记录和警报

[日志](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1)安全调查的你的安全问题和有关问题以确保该人触发警报及时了解问题。 在所有组件中启用审核与日志记录。 审核日志应捕获用户上下文，并识别所有重要事件。

到你的站点，不记录任何用户提交的敏感数据的检查。 敏感数据的示例包括：

- 用户凭据
- 社会保障号码或其他标识信息
- 信用卡号或其他财务信息
- 运行状况信息
- 私钥，或者可用于解密已加密的信息的其他数据
- 可以用来增强应用程序攻击效果的系统信息或应用程序信息

请确保该应用程序监视用户管理事件，例如成功和失败的用户登录名、 密码重置、 密码更改、 帐户锁定和用户注册。 这些事件的日志记录可帮助你检测和响应潜在的可疑行为。 它还允许您收集操作数据，例如谁在访问应用程序。

## <a name="next-steps"></a>后续步骤
在以下文章中，我们建议的安全控件和活动，有助于您开发和部署安全的应用程序。

- [开发安全应用程序](secure-develop.md)
- [部署安全的应用程序](secure-deploy.md)
