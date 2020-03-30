---
title: 强大的身份基础的四个步骤 - Azure AD
description: 本主题介绍混合标识客户为构建强大的身份基础而可以采取的四个步骤。
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74206777"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>使用 Azure 活动目录建立强大的标识基础的四个步骤

由于应用快速向云移动，管理对应用和数据的访问不能再依赖于传统的网络安全边界策略，如外围网络和防火墙。 现在，组织必须信任其身份解决方案，以控制对组织应用和数据具有访问权限的人员和内容。 越来越多的组织允许员工自带设备工作，并从任何可以连接到 Internet 的地方使用设备。 确保这些设备符合要求和安全已成为组织选择实施的标识解决方案中的一个重要考虑因素。 在当今的数字工作场所中，身份是任何迁移到云的组织[的主要控制平面](https://www.microsoft.com/security/technology/identity-access-management?rtc=1)。

在采用 Azure 活动目录 （Azure AD） 混合标识解决方案时，组织可以访问高级功能，这些功能通过自动化、委派、自助服务和单一登录功能来解锁工作效率。 它允许您的员工从需要工作的地方访问公司资源，同时允许您的 IT 团队通过确保合适的人员能够正确访问正确的资源来建立安全生产力来管理这种访问。

根据我们的学习，此最佳实践清单将帮助您快速部署建议的操作，在您的组织中建立*牢固的*身份基础：

* 轻松连接到应用
* 自动为每个用户建立一个标识
* 为您的用户安全提供支持
* 操作您的见解

## <a name="step-1---connect-to-apps-easily"></a>步骤 1 - 轻松连接到应用

通过将应用与 Azure AD 连接，可以通过启用单一登录 （SSO） 并执行用户预配来提高最终用户的工作效率和安全性。 通过在单个位置 Azure AD 管理应用，可以最大限度地减少管理开销，并为安全和合规性策略实现单一控制点。

本节介绍用于管理用户访问应用、启用对内部应用的安全远程访问以及将应用迁移到 Azure AD 的好处的选项。

### <a name="make-apps-available-to-your-users-seamlessly"></a>无缝为用户提供应用

Azure AD 使管理员能够将[应用程序添加到](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) [Azure 门户](https://portal.azure.com/)中的企业应用程序库。 将应用程序添加到企业应用程序库可以更轻松地将应用程序配置为使用 Azure AD 作为标识提供程序。 它还允许您使用条件访问策略管理用户对应用程序的访问，并将单一登录 （SSO） 配置为应用程序，以便用户不必重复输入其密码，并自动登录到本地和基于云的应用程序。

将应用程序添加到 Azure AD 库后，用户可以查看分配给它们的应用，并根据需要搜索和请求其他应用。 Azure AD 为用户提供[了几种](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)访问其应用的方法：

* 访问面板/我的应用程序
* Office 365 应用程序启动程序
* 直接登录联合应用
* 直接登录链接

要了解有关用户访问应用的更多信息，请参阅本文**中的步骤 3 -- 增强用户的能力**。

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>将应用从活动目录联合服务迁移到 Azure AD

将单一登录配置从活动目录联合服务 （ADFS） 迁移到 Azure AD 可实现安全性、更一致的可管理性和协作性的其他功能。 为获得最佳结果，我们建议您将应用从 AD FS 迁移到 Azure AD。 将应用程序身份验证和授权引入 Azure AD 可为您提供以下好处：

* 管理成本
* 管理风险
* 提高生产力
* 解决合规性和治理问题

要了解更多信息，请参阅[将应用程序迁移到 Azure 活动目录](https://aka.ms/migrateapps/whitepaper)的白皮书。

### <a name="enable-secure-remote-access-to-apps"></a>启用对应用的安全远程访问

[Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)为组织提供了一个简单的解决方案，以便为需要以安全方式访问内部应用的远程用户将本地应用发布到云。 对 Azure AD 进行单个登录后，用户可以通过外部 URL 或内部应用程序门户访问云和本地应用程序。

Azure AD 应用程序代理具有以下优点：

* 将 Azure AD 扩展到本地资源
  * 云级安全性和保护
  * 易于启用的条件访问和多重身份验证等功能
* 外围网络中没有组件，如 VPN 和传统反向代理解决方案
* 不需要任何入站连接
* 云和本地中的设备、资源和应用的单一登录 （SSO）
* 使最终用户随时随地高效工作

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>使用 Microsoft 云应用安全性发现影子 IT

在现代企业中，IT 部门通常未注意用户用于完成其工作的所有云应用程序。 当 IT 管理员被问到他们认为员工使用多少云应用时，他们平均说 30 或 40 个云应用。 实际上，组织中的员工平均使用超过 1，000 个单独的应用。 80% 的员工使用未经批准的应用，但没有人审查过这些应用，并且可能不符合您的安全和合规性政策。

[Microsoft 云应用安全](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)（MCAS） 可以帮助您识别受 IT 可能会批准并添加到企业应用程序库的用户欢迎的有用应用，以便用户从 SSO 和条件访问等功能中受益。

<em>**"云应用安全**可帮助我们确保我们的员工正确使用我们的云和 SaaS 应用程序，以支持有助于保护埃森哲的基本安全策略。</em> --- [约翰·布拉西，埃森哲信息安全董事总经理](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

除了检测影子 IT 之外，MCAS 还可以确定应用的风险级别，防止未经授权访问公司数据、可能数据泄露以及应用程序固有的其他安全风险。

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>步骤 2 - 自动为每个用户建立一个标识

在 Azure AD 混合标识解决方案中整合本地目录和基于云的目录，将允许您通过在云中预配现有标识来重用现有的本地活动目录投资。 该解决方案与 Azure AD 同步本地标识，而 IT 使本地活动目录与任何现有治理解决方案一起运行，作为标识的主要真实来源。 Microsoft 的 Azure AD 混合标识解决方案跨越本地和基于云的功能，创建通用用户标识，以便对所有资源进行身份验证和授权，而不管它们位于何处。

将本地目录与 Azure AD 集成，可提高用户工作效率，并通过提供用于访问云和本地资源的通用标识，防止用户跨应用和服务使用多个帐户。 对于最终用户和 IT 来说，使用多个帐户都是一个难题。 从最终用户的角度来看，拥有多个帐户意味着必须记住多个密码。 为了避免这种情况，许多用户为每个帐户重用相同的密码，从安全角度来看，这是不好的。 从 IT 角度来看，重用通常会导致更多的密码重置和帮助台成本以及最终用户投诉。

Azure AD 连接是用于将本地标识同步到 Azure AD 的工具，然后可用于访问云应用程序。 标识在 Azure AD 中后，它们可以预配到 SaaS 应用程序（如 Salesforce 或 Concur）。

在本节中，我们列出了为云提供高可用性、现代身份验证和减少本地占用空间的建议。

> [!NOTE]
> 如果要了解有关 Azure AD 连接的更多内容，请参阅[什么是 Azure AD 连接同步？](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>为 Azure AD 连接设置暂存服务器，并使其保持最新

Azure AD 连接在预配过程中扮演着关键角色。 如果同步服务器由于任何原因脱机，则对本地的更改将不会在云中更新，并导致用户访问问题。 请务必定义故障转移策略，允许管理员在同步服务器脱机后快速恢复同步。

为了在主 Azure AD Connect 服务器脱机时提供高可用性，建议为 Azure AD Connect 部署单独的[暂存服务器](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server)。 部署服务器允许管理员通过简单的配置开关将暂存服务器"提升"到生产。 在暂存模式下配置备用服务器还允许您测试和部署新的配置更改，并在旧服务器停用时引入新服务器。

> [!TIP]
> Azure AD 连接会定期更新。 因此，强烈建议您保持暂存服务器的电流，以便利用每个新版本提供的性能改进、错误修复和新功能。

### <a name="enable-cloud-authentication"></a>启用云身份验证

具有本地活动目录的组织应使用 Azure AD 连接将其目录扩展到 Azure AD 并配置适当的身份验证方法。 为组织[选择正确的身份验证方法是](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)将应用迁移到云的第一步。 它是一个关键组件，因为它控制对所有云数据和资源的访问。

为 Azure AD 中的本地目录对象启用云身份验证的最简单和推荐的方法是启用[密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)（PHS）。 或者，某些组织可能会考虑启用[直通身份验证](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)（PTA）。

无论您选择小灵通还是 PTA，不要忘记启用[无缝单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)功能，以允许用户在使用公司网络上的 Windows 7 和 8 设备时，无需在应用中不断输入用户名和密码即可访问云应用。 如果没有单一登录，用户必须记住特定于应用程序的密码并登录到每个应用程序。 同样，IT 人员需要为每个应用程序（如 Office 365、Box 和 Salesforce）创建和更新用户帐户。 用户需要记住其密码，以及花时间登录到每个应用程序。 为整个企业提供标准化的单一登录机制对于最佳用户体验、降低风险、报告能力和治理至关重要。

对于已经使用 AD FS 或其他本地身份验证提供程序的组织，迁移到 Azure AD 作为标识提供程序可以减少复杂性并提高可用性。 除非您有使用联合的特定用例，否则我们建议您从联合身份验证迁移到小灵通和无缝 SSO 或 PTA 和无缝 SSO，以享受减少本地占用空间的优势以及云提供的灵活性改进的用户体验。 有关详细信息，请参阅[Azure 活动目录从联合迁移到密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)。

### <a name="enable-automatic-deprovisioning-of-accounts"></a>启用自动取消预配帐户

为应用程序启用自动预配和取消预配是管理跨多个系统标识生命周期的最佳策略。 Azure AD 支持[自动、基于策略的预配和取消预配](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)用户帐户，以各种流行的 SaaS 应用程序（如 ServiceNow 和 Salesforce）以及实现[SCIM 2.0 协议](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)的其他应用程序进行预配和取消预配。 与需要自定义代码或手动上载 CSV 文件的传统预配解决方案不同，预配服务托管在云中，并具有可以使用 Azure 门户进行设置和管理的预集成连接器。 自动取消预配的一个关键好处是，它有助于保护组织的安全，当用户在离开组织时立即从关键的 SaaS 应用中删除他们的身份。

若要了解有关用户帐户自动预配及其工作原理的详细信息，请参阅 [Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

## <a name="step-3---empower-your-users-securely"></a>第 3 步 - 安全地为用户提供支持

在当今的数字工作场所中，在安全性与生产力之间取得平衡非常重要。 但是，最终用户通常会拒绝采取安全措施，降低工作效率和对云应用的访问。 为了帮助解决这个问题，Azure AD 提供了自助服务功能，使用户能够保持工作效率，同时最大限度地减少管理开销。

本节列出了通过增强用户能力来消除组织摩擦的建议，同时保持警惕。

### <a name="enable-self-service-password-reset-for-all-users"></a>为所有用户启用自助服务密码重置

Azure 的[自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)（SSPR） 为 IT 管理员提供了一种简单的方法，允许用户重置和解锁其密码或帐户，而无需管理员干预。 系统提供详细的报告，用于跟踪用户访问系统的时间，同时还提供通知，提醒用户存在误用或滥用情况。

默认情况下，Azure AD 在执行密码重置时会解锁帐户。 但是，当您[在本地](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)启用 Azure AD Connect 集成时，您还可以选择分隔这两个操作，这使用户能够解锁其帐户，而无需重置密码。

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>确保所有用户都注册了 MFA 和 SSPR

Azure 提供可供您和您的组织使用的报表，以确保用户注册 MFA 和 SSPR。 尚未注册的用户可能需要接受有关该过程的教育。

MFA[登录报告](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting)包含有关 MFA 使用情况的信息，并让您深入了解 MFA 在组织中的工作方式。 访问 Azure AD 的登录活动（以及审核和风险检测）对于故障排除、使用情况分析和取证调查至关重要。

同样，[自助服务密码管理报告](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)可用于确定谁已注册（或尚未）注册 SSPR。

### <a name="self-service-app-management"></a>自助服务应用管理

在用户可以从访问面板中自行发现应用程序之前，您需要启用[自助服务应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access)对您希望允许用户自行发现和请求访问的任何应用程序的访问权限。 自助服务应用程序访问是允许用户自行发现应用程序以及允许业务组批准对这些应用程序的访问的好方法。 您可以允许业务组直接从访问面板管理分配给这些用户的凭据，以便使用[密码单签名应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app)。

### <a name="self-service-group-management"></a>自助组管理

在使用组时，最好映射将用户分配给应用程序，因为它们具有极大的灵活性和大规模管理能力：

* 使用动态组成员身份基于属性
* 委派给应用所有者

Azure AD 提供使用安全组和 Office 365 组管理资源访问权限的功能。 这些组可以由组所有者管理，该所有者可以批准或拒绝成员身份请求和委派组成员身份控制。 此功能称为[自助服务组管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)，它允许未分配管理角色的组所有者创建和管理组，而无需依赖管理员来处理其请求，从而节省时间。

## <a name="step-4---operationalize-your-insights"></a>第 4 步 - 操作您的见解

审核和记录与安全相关的事件和相关警报是高效策略的重要组成部分，可确保用户保持高效工作，并确保您的组织安全。 安全日志和报告可以帮助回答以下问题：

* 您是否在使用您支付的报酬？
* 我的租户中是否发生了可疑或恶意事件？
* 谁在安全事件中受到影响？

安全日志和报告提供可疑活动的电子记录，并帮助检测可能指示试图或已成功的外部网络渗透以及内部攻击的模式。 您可以使用审核来监视用户活动、记录法规遵从性、进行取证分析等。 警报提供安全事件的通知。

### <a name="assign-least-privileged-admin-roles-for-operations"></a>为操作分配最低特权的管理员角色

在考虑操作方法时，需要考虑几个级别的管理。 第一个级别将管理负担置于全局管理员身上。 始终使用全局管理员角色，可能适用于较小的公司。 但对于具有帮助台人员和负责特定任务的管理员的大型组织，分配全局管理员的角色可能会带来安全风险，因为它使这些人员能够管理超出和超越的任务他们应该能做什么。

在这种情况下，应考虑下一级别的管理。 使用 Azure AD，可以将最终用户指定为"受限管理员"，他们可以以较低权限的角色管理任务。 例如，您可以为帮助台人员分配[安全读取器](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader)角色，以为他们提供通过只读访问管理安全相关功能的能力。 或者，将[身份验证管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator)角色分配给个人以授予他们重置非密码凭据或读取和配置 Azure 服务运行状况的能力可能很有意义。

要了解详细信息，请参阅[Azure 活动目录中的管理员角色权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>使用 Azure AD 连接运行状况监视混合组件（Azure AD 连接同步、AD FS）

Azure AD 连接和 AD FS 是关键组件，可能会中断生命周期管理和身份验证，最终导致中断。 因此，应部署 Azure AD 连接运行状况，以便监视和报告这些组件。

要了解更多信息，请转到[使用 Azure AD 连接运行状况读取监视器 AD FS。](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>使用 Azure 监视器收集用于分析的数据日志

[Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/overview)是所有 Azure AD 日志的统一监视门户，可提供深入的见解、高级分析和智能机器学习。 使用 Azure 监视器，您可以在门户中并通过 API 使用指标和日志，以更好地了解资源的状态和性能。 它在门户内实现单一玻璃体验，同时通过支持传统第三方 SIEM 系统的数据导出选项实现广泛的产品集成。 Azure 监视器还使您能够配置警报规则以获得通知，或对影响资源的问题执行自动操作。

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>为您的领导和日常工作创建自定义仪表板

没有 SIEM 解决方案的组织可以下载 Azure AD[的 Power BI 内容包](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack)。 Power BI 内容包包含预先构建的报告，可帮助您了解用户如何采用和使用 Azure AD 功能，从而使您能够深入了解目录中的所有活动。 您还可以创建自己的[自定义仪表板](https://docs.microsoft.com/power-bi/service-dashboards)，并与领导团队共享以报告日常活动。 仪表板是监视您的业务并一目了然地查看所有最重要的指标的好方法。 仪表板上的可视化效果可能来自一个或许多个基础数据集，也可能来自一个或多个基础报表。 仪表板将本地数据和云数据合并到一起，提供合并视图（无论数据源自哪里）。

![电源 BI 自定义仪表板](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>了解您的支持呼叫驱动程序

实现本文中概述的混合标识解决方案时，最终应注意到支持呼叫的减少。 通过实施 Azure 的自助服务密码重置，缓解忘记密码和帐户锁定等常见问题，同时启用自助服务应用程序访问允许用户自行发现应用程序并请求访问应用程序，而无需依赖您的 IT 员工。

如果您未观察到支持呼叫的减少，我们建议您分析支持呼叫驱动程序，以尝试确认 SSPR 或自助服务应用程序访问是否配置正确，或者是否有任何可以系统地出现任何其他新问题解决。

*"在我们的数字化转型之旅中，我们需要一个可靠的身份和访问管理提供商，以促进我们、合作伙伴和云服务提供商之间的无缝安全集成，从而实现有效的生态系统;Azure AD 是提供我们所需的功能和可见性的最佳选项，使我们能够检测和响应风险。* --- [亚赞·阿尔马斯里，阿拉梅斯全球信息安全总监](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>监控应用使用情况，推动洞察

除了发现影子 IT 之外，使用[Microsoft 云应用安全性](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)监视整个组织的应用使用情况还可以帮助您组织充分利用云应用程序的承诺。 通过提高对活动的可见性，并增强对云应用程序关键数据的保护，它可以帮助您控制资产。 使用 MCAS 监视组织中的应用使用情况可以帮助您回答以下问题：

* 员工使用哪些未经批准的应用来存储数据？
* 敏感数据存储在云中的何时何地？
* 谁在云中访问敏感数据？

*"借助云应用安全性，我们可以快速发现异常并采取行动。* --- [埃里克·勒彭斯克，埃森哲信息安全高级经理](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>总结

实现混合标识解决方案有许多方面，但此四步检查表将帮助您快速完成身份基础结构，使用户能够提高工作效率和安全性。

* 轻松连接到应用
* 自动为每个用户建立一个标识
* 为您的用户安全提供支持
* 操作您的见解

我们希望本文档是为您的组织建立牢固的身份基础的有用路线图。

## <a name="identity-checklist"></a>身份清单

我们建议您在开始组织中更稳固的身份基础时打印以下清单供参考。

### <a name="today"></a>今天

|完成了吗？|Item|
|:-|:-|
||针对组的试用自助服务密码重置 （SSPR）|
||使用 Azure AD 连接运行状况监视混合组件|
||为操作分配最低特权的管理员角色|
||使用 Microsoft 云应用安全性发现影子 IT|
||使用 Azure 监视器收集数据日志进行分析|

### <a name="next-two-weeks"></a>接下来的两周

|完成了吗？|Item|
|:-|:-|
||使应用可供用户使用|
||为选择的 SaaS 应用进行试用 Azure AD 预配|
||为 Azure AD 连接设置暂存服务器，并使其保持最新|
||开始将应用从 ADFS 迁移到 Azure AD|
||为您的领导和日常工作创建自定义仪表板|

### <a name="next-month"></a>下个月

|完成了吗？|Item|
|:-|:-|
||监控应用使用情况，推动洞察|
||试用安全远程访问应用|
||确保所有用户都注册了 MFA 和 SSPR|
||启用云身份验证|

### <a name="next-three-months"></a>接下来的三个月

|完成了吗？|Item|
|:-|:-|
||启用自助服务应用管理|
||启用自助服务组管理|
||监控应用使用情况，推动洞察|
||了解您的支持呼叫驱动程序|

## <a name="next-steps"></a>后续步骤

了解如何使用 Azure 活动目录的功能和以下五个步骤检查表（[保护身份基础结构的五个步骤](https://aka.ms/securitysteps)）来增强安全状态。

了解 Azure AD 中的标识功能如何通过提供解决方案和功能，使组织能够快速采用其标识管理并将其从传统的本地系统移动到 Azure AD - [Azure AD 如何为本地工作负载提供云治理管理](https://aka.ms/cloudgoverned)，从而帮助您加快向云治理管理的过渡。
