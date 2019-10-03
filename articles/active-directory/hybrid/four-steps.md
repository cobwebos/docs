---
title: 使用 Azure Active Directory 进行强身份验证的四个步骤
description: 本主题介绍了混合标识客户可以采取的四个步骤来构建强标识基础。
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
ms.openlocfilehash: 36433aef75d38418cb74fcd195dc1e9e902f24a8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70124927"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>使用 Azure Active Directory 进行强身份验证的四个步骤

管理对应用和数据的访问权限后, 就不能再依赖传统网络安全边界策略 (如外围网络和防火墙), 因为应用会迅速移动到云中。 现在, 组织必须信任其身份解决方案, 以控制谁以及哪些人有权访问组织的应用和数据。 更多的组织允许员工自带设备的设备工作, 并从他们可以连接到 Internet 的任何地方使用其设备。 确保这些设备符合和安全已成为组织选择实现的标识解决方案的一个重要考虑因素。 在当今的数字工作区中, 标识是任何组织迁移到云的[主要控制平面](https://www.microsoft.com/security/technology/identity-access-management?rtc=1)。

采用 Azure Active Directory (Azure AD) 混合标识解决方案时, 组织可通过自动化、委托、自助服务和单一登录功能获得对高效率的高级功能的访问权限。 它允许您的工作人员随时随地访问公司资源, 同时允许您的 IT 团队通过确保适当的人员访问正确的资源来建立安全的工作效率。

基于我们的知识, 此最佳实践清单将帮助你快速部署建议的操作, 以便在你的组织中构建*强*身份基础:

* 轻松连接到应用
* 自动为每个用户建立一个标识
* 安全地为用户
* 操作你的见解

## <a name="step-1---connect-to-apps-easily"></a>步骤 1-轻松连接到应用

通过将应用连接 Azure AD, 你可以通过启用单一登录 (SSO) 和执行用户预配来提高最终用户的工作效率和安全性。 通过在一个位置管理你的应用程序 Azure AD, 你可以最大程度地减少管理开销, 并为安全和合规性策略实现单点控制。

本部分介绍了用于管理用户对应用程序的访问权限、支持对内部应用程序进行安全远程访问的选项, 以及将应用程序迁移到 Azure AD 的好处。

### <a name="make-apps-available-to-your-users-seamlessly"></a>使应用可无缝地供用户使用

Azure AD 使管理员能够向[Azure 门户](https://portal.azure.com/)中的企业应用程序库[添加应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)。 通过向企业应用程序库添加应用程序, 你可以更轻松地将应用程序配置为使用 Azure AD 作为标识提供者。 它还允许你使用条件性访问策略管理用户对应用程序的访问权限, 并配置对应用程序的单一登录 (SSO), 以便用户无需重复输入其密码, 自动登录到本地和基于云的应用程序。

将应用程序添加到 Azure AD 库后, 用户可以查看分配给他们的应用, 并根据需要搜索和请求其他应用。 Azure AD 为用户提供[多种方法](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)来访问其应用:

* 访问面板/应用
* Office 365 应用启动器
* 直接登录联合应用
* 直接登录链接

若要详细了解用户对应用的访问权限, 请参阅本文中的**步骤 3--为用户**准备。

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>将应用从 Active Directory 联合身份验证服务迁移到 Azure AD

将单一登录配置从 Active Directory 联合身份验证服务 (ADFS) 迁移到 Azure AD 可实现更高的安全性、更一致的可管理性和协作功能。 为获得最佳结果, 建议将应用从 AD FS 迁移到 Azure AD。 向 Azure AD 提供应用程序身份验证和授权可提供以下优势:

* 管理成本
* 管理风险
* 提高工作效率
* 解决相容性和监管

若要了解详细信息, 请参阅将[应用程序迁移到 Azure Active Directory](https://aka.ms/migrateapps/whitepaper)白皮书。

### <a name="enable-secure-remote-access-to-apps"></a>启用对应用的安全远程访问

[Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)为组织提供了一种简单的解决方案, 用于以安全的方式向需要访问内部应用的远程用户发布本地应用。 单一登录到 Azure AD 后, 用户可以通过外部 Url 或内部应用程序门户访问云应用程序和本地应用程序。

Azure AD 应用程序代理具有以下优势:

* 将 Azure AD 扩展到本地资源
  * 云级安全性和保护
  * 可轻松启用的条件性访问和多重身份验证等功能
* 外围网络中没有组件, 例如 VPN 和传统的反向代理解决方案
* 不需要任何入站连接
* 在云中和本地跨设备、资源和应用的单一登录 (SSO)
* 使最终用户能够随时随地高效地工作

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>发现影子 IT 与 Microsoft Cloud App Security

在现代企业中, IT 部门通常不知道用户使用哪些云应用程序来完成工作。 当 IT 管理员询问他们的员工使用的云应用数量时, 平均为30或40。 实际上, 组织中的员工使用的平均是1000多个单独的应用。 80% 的员工使用未批准的应用, 这些应用未经过审核, 可能不符合你的安全和合规性策略。

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)(MCAS) 可帮助你确定很有用的应用程序, 这些应用程序可能批准并添加到企业应用程序库, 使用户能够受益于 SSO 和条件性访问等功能。

*"**Cloud App Security** 有助于我们确保用户正确地使用我们的云和 SaaS 应用程序, 以支持帮助保护 Accenture 的基础安全策略的方式。"* --- [John Blasi, 管理总监, 信息安全, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

除了检测影子 IT 外, MCAS 还可以确定应用程序的风险级别, 防止未经授权访问公司数据、可能的数据泄漏和应用程序固有的其他安全风险。

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>步骤 2-为每个用户自动建立一个标识

将本地和基于云的目录一起引入 Azure AD 的混合标识解决方案中, 可以通过在云中预配现有标识来重复使用现有的本地 Active Directory 投资。 该解决方案将本地标识与 Azure AD 进行同步, 同时将本地 Active Directory 与任何现有治理解决方案一起运行, 作为标识的主要来源。 Microsoft 的 Azure AD 混合标识解决方案跨越本地和基于云的功能, 为所有资源创建用于身份验证和授权的通用用户标识, 而不考虑其位置。

将本地目录与 Azure AD 集成可提高用户的工作效率, 并可通过提供公共标识访问云和本地资源, 防止用户跨应用和服务使用多个帐户。 对于最终用户和 IT, 使用多个帐户是一个难点。 从最终用户的角度来看, 具有多个帐户意味着必须记住多个密码。 为避免出现这种情况, 许多用户为每个帐户重复使用相同的密码, 这在安全性方面是不正确的。 从 IT 角度来看, 经常重复使用会导致更多的密码重置和技术支持人员成本以及最终用户的投诉。

Azure AD Connect 是一种工具, 用于将本地标识同步到 Azure AD, 然后可以使用该工具访问云应用程序。 Azure AD 标识后, 可以将其预配到 SaaS 应用程序 (如 Salesforce 或 Concur)。

在本部分中, 我们列出了有关为云提供高可用性和新式身份验证的建议, 并减少了本地占用量。

> [!NOTE]
> 如果要了解有关 Azure AD Connect 的详细信息, 请参阅[什么是 Azure AD Connect 同步？](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>为 Azure AD Connect 设置过渡服务器, 并使其保持最新状态

Azure AD Connect 在预配过程中扮演着关键角色。 如果同步服务器出于任何原因进入脱机状态, 则不会在云中更新对本地的更改, 从而导致用户访问权限问题。 必须定义一个故障转移策略, 使管理员能够在同步服务器脱机后快速恢复同步。

若要在主 Azure AD Connect 服务器脱机的情况下提供高可用性, 建议为 Azure AD Connect 部署单独的[临时服务器](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server)。 部署服务器后, 管理员可以通过简单的配置交换机将过渡服务器 "升级" 到生产服务器。 通过在暂存模式下配置备用服务器, 还可以测试和部署新的配置更改, 并在解除旧服务器的授权的情况下引入新的服务器。

> [!TIP]
> 定期更新 Azure AD Connect。 因此, 强烈建议您保持临时服务器最新, 以便充分利用性能改进、bug 修复和每个新版本提供的新功能。

### <a name="enable-cloud-authentication"></a>启用云身份验证

使用本地 Active Directory 的组织应使用 Azure AD Connect 将其目录扩展到 Azure AD, 并配置适当的身份验证方法。 为组织[选择正确的身份验证方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)是将应用程序移动到云的过程中的第一步。 它是关键组件, 因为它控制对所有云数据和资源的访问。

为 Azure AD 中的本地目录对象启用云身份验证的最简单和推荐方法是启用[密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)(PHS)。 此外, 某些组织可能会考虑启用[直通身份验证](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)(PTA)。

无论你选择 PHS 还是 PTA, 别忘了启用[无缝单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)以允许用户在使用企业网络中的 Windows 7 和8设备时, 无需在应用中不断输入用户名和密码即可访问云应用。 如果没有单一登录, 用户必须记住应用程序特定的密码, 并登录到每个应用程序。 同样, IT 人员需要创建和更新每个应用程序 (如 Office 365、Box 和 Salesforce) 的用户帐户。 用户需要记住密码, 并花费时间登录每个应用程序。 为整个企业提供标准化的单一登录机制对于获得最佳用户体验、降低风险、报告和管理能力至关重要。

对于已使用 AD FS 或其他本地身份验证提供程序的组织, 随着标识提供者迁移到 Azure AD, 可以降低复杂性并提高可用性。 除非你有使用联合身份验证的特定用例, 否则建议从联合身份验证迁移到 PHS 和无缝 SSO 或 PTA 和无缝 SSO, 以享受降低的本地占用量和云提供的灵活性改进的用户体验。 有关详细信息, 请参阅[Azure Active Directory 的从联合迁移到密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)。

### <a name="enable-automatic-deprovisioning-of-accounts"></a>启用自动预配帐户

为应用程序启用自动预配和取消预配是在多个系统之间管理标识生命周期的最佳策略。 Azure AD 支持将用户帐户[自动预配和取消预配](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)到各种流行的 SaaS 应用程序 (如 ServiceNow 和 Salesforce) 以及实现[SCIM 2.0 协议](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)的其他应用程序。 与需要自定义代码或手动上传 CSV 文件的传统预配解决方案不同, 预配服务托管在云中, 并具有可使用 Azure 门户进行设置和管理的预集成连接器。 自动取消预配的主要优点是, 它可以在用户离开组织时立即从关键 SaaS 应用中删除用户的标识, 从而帮助保护你的组织。

若要了解有关用户帐户自动预配及其工作原理的详细信息，请参阅 [Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

## <a name="step-3---empower-your-users-securely"></a>步骤 3-安全地准备用户

在当今的数字工作区中, 将安全性与工作效率进行平衡非常重要。 但是, 最终用户常常会推出降低工作效率和访问云应用的安全措施。 为了帮助解决这种情况, Azure AD 提供了自助服务功能, 使用户能够在最大程度地降低管理开销的同时保持工作效率。

本部分列出了通过在保持警惕的同时为用户提供的有关删除组织中的摩擦的建议。

### <a name="enable-self-service-password-reset-for-all-users"></a>为所有用户启用自助密码重置

Azure 的[自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)(SSPR) 为 IT 管理员提供了一种简单的方法, 允许用户在无需管理员干预的情况下重置和解锁其密码或帐户。 系统提供详细的报告，用于跟踪用户访问系统的时间，同时还提供通知，提醒用户存在误用或滥用情况。

默认情况下，Azure AD 在执行密码重置时会解锁帐户。 但是, 当你[在本地](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)启用 Azure AD Connect 集成时, 还可以选择分离这两个操作, 使用户无需重置密码即可解锁其帐户。

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>确保所有用户都注册了 MFA 和 SSPR

Azure 提供可供你和你的组织使用的报表, 以确保用户已注册 MFA 和 SSPR。 在此过程中, 可能需要教育尚未注册的用户。

"MFA[登录" 报表](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting)包含有关 mfa 使用情况的信息, 可帮助你深入了解如何在组织中使用 mfa。 对于故障排除、使用情况分析和取证调查, 有权访问 Azure AD 的登录活动 (以及审核和风险检测) 是至关重要的。

同样, 可以使用[自助服务密码管理报告](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)来确定谁已注册 SSPR。

### <a name="self-service-app-management"></a>自助服务应用管理

在用户可以从其访问面板中自行发现应用程序之前, 你需要启用对你希望允许用户自行发现并请求访问的任何应用程序的[自助服务应用程序访问权限](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access)。 自助应用程序访问是允许用户自行发现应用程序并可以选择允许业务组批准对这些应用程序的访问权限的一种绝佳方式。 您可以允许业务组从其访问面板中管理分配给这些用户的[密码单一登录应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app)的凭据。

### <a name="self-service-group-management"></a>自助组管理

当使用组时, 最好将用户分配到应用程序, 因为它们允许大规模管理的灵活性和能力:

* 基于属性的使用动态组成员身份
* 委托给应用所有者

Azure AD 提供使用安全组和 Office 365 组管理资源访问权限的功能。 可以批准或拒绝成员身份请求并委派组成员身份的控制权限的组所有者可以管理这些组。 此功能称为[自助服务组管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management), 通过允许没有分配管理角色的组所有者来创建和管理组, 而无需依赖管理员来处理其请求, 从而节省了时间。

## <a name="step-4---operationalize-your-insights"></a>步骤 4-操作你的见解

审核和记录与安全相关的事件和相关警报是有效策略的重要组成部分, 可确保用户的工作效率和组织的安全性。 安全日志和报告可以帮助解答问题, 例如:

* 你使用的是什么？
* 我的租户中是否有任何可疑或恶意的情况？
* 安全事件期间, 谁受到了影响？

安全日志和报告提供可疑活动的电子记录，并帮助检测可能指示试图或已成功的外部网络渗透以及内部攻击的模式。 您可以使用审核来监视用户活动、记录法规遵从性、进行鉴证分析等。 警报提供安全事件的通知。

### <a name="assign-least-privileged-admin-roles-for-operations"></a>为操作分配最少特权管理员角色

考虑到操作方法时, 需要考虑几个级别的管理。 第一层将管理负担置于全局管理员。 始终使用全局管理员角色可能适用于小型公司。 但对于具有支持特定任务的技术支持人员和管理员的大型组织而言, 分配全局管理员角色可能会带来安全风险, 因为它提供了管理高于和超过的任务的能力。应执行的操作。

在这种情况下, 你应该考虑下一层的管理。 使用 Azure AD, 你可以将最终用户指定为 "受限管理员", 这些用户可以在权限较低的角色中管理任务。 例如, 你可以向技术支持人员分配[安全读者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader)角色, 使其能够使用只读访问权限来管理与安全相关的功能。 或者, 将[身份验证管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator)角色分配给个人是有意义的, 使其能够重置非密码凭据或读取和配置 Azure 服务运行状况。

若要了解详细信息, 请参阅[Azure Active Directory 中的管理员角色权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>使用 Azure AD Connect Health 监视混合组件 (Azure AD Connect 同步、AD FS)

Azure AD Connect 和 AD FS 是关键组件, 可能会中断生命周期管理和身份验证, 并最终导致服务中断。 因此, 你应该部署 Azure AD Connect Health 来监视和报告这些组件。

若要了解详细信息, 请参阅[使用 Azure AD Connect Health 的读取监视器 AD FS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)。

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>使用 Azure Monitor 收集用于分析的数据日志

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)是适用于所有 Azure AD 日志的统一监视门户, 提供深层 insights、高级分析和智能机器学习。 使用 Azure Monitor, 你可以在门户中使用指标和日志, 并通过 Api 来更好地了解资源的状态和性能。 它在门户中启用了单一的玻璃体验, 同时通过支持传统第三方 SIEM 系统的 Api 和数据导出选项启用各种产品集成。 Azure Monitor 还可以配置警报规则以获得通知, 或对影响资源的问题执行自动操作。

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>为领导和日常工作创建自定义仪表板

没有 SIEM 解决方案的组织可以下载 Azure AD [Power BI 内容包](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack)。 Power BI 内容包包含预构建的报表, 可帮助你了解你的用户如何采用和使用 Azure AD 功能, 这使你能够深入了解你的目录中的所有活动。 您还可以创建自己的[自定义仪表板](https://docs.microsoft.com/power-bi/service-dashboards), 并与领导团队共享来报告日常活动。 仪表板是一种很好的方式来监视你的业务, 并一目了然地查看所有最重要的指标。 仪表板上的可视化效果可能来自一个基础数据集或多个基础数据集或多个基础数据集。 "仪表板" 结合了本地和云数据, 提供了合并视图, 无论数据位于何处。

![Power BI 自定义仪表板](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>了解支持调用驱动程序

如本文中所述实施混合标识解决方案时, 应最终注意到支持调用的缩减。 通过实现 Azure 的自助密码重置, 可减轻一些常见问题, 如忘记的密码和帐户锁定, 同时启用自助应用程序访问允许用户自行发现并请求访问应用程序, 而无需信赖在 IT 人员上。

如果你没有看到支持调用的减少, 我们建议你分析你的支持调用驱动程序, 尝试确认是否已正确配置 SSPR 或自助服务应用程序访问, 或者是否有任何其他可以系统系统的新问题解除.

*"在我们的数字转换过程中, 我们需要一种可靠的标识和访问管理提供程序, 以便于有效生态系统为我们、合作伙伴和云服务提供商之间实现无缝但安全的集成。Azure AD 是为我们提供所需的功能和可见性的最佳选项, 使我们能够检测和应对风险。 "* --- [Yazan Almasri, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>监视应用的使用情况以推动见解

除了发现影子 IT 外, 使用[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)监视组织中的应用使用情况还有助于你的组织充分利用云应用程序的承诺。 它通过改进活动可见性来帮助你控制资产, 并提高跨云应用程序的关键数据的保护。 使用 MCAS 监视组织中的应用使用情况可帮助你回答以下问题:

* 员工使用哪些未批准应用来存储数据？
* 在云中存储敏感数据的位置和时间
* 谁在访问云中的敏感数据？

*"借助 Cloud App Security, 我们可以快速发现异常并采取措施。"* --- [Eric LePenske, 高级经理, 信息安全, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>总结

实现混合标识解决方案有许多方面, 但这四个步骤的清单将帮助你快速完成一个标识基础结构, 使用户能够更高效、更安全。

* 轻松连接到应用
* 自动为每个用户建立一个标识
* 安全地为用户
* 操作你的见解

我们希望此文档是为你的组织建立强大的标识基础的有用路线图。

## <a name="identity-checklist"></a>标识清单

当你开始在组织中使用更可靠的标识基础时, 建议你打印以下清单以供参考。

### <a name="today"></a>今天

|完成了吗？|项|
|:-|:-|
||组的试点自助密码重置 (SSPR)|
||使用 Azure AD Connect Health 监视混合组件|
||为操作分配最少特权管理员角色|
||发现影子 IT 与 Microsoft Cloud App Security|
||使用 Azure Monitor 收集用于分析的数据日志|

### <a name="next-two-weeks"></a>接下来两周

|完成了吗？|项|
|:-|:-|
||使应用可供用户使用|
||试点 Azure AD 预配适用于所选的 SaaS 应用|
||为 Azure AD Connect 设置过渡服务器, 并使其保持最新状态|
||开始将应用从 ADFS 迁移到 Azure AD|
||为领导和日常工作创建自定义仪表板|

### <a name="next-month"></a>下个月

|完成了吗？|项|
|:-|:-|
||监视应用的使用情况以推动见解|
||向应用程序进行安全远程访问|
||确保所有用户都注册了 MFA 和 SSPR|
||启用云身份验证|

### <a name="next-three-months"></a>接下来三个月

|完成了吗？|项|
|:-|:-|
||启用自助服务应用管理|
||启用自助服务组管理|
||监视应用的使用情况以推动见解|
||了解支持调用驱动程序|

## <a name="next-steps"></a>后续步骤

了解如何使用 Azure Active Directory 的功能和此五步检查列表 ([用于保护标识基础结构的五个步骤](https://aka.ms/securitysteps)) 中的功能提高安全状况。

了解 Azure AD 中的身份功能如何帮助你通过提供解决方案和功能来帮助你加快向云管理管理的过渡, 并使组织能够快速从传统要 Azure AD 的本地系统- [Azure AD 为本地工作负荷提供云管控管理的方式](https://aka.ms/cloudgoverned)。
