---
title: 使用 Azure Active Directory 的强大的标识基础的四个步骤
description: 本主题介绍了混合标识客户可以用来构建强大的标识基础的四个步骤。
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
ms.openlocfilehash: a20a1a1009949aa2e6de8586040e918ae15c8d39
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655944"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>使用 Azure Active Directory 的强大的标识基础的四个步骤

管理对应用和数据的访问可以不再依赖于如外围网络和防火墙的传统网络安全边界策略由于快速移动到云的应用。 现在，组织必须信任其标识解决方案控制的人员和内容有权访问组织的应用和数据。 更多的组织允许员工自带设备来处理，并从它们可以连接到 Internet 的任何位置使用他们的设备。 确保这些设备符合要求和安全已成为组织选择实现的标识解决方案中一个重要考虑因素。 在如今的数字工作区[标识是主要的控制平面](https://www.microsoft.com/security/technology/identity-access-management?rtc=1)迁移到云的任何组织。

如何使用 Azure Active Directory (Azure AD) 混合标识解决方案，组织获得解锁通过自动化、 委派、 自助服务，和单一登录功能的工作效率的高级功能访问权限。 它允许您的工作人员从他们需要完成其工作的同时允许你的 IT 团队来管理该访问权限，方法是确保适当的人能正确到适当的资源来建立安全的工作效率的访问权限的任何位置访问公司资源。

根据我们的知识，此清单的最佳实践将帮助您快速部署生成的建议的措施*强*在组织中的 identity foundation:

* 轻松地连接到应用
* 自动建立一个标识每个用户
* 安全地让您的用户
* 实施你的见解

## <a name="step-1---connect-to-apps-easily"></a>步骤 1-轻松地连接到应用

通过与 Azure AD 连接您的应用程序，你可以通过启用单一登录 (SSO) 来提高最终用户工作效率和安全性，并执行用户预配。 管理你的应用在一个位置，Azure AD 中，可以最大程度减少管理开销，并实现安全性和符合性策略的单点控制。

本部分介绍用于管理用户访问应用，启用安全远程访问内部应用，以及将您的应用程序迁移到 Azure AD 的好处的选项。

### <a name="make-apps-available-to-your-users-seamlessly"></a>将应用提供给你的用户无缝

Azure AD 使管理员能够[添加应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)到企业应用程序库中[Azure 门户](https://portal.azure.com/)。 向企业应用程序库添加应用程序，使你更轻松地将应用程序使用 Azure AD 作为标识提供者配置。 它还可以管理用户访问权限的应用程序使用的条件性访问策略，以使用户无需重复输入其密码，并自动登录到在本地对应用程序配置单一登录 (SSO) 和基于云的应用程序。

一旦应用程序添加到 Azure AD 库中，用户可以看到分配给他们并搜索并根据需要请求其他应用程序。 Azure AD 提供[几种方法](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)用户可以访问其应用程序：

* 访问面板 / 我的应用
* Office 365 应用启动器
* 直接登录联合应用
* 直接登录链接

若要了解有关对应用的用户访问的详细信息，请参阅**步骤 3--使用户**这篇文章中。

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>将应用从 Active Directory 联合身份验证服务迁移到 Azure AD

迁移单一登录配置从 Active Directory 联合身份验证服务 (ADFS) 到 Azure AD 实现了安全性、 更一致的可管理性和协作的其他功能。 为了获得最佳结果，我们建议，则您的应用程序从 AD FS 迁移到 Azure AD。 Azure AD 中引入您的应用程序身份验证和授权提供以下优势：

* 管理成本
* 管理风险
* 提高工作效率
* 满足合规性和监管

若要了解详细信息，请参阅[您的应用程序迁移到 Azure Active Directory](https://aka.ms/migrateapps/whitepaper)白皮书。

### <a name="enable-secure-remote-access-to-apps"></a>启用对应用程序的安全远程访问

[Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)提供组织将对本地应用程序发布到云，用于以安全的方式需要内部应用的访问权限的远程用户的简单解决方案。 单一登录方式登录到 Azure AD 之后, 用户可以通过外部 Url 或内部应用程序门户访问在本地和云应用程序。

Azure AD 应用程序代理提供了以下好处：

* 将 Azure AD 扩展到本地资源
  * 云级安全性和保护
  * 功能，如条件性访问和轻松启用多重身份验证
* 例如 VPN 和传统的反向代理解决方案在外围网络中任何组件
* 不需要任何入站连接
* 单一登录 (SSO) 跨设备、 资源和在云中和本地应用程序
* 支持最终用户可以高效工作，随时和任意位置

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>与 Microsoft Cloud App Security 发现影子 IT

在现代企业中，IT 部门不一定了解的用户用于完成其工作的所有云应用程序。 当 IT 管理员询问多少云应用他们认为他们的员工使用，平均他们说 30 或 40。 实际上，平均为 1,000 幅以上单独的应用正在使用你的组织中的员工。 80%的员工使用非已批准应用没有人已检查了并不是符合安全性和符合性策略。

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) 可以帮助您识别与用户受欢迎的有用应用它可以批准并添加到企业应用程序库，以便用户从 SSO 和条件性访问等功能中受益。

*"* * Cloud App Security** 可帮助我们确保我们的员工正确使用的我们的云和 SaaS 应用程序，支持帮助保护 Accenture 的基本安全策略的方式。"*--- [John Blasi，管理信息安全总监 Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

除了检测影子 IT、 MCAS 还可以确定风险级别的应用程序，防止未经授权的访问公司数据、 可能的数据泄漏和其他应用程序中固有的安全风险。

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>步骤 2-自动建立一个标识每个用户

组合在一起的本地和基于云的目录中的 Azure AD 混合标识解决方案可以通过预配在云中将现有标识重复使用现有的本地 Active Directory 投资。 解决方案将同步本地标识与 Azure AD，同时 IT 保留在本地 Active Directory 作为标识的事实的主要来源运行与任何现有的管理解决方案。 Microsoft 的 Azure AD 混合标识解决方案跨越本地和基于云的功能，创建常用用户标识进行身份验证和授权不考虑其位置的所有资源。

将你的本地目录与 Azure AD 使用户提高工作效率和可防止用户通过提供一个通用标识用于访问这两个云应用和服务上使用多个帐户和本地资源。 使用多个帐户是一个痛点，最终用户和 IT 部门。 从最终用户的角度看，具有多个帐户意味着无需记住多个密码。 若要避免此问题，许多用户重复使用每个帐户，这一点非常糟糕从安全角度来看的相同密码。 从 IT 角度，重用通常会导致多个密码重置和技术支持成本，以及最终用户投诉。

Azure AD Connect 是用于同步到 Azure AD，然后可用于访问云应用程序将本地标识的工具。 Azure AD 标识后，它们可以预配 SaaS 应用程序，如 Salesforce 或 Concur。

本部分中，我们列出了用于提供高可用性，云的新式身份验证并减少本地占用空间的建议。

> [!NOTE]
> 如果你想要了解有关 Azure AD Connect 的详细信息，请参阅[什么是 Azure AD Connect Sync？](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>为 Azure AD Connect 设置暂存服务器并使其保持最新

Azure AD Connect 在预配过程中扮演着关键角色。 如果出于任何原因，同步服务器处于脱机状态，更改到的本地不会在云中更新，并导致向用户的访问权限问题。 请务必定义故障转移策略，使管理员能够快速恢复同步之后同步服务器处于脱机状态。

提供事件中的高可用性在主 Azure AD Connect 服务器处于脱机状态，建议你部署一个单独[暂存服务器](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server)Azure AD connect。 部署服务器允许管理员将过渡到生产服务器"升级"由简单的配置开关。 具有处于暂存模式配置的备用服务器还允许你测试和部署新的配置更改并且如果停用旧引入新的服务器。

> [!TIP]
> Azure AD Connect 会定期更新。 因此，强烈建议您将过渡服务器当前以便充分利用性能改进、 bug 修复程序和每个新版本所提供的新功能。

### <a name="enable-cloud-authentication"></a>启用云身份验证

在本地 Active Directory 的组织应将其目录扩展到 Azure AD 中使用 Azure AD Connect 并配置适当的身份验证方法。 [选择正确的身份验证方法](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)为你的组织将应用移动到云的旅程中的第一步。 它是一个重要的组成部分，因为它控制对所有云数据和资源的访问。

启用 Azure AD 中的本地目录对象的云身份验证的最简单和推荐的方法是启用[密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)(PHS)。 或者，某些组织可能会考虑启用[直通身份验证](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)(PTA)。

无论您是选择 PHS 或 PTA，不要忘记启用[无缝单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)以允许用户访问云应用，而无需不断地输入其用户名和密码在应用程序时对你公司使用 Windows 7 和 8 设备网络。 无需实现单一登录，用户必须特定于应用程序的密码并登录到每个应用程序。 同样，IT 人员需要创建和更新每个应用程序，例如 Office 365、 Box 和 Salesforce 的用户帐户。 用户需要记住他们的密码，加上花些时间来登录到每个应用程序。 提供一种标准化单一登录机制为整个企业的最佳用户体验、 减少了的风险、 报告、 功能和管理至关重要。

对于已在使用 AD FS 或其他本地身份验证提供程序的组织，移动到 Azure AD 作为标识提供程序可以降低复杂性并提高可用性。 除非有特定用例使用联合身份验证，我们建议从联合身份验证迁移到 PHS 和无缝 SSO 或 PTA 和无缝 SSO，享受减少的本地占用空间和灵活性与云的好处改进的用户体验。 有关详细信息，请参阅[联合身份验证从迁移到 Azure Active directory 密码哈希同步](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)。

### <a name="enable-automatic-deprovisioning-of-accounts"></a>启用自动取消预配的帐户

启用自动预配和取消预配到您的应用程序是用于标识生命周期管理跨多个系统的最佳策略。 Azure AD 支持[自动化的基于策略的预配和取消预配](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)的用户帐户添加到常见 SaaS 应用程序如 ServiceNow 和 Salesforce，以及其他实现的各种[SCIM 2.0协议](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)。 与传统的预配解决方案，它需要自定义代码或手动上传的 CSV 文件，预配服务托管在云中，以及功能预先集成了连接器，可以设置和管理使用 Azure 门户。 自动取消预配的主要优点是它可帮助保护你的组织他们离开组织时立即关键 SaaS 应用中删除用户的标识。

若要了解有关用户帐户自动预配及其工作原理的详细信息，请参阅 [Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

## <a name="step-3---empower-your-users-securely"></a>步骤 3-安全地让您的用户

在当今数字工作区中，务必平衡安全与工作效率。 但是，最终用户通常抵制降低其工作效率和云应用的访问权限的安全措施。 若要帮助解决此问题，Azure AD 提供自助服务功能，使用户能够管理开销降到最低的同时保持工作效率。

本部分列出了在保持警惕的同时使您的用户从组织删除冲突的建议。

### <a name="enable-self-service-password-reset-for-all-users"></a>为所有用户启用自助密码重置

Azure 的[自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)(SSPR) 提供了一个简单意味着 IT 管理员可以允许用户重置和解锁其密码或帐户而无需管理员干预。 系统提供详细的报告，用于跟踪用户访问系统的时间，同时还提供通知，提醒用户存在误用或滥用情况。

默认情况下，Azure AD 在执行密码重置时会解锁帐户。 但是，当启用 Azure AD Connect[集成的本地](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)，还可以选择要区分这两个操作，让用户直接解锁其帐户而无需重置密码。

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>确保所有用户都注册 MFA 和 SSPR

Azure 提供了可供你和你的组织使用以确保用户已注册 MFA 和 SSPR 报告。 尚未注册的用户可能需要让他们了解该过程。

MFA[登录报表](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting)包括有关 MFA 使用情况信息，使你能够深入了解 MFA 在组织中的工作方式。 使其可以访问登录活动 （和审核和风险事件） 的 Azure AD 进行故障排除、 使用情况分析和取证调查至关重要。

同样，[自助服务密码管理报告](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)可用于确定哪些用户具有 （或尚未） 已注册 SSPR。

### <a name="self-service-app-management"></a>自助服务应用程序管理

你的用户可以自己发现其访问面板中的应用程序之前，需要启用[自助服务应用程序访问](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access)到你想要允许用户自行发现并请求任何应用程序访问。 自助服务应用程序访问是允许用户自行发现应用程序并 （可选） 允许业务组批准对这些应用程序的访问的好办法。 您可以允许业务组来管理分配给用户的凭据[在应用程序密码单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app)直接从其访问面板。

### <a name="self-service-group-management"></a>自助组管理

将用户分配到应用程序时，将最佳映射使用组，因为它们允许极大的灵活性和大规模管理功能：

* 基于属性的使用动态组成员身份
* 应用程序所有者的委派

Azure AD 提供使用安全组和 Office 365 组管理资源访问权限的功能。 可以批准或拒绝成员资格请求和委派的组成员身份控制的组所有者可以管理这些组。 名为[自助服务组管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)，此功能组所有者未分配有管理角色创建和管理组，而无需依赖于管理员来处理其请求，从而节省时间。

## <a name="step-4---operationalize-your-insights"></a>步骤 4-实施你的见解

审核和安全相关事件和相关的警报的日志记录是策略的有效，以确保用户保持工作效率和你的组织是策略的安全的基本组件。 安全日志和报告可以帮助回答问题如：

* 您正在使用要为付费？
* 是否有任何可疑或恶意我的租户中发生的情况？
* 用户已在安全事件期间受影响？

安全日志和报告提供可疑活动的电子记录，并帮助检测可能指示试图或已成功的外部网络渗透以及内部攻击的模式。 可以使用审核来监控用户活动、 记录法规遵从性，执行取证分析和的详细信息。 警报提供安全事件的通知。

### <a name="assign-least-privileged-admin-roles-for-operations"></a>分配操作的最小特权的管理员角色

考虑到操作方法时，有几个级别的管理来考虑。 第一个级别将置于你的全局管理员的管理负担。 始终使用全局管理员角色，可能适合于小型公司。 但对于大型组织与技术支持人员和管理员负责执行特定任务中，分配全局管理员角色可以带来安全风险因为它提供的个人能够管理所总结的任务它们应该是功能的什么支持。

在这种情况下，应考虑下一级别的管理。 使用 Azure AD，可以指定为"受限管理员"可以管理在较低权限角色的任务的最终用户。 例如，你可能会分配您的帮助支持人员[安全读取者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader)角色，向他们提供的功能来管理具有只读访问权限与安全相关的功能。 或可能是，最好将分配[身份验证管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator)给各位成员以使它们能够重置非密码凭据或读取和配置 Azure 服务运行状况的角色。

若要了解详细信息，请参阅[Azure Active Directory 中的管理员角色权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>监视混合组件 （Azure AD Connect 同步中，AD FS） 使用 Azure AD Connect Health

Azure AD Connect 和 AD FS 可生命周期管理和身份验证都可能会破坏并最终会导致中断的关键组件。 因此，你应该部署 Azure AD Connect Health 进行监视和报告这些组件。

若要了解详细信息，请转读取[使用 Azure AD Connect Health 监视 AD FS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)。

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>使用 Azure Monitor 收集数据日志，以便分析

[Azure 监视器](https://docs.microsoft.com/azure/azure-monitor/overview)是一个统一监视门户为 Azure AD 的所有日志，它提供高级分析和智能机器学习的深入见解。 通过 Azure Monitor，可以使用指标和日志在门户中，并通过 Api 以获取更多可见性的状态和资源的性能。 这样，在单一的同时实现范围广泛的产品集成，支持传统的第三方 SIEM 系统中的 Api 和数据导出选项通过在门户中的玻璃体验。 Azure 监视器还可以配置警报规则以获得通知，或对影响资源的问题执行自动的操作的功能。

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>创建你的杰出领导和一天到一天的工作的自定义仪表板

可以下载没有 SIEM 解决方案的组织[Power BI 内容包](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack)Azure AD 的。 Power BI 内容包包含预构建的报告以帮助您了解如何在你的用户采用和使用 Azure AD 功能，可用于深入了解你的目录中的所有活动。 您还可以创建您自己[自定义仪表板](https://docs.microsoft.com/power-bi/service-dashboards)领导团队在日常活动报告与团队共享。 仪表板是指标的监视你的业务和查看所有最重要一目了然的好办法。 仪表板上的可视化效果可能会从基础数据集或多个，和一个基础报表。 仪表板组合在本地和云中的数据，提供合并的视图而不考虑数据存在的位置。

![Power BI 自定义仪表板](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>了解支持调用驱动程序

实现本文中所述的混合标识解决方案时，您最终会减少支持电话中。 常见的问题如忘记的密码和帐户锁定缓解通过实现 Azure 的自助服务密码重置，启用自助应用程序访问时，用户可以自行发现并请求访问应用程序而不依赖在您的 IT 员工。

如果你看不到支持呼叫的减少，我们建议以试图确认如果 SSPR 或自助服务应用程序的访问是否已正确配置分析支持调用驱动程序或是否有任何其他可以系统地为的新问题解决问题。

*"在数字化转换之旅，我们需要可靠的标识和访问管理提供程序，以便我们、 合作伙伴和云服务提供商，为有效的生态系统; 之间的无缝且安全集成Azure AD 时提供我们所需的功能和可见性，使我们能够检测和响应风险的最佳选项。"* --- [Yazan Almasri，全球信息安全总监 Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>监视使用情况的推动深入见解的应用

除了发现影子 IT，跨组织使用监视应用使用情况[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)可以帮助您的组织，在您着手充分利用云应用程序的承诺。 它可以帮助使保持在通过提高活动可见性对资产的控制并提高跨云应用程序的关键数据的保护。 监视应用使用情况中使用 MCAS 您的组织可以帮助您回答以下问题：

* 员工都使用哪些未批准的应用存储中的数据？
* 位置和时间是敏感数据被存储在云中？
* 谁在访问云中的敏感数据？

*"使用 Cloud App Security，我们可以快速找出异常并采取措施。"* --- [Eric LePenske 高级管理器中，信息安全 Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>总结

有很多方面实现混合标识解决方案，但此四步清单将帮助你快速完成将使用户能够提高工作效率和安全的标识基础结构。

* 轻松地连接到应用
* 自动建立一个标识每个用户
* 安全地让您的用户
* 实施你的见解

我们希望本文档是有用的路线图建立你的组织的强大的标识基础。

## <a name="identity-checklist"></a>标识清单

我们建议在组织中开始将旅程迁移到更加可靠的 identity foundation 打印引用的下列清单。

### <a name="today"></a>今天

|完成了吗？|Item|
|:-|:-|
||试验自助服务密码重置 (SSPR) 的组|
||使用 Azure AD Connect Health 监视混合组件|
||分配操作的最小特权的管理员角色|
||与 Microsoft Cloud App Security 发现影子 IT|
||使用 Azure Monitor 收集数据日志以便进行分析|

### <a name="next-two-weeks"></a>接下来两周

|完成了吗？|Item|
|:-|:-|
||使应用可用于你的用户|
||试运行 Azure AD 预配为所选的 SaaS 应用|
||Azure AD connect 设置暂存服务器并保持最新|
||开始从 ADFS 的应用程序迁移到 Azure AD|
||创建你的杰出领导和一天到一天的工作的自定义仪表板|

### <a name="next-month"></a>下个月

|完成了吗？|Item|
|:-|:-|
||监视使用情况的推动深入见解的应用|
||试验到应用的安全远程访问|
||确保所有用户都注册 MFA 和 SSPR|
||启用云身份验证|

### <a name="next-three-months"></a>接下来的三个月

|完成了吗？|Item|
|:-|:-|
||启用自助服务应用程序管理|
||启用自助服务组管理|
||监视使用情况的推动深入见解的应用|
||了解支持调用驱动程序|

## <a name="next-steps"></a>后续步骤

了解如何提高您使用的功能的安全状况的 Azure Active Directory 和此五步清单-[保护标识基础结构的五个步骤](https://aka.ms/securitysteps)。

了解 Azure AD 中的标识功能如何帮助你快速过渡到云的解决方案和功能，组织可快速采用和在传统移动多个其标识管理，从而控制的管理在本地到 Azure AD-系统[Azure AD 提供的云控制管理的本地工作负荷](https://aka.ms/cloudgoverned)。
