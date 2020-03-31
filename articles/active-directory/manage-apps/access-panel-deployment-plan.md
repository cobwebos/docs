---
title: 规划 Azure 活动目录访问面板部署
description: 有关部署 Azure 活动目录访问面板的指南
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2019
ms.author: baselden
ms.reviewer: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d8b6c6d40aa81bf56baed59f90417f2147fa56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897069"
---
# <a name="plan-an-azure-active-directory-access-panel-deployment"></a>规划 Azure 活动目录访问面板部署

Azure 活动目录 （Azure AD） 访问面板是一个基于 Web 的门户，可帮助降低支持成本、提高工作效率和安全性，并减少用户挫折感。 该系统包括详细报告，跟踪您访问系统时的情况，并通知管理员滥用或滥用。

通过使用 Azure AD 访问面板，可以：

* 发现并访问其公司的所有 Azure AD 连接资源，如应用程序
* 请求访问新应用和组
* 为他人管理对这些资源的访问
* 管理自助服务密码重置和 Azure 多重身份验证设置
* 管理其设备

它还允许管理员管理：

* 服务条款
* 组织
* 访问评审


## <a name="benefits-of-azure-ad-access-panel-integration"></a>Azure AD 访问面板集成的优势

Azure AD 访问面板通过以下方式为企业带来好处：

**提供直观的用户体验**：访问面板为所有 Azure 单一登录 （SSO） 连接的应用程序提供一个平台。 您有一个统一的门户来查找现有设置和新功能，如组管理和自助服务密码重置，因为它们被添加。 直观的体验允许用户更快地恢复工作并提高工作效率，同时减少他们的挫折感。

**提高工作效率**：访问面板中的所有用户应用程序都启用了 SSO。 跨企业应用程序和 Office 365 启用 SSO 可减少或消除其他登录提示，从而创建卓越的登录体验。 访问面板使用自助服务和动态成员资格，并提高了您的身份系统的整体安全性。 它通过确保适当的人员管理对应用程序的访问权限来做到这一点。 访问面板是一个连贯的登录页，可供您快速查找资源并继续工作任务。

**管理成本**：使用 Azure AD 启用访问面板有助于剥离本地基础结构。 它通过为您提供一致的门户来查找所有应用、请求访问资源和管理帐户，从而降低了支持成本。

**提高灵活性和安全性**：访问面板使您能够访问云平台提供的安全性和灵活性。 管理员可以轻松地将设置更改为应用程序和资源，并可以容纳新的安全要求，而不会影响用户。

**支持可靠的审核和使用跟踪**：所有用户功能的审核和使用跟踪可让您了解用户何时使用其资源，并确保您可以评估安全性。

### <a name="licensing-considerations"></a>许可注意事项

访问面板是免费的，无需在基本级别使用许可证。 但是，目录中的对象数和要部署的其他功能可能需要其他许可证。 一些具有许可要求的常见 Azure AD 方案包括以下安全功能：

* [Azure 多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)

* [基于组的成员资格](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)

* [自助密码重置](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)

* [Azure Active Directory 标识保护](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

请参阅[Azure AD 的完整许可指南](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites-for-deploying-the-azure-ad-access-panel"></a>部署 Azure AD 访问面板的先决条件

在开始此项目之前完成以下先决条件：

* [集成应用程序 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)

* [管理 Azure AD 用户和组基础结构](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-the-azure-ad-access-panel-deployment"></a>规划 Azure AD 访问面板部署

下表概述了访问面板部署的关键用例：

| 区域| 描述 |
| - | - |
| 访问| 访问面板门户可从公司网络中的公司和个人设备访问。 |
|访问 | 访问面板门户可从公司网络外部的公司设备访问。 |
| 审核| 使用数据至少每 29 天下载一次到公司系统中。 |
| 调控| 定义和监视用户分配到 Azure AD 连接的应用程序和组的生命周期。 |
| 安全性| 通过用户和组分配控制对资源的访问。 只有经过授权的用户可以管理资源访问。 |
| 性能| 记录和监视访问分配传播时间线。 |
| 用户体验| 用户知道访问面板功能以及如何使用它们。|
| 用户体验| 用户可以管理他们对应用程序和组的访问。|
| 用户体验| 用户可以管理其帐户。 |
| 用户体验| 用户知道浏览器兼容性。 |
| 支持| 用户可以找到对访问面板问题的支持。 |

### <a name="best-practices-for-deploying-the-azure-ad-access-panel"></a>部署 Azure AD 访问面板的最佳做法

可以逐步启用访问面板的功能。 我们建议以下部署顺序：

1. 我的应用
   * 应用程序启动器
   * 自助服务应用管理
   * 微软 Office 365 集成

1. 自助服务应用发现
   * 自助式密码重置
   * 多重身份验证设置
   * 设备管理
   * 使用条款
   * 管理组织

1. 我的组
   * 自助组管理
1. 访问评审
   * 访问审核管理

从"我的应用"开始，将用户引入门户，作为访问资源的常见位置。 自助应用程序发现的附加基于"我的应用"体验。 我的组和访问评审基于自助服务功能。

### <a name="plan-configurations-for-the-azure-ad-access-panel"></a>规划 Azure AD 访问面板的配置

下表列出了几个重要的访问面板配置以及您可以使用的典型值：

| Configuration| 典型值 |
| - | - |
| 确定试点组| 确定要使用的 Azure AD 安全组，并确保所有试点成员都是该组的一部分。 |
| 确定要为生产启用的组或组。| 标识要使用的 Azure AD 安全组或同步到 Azure AD 的活动目录组。 确保所有试点成员都是小组的一部分。 |
| 允许用户对某些类型的应用程序使用 SSO| 联合 SSO、 OAuth、 密码 SSO、 应用代理 |
| 允许用户使用自助服务密码重置 | 是 |
| 允许用户使用多重身份验证| 是 |
| 允许用户对某些类型的组使用自助服务组管理| 安全组，Office 365 组 |
| 允许用户使用自助服务应用管理| 是 |
| 允许用户使用访问评论| 是 |

### <a name="plan-consent-strategy"></a>计划同意策略

用户或管理员必须同意任何应用程序的使用条款和隐私政策。 如果可能，如果给定您的业务规则，请使用管理员同意，这为用户提供了更好的体验。

要使用管理员同意，您必须是组织的全局管理员，并且应用程序必须是：

* 在您的组织中注册

* 在另一个 Azure AD 组织中注册，以前至少由一个用户同意

有关详细信息，请参阅[配置最终用户同意在 Azure 活动目录中的应用程序的方式](configure-user-consent.md)。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，它们通常是由于对影响、结果和责任的期望不匹配而达到这一要求。 为了避免这些陷阱，[请确保您与合适的利益相关者接洽](../fundamentals/active-directory-deployment-plans.md)，并确保项目中的利益相关者角色得到充分理解。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功至关重要。 主动告知用户他们的体验将如何改变以及何时变化，以及如何在需要时获得支持。

尽管访问面板通常不会造成用户问题，但请务必做好准备。 在启动前为您的支持人员创建指南和所有资源的列表。

#### <a name="communications-templates"></a>通信模板

Microsoft 为访问面板的电子邮件[和其他通信提供了可自定义的模板](https://aka.ms/APTemplates)。 您可以调整这些资产，以便根据企业文化在其它通信渠道中使用。

## <a name="plan-your-sso-configuration"></a>规划 SSO 配置

当用户登录到应用程序时，他们将经历身份验证过程，并且需要证明他们是谁。 如果没有 SSO，密码存储在应用程序中，并且用户必须知道此密码。 使用 SSO，用户的凭据将传递到应用程序，因此他们不需要为每个应用程序重新输入密码。

要在"我的应用"中启动应用程序，必须启用 SSO。

Azure AD 支持三种不同的方法在[应用程序中启用单一登录](what-is-single-sign-on.md)：

* **联合单一登录** 
    * 使应用程序能够重定向到 Azure AD 进行用户身份验证，而不是提示输入密码。 
    * 支持使用协议的应用程序，如 SAML 2.0、WS-联合或 OpenID 连接，并且是单一登录最丰富的模式。

* **基于密码的单登录** 
    * 通过使用 Web 浏览器扩展或移动应用程序，实现安全的应用程序密码存储和重播。 
    * 利用应用程序提供的现有登录过程，但允许管理员管理密码。 用户不需要知道密码。

* **现有单登录** 
    * 使 Azure AD 能够利用为应用程序配置的任何现有单一登录。
    * 使这些应用程序能够链接到 Office 365 或 Azure AD 访问面板门户。 
    * 在 Azure AD 中启用其他报告，当应用程序启动到那里时。 
    * 包括使用 Azure 应用程序代理和链接的单点登录模式。

了解如何在此处配置应用程序的 SSO 模式：[对 Azure 活动目录中的应用程序单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

为了获得最佳使用"我的应用"页面的体验，从集成可用于联合 SSO 的云应用程序开始。 联合 SSO 允许用户在其应用启动表面上获得一致的一键式体验，并且在配置控制方面往往更加强大。

当应用程序支持 Azure AD 时，将联合 SSO 与 Azure AD（OpenID 连接/SAML）一起使用，而不是使用基于密码的 SSO 和 ADFS。

有关如何部署和配置 SaaS 应用程序的详细信息，请参阅[SaaS SSO 部署计划](https://aka.ms/deploymentplans/sso)。

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>计划部署"我的应用"浏览器扩展

当用户登录到基于密码的 SSO 应用程序时，他们需要安装和使用"我的应用"安全登录扩展。 扩展执行将密码传输到应用程序的登录表单的脚本。 当用户首次启动基于密码的 SSO 应用程序时，系统会提示他们安装扩展。 有关扩展的详细信息，请参阅有关[安装访问面板浏览器扩展的](https://docs.microsoft.com/azure/active-directory/application-access-panel-extension-problem-installing?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)本文档。

如果必须集成基于密码的 SSO 应用程序，则应定义一种机制，以便使用[受支持的浏览器](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)大规模部署扩展。 选项包括：

* [Internet 资源管理器的组策略](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)

* [互联网资源管理器的配置管理器](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)

* [Chrome、火狐、微软边缘或 IE 的用户驱动的下载和配置](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

了解更多：[如何配置密码单一登录](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)。

不使用基于密码的 SSO 应用程序的用户也会从扩展中受益。 这些好处包括能够从搜索栏启动任何应用、查找对最近使用的应用程序的访问权限以及指向"我的应用"页面的链接。

以下是用户首次启动基于密码的 SSO 应用程序时将看到的内容：

!["我的应用"浏览器扩展安装屏幕的屏幕截图 ](media/access-panel-deployment-plan/ap-dp-sign-in.png)

#### <a name="plan-for-mobile-access"></a>规划移动访问

对于启动基于密码的 SSO 应用程序移动用户，需要使用 Intune 策略（Microsoft 边缘或 Intune 托管浏览器）保护的浏览器。 受策略保护的浏览器允许传输为应用程序保存的密码。 Microsoft Edge 或托管浏览器提供了一组 Web 数据保护功能。 您还可以在 iOS 和 Android 设备上将 Microsoft Edge 用于企业方案。 Microsoft Edge 支持与 Intune 托管浏览器相同的管理方案，并改善了用户体验。 了解更多：[使用 Microsoft Intune 策略保护浏览器管理 Web 访问](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

## <a name="plan-your-my-apps-deployment"></a>规划"我的应用部署"

访问面板的基础是应用程序启动器"我的应用程序"，用户在 中[https://myapps.microsoft.com](https://myapps.microsoft.com/)访问它。 "我的应用"页面为用户提供了一个开始工作并访问其必要应用程序的位置。 在这里，用户找到他们具有单一登录访问权限的所有应用程序的列表。 

![应用面板的屏幕截图](media/access-panel-deployment-plan/ap-dp-app-launcher.png)

当用户使用 Office 365 门户时，Office 365 应用启动器中将显示相同的应用程序。

计划将应用程序添加到"我的应用"启动器的顺序，并决定是逐步推出还是一次性推出所有应用程序。 为此，请创建一个应用程序清单，列出每个应用程序的身份验证类型和任何现有的 SSO 集成。

#### <a name="add-applications-to-the-my-apps-panel"></a>将应用程序添加到"我的应用"面板

任何启用 Azure AD SSO 的应用程序都可以添加到"我的应用"启动器中。 其他应用程序通过使用链接 SSO 选项添加。 您可以配置链接到现有 Web 应用程序 URL 的应用程序磁贴。 链接的 SSO 允许您开始将用户定向到"我的应用"门户，而无需将所有应用程序迁移到 Azure AD SSO。 您可以逐渐迁移到 Azure AD SSO 配置的应用程序，而不会中断用户体验。

#### <a name="use-my-apps-collections"></a>使用"我的应用"集合

默认情况下，所有应用程序都一起列在一页上。 但是，您可以使用集合将相关应用程序组合在一起，并在单独的选项卡上显示它们，从而更易于查找它们。 例如，可以使用集合为特定作业角色、任务、项目等创建应用程序的逻辑分组。 有关详细信息，请参阅[如何使用"我的应用"集合自定义用户访问面板](access-panel-collections.md)。 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>计划是使用"我的应用"还是现有门户

您的用户可能已经拥有"我的应用"以外的应用程序或门户。 如果是这样，则决定是同时支持两个门户还是仅使用一个门户。

如果现有门户已用作用户的起点，则可以使用用户访问 URL 集成"我的应用"功能。 用户访问 URL 功能是指向"我的应用"门户中可用的应用程序的直接链接。 这些 URL 可以嵌入到任何现有网站中。 当用户选择该链接时，它会从"我的应用"门户打开应用程序。

您可以在 Azure 门户中的应用程序 **"属性"** 区域中找到用户访问 URL 属性。

![应用面板的屏幕截图](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>规划自助服务应用程序发现和访问

将一组核心应用程序部署到用户的"我的应用"页后，应启用自助服务应用管理功能。 自助服务应用发现使用户能够：

* 查找要添加到其"我的应用"的新应用。 
* 添加他们可能不知道在设置期间需要的可选应用。

审批工作流可用于显式审批以访问应用程序。 当有挂起的访问权限请求时，审批者将在"我的应用"门户中接收通知。

## <a name="plan-self-service-group-membership"></a>计划自助服务组成员身份 

您可以允许用户在 Azure AD 中创建和管理自己的安全组或 Office 365 组。 组的所有者可以批准或拒绝成员身份请求和委派组成员身份的控制。 自助服务组管理功能不适用于启用邮件的安全组或通讯组列表。

要规划自助服务组成员身份，请确定是否允许组织中的所有用户创建和管理组或仅允许用户子集。 如果您允许用户子集，则需要设置一个组，将这些人添加到其中。 有关启用这些方案的详细信息，请参阅[在 Azure 活动目录中设置自助服务组管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)。

## <a name="plan-reporting-and-auditing"></a>计划报告和审计

Azure AD 提供[提供技术和业务见解的报告](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)。 与您的业务和技术应用程序所有者合作，接管这些报告的所有权，并定期使用它们。 下表提供了典型报告方案的一些示例。

|   | 管理风险| 提高生产率| 管理和符合性 |
|  - |- | - | - |
| 报表类型|  应用程序权限和使用情况| 帐户设置活动| 查看访问应用程序的人员 |
| 潜在行动| 审计访问;撤消权限| 修复任何预配错误| “撤销访问权限” |

Azure AD 将大多数审核数据保留 30 天。 数据可通过 Azure 管理门户或 API 获取，可供下载到分析系统中。

#### <a name="auditing"></a>审核

应用程序访问的审核日志提供 30 天。 如果企业内的安全审核需要更长的保留时间，则需要将日志导出到安全信息事件和管理 （SIEM） 工具中，如 Splunk 或 ArcSight。

对于审核、报告和灾难恢复备份，请记录所需的下载频率、目标系统是什么以及谁负责管理每个备份。 您可能不需要单独的审核和报告备份。 灾难恢复备份应该是一个单独的实体。

## <a name="deploy-applications-to-users-my-apps-panel"></a>将应用程序部署到用户的"我的应用"面板

为 SSO 配置应用程序后，将分配组访问权限。 分配的组中的用户将有权访问，并且将在其"我的应用"和 Office 365 应用启动器中看到该应用程序。

请参阅[将用户和组分配给活动目录中的应用程序](methods-for-assigning-users-and-groups.md)。

如果在测试或部署期间要添加组，但尚未允许应用程序显示在"我的应用"中，请参阅在 Azure[活动目录中从用户体验中隐藏应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

### <a name="deploy-microsoft-office-365-applications-to-my-apps"></a>将 Microsoft Office 365 应用程序部署到我的应用程序

对于 Office 365 应用程序，用户会根据分配给他们的许可证接收 Office 的副本。 访问 Office 应用程序的先决条件是为用户分配与 Office 应用程序绑定的正确许可证。 当您为用户分配许可证时，他们将自动在其"我的应用"页面和 Office 365 应用启动器中看到与许可证关联的应用程序。

如果要向用户隐藏一组 Office 应用程序，可以选择从"我的应用"门户隐藏应用，同时仍允许从 Office 365 门户进行访问。 在应用程序的"用户设置"部分找到这些设置。 了解更多：[在 Azure 活动目录中从用户体验中隐藏应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

![配置如何隐藏应用程序的屏幕截图](media/access-panel-deployment-plan/ap-dp-o365-portal.png)

### <a name="deploy-application-self-service-capabilities"></a>部署应用程序自助服务功能

自助服务应用程序访问允许用户自行发现和请求对应用程序的访问。 用户可以自由地访问他们需要的应用，而无需每次通过 IT 组请求访问。 当用户请求访问并经应用所有者自动或手动批准时，它们将添加到后端的组中。 启用了对谁请求、批准或删除访问权限的报告，并使您能够控制管理分配的角色。

您可以将应用程序访问请求的审批委托给业务审批人。 业务审批人可以从业务审批人"我的应用"页面设置应用访问密码。

了解更多：[如何使用自助服务应用程序访问](https://docs.microsoft.com/azure/active-directory/application-access-panel-self-service-applications-how-to)。

![配置自助服务应用程序管理的屏幕截图](media/access-panel-deployment-plan/ap-dp-salesforce.png)

## <a name="validate-your-deployment"></a>验证部署

确保访问面板部署经过全面测试，并实施回滚计划。

以下测试应同时使用公司拥有的设备和个人设备进行。 这些测试用例还应反映您的业务用例。 以下是根据本文档中的示例业务需求和典型技术方案进行的几个案例。 添加特定于您需求的其他组件。

#### <a name="application-sso-access-test-case-examples"></a>应用程序 SSO 访问测试用例示例：


| 业务案例| 预期结果 |
| - | -|
| 用户登录"我的应用"门户| 用户可以登录并查看其应用程序 |
| 用户启动联合 SSO 应用程序| 用户自动登录到应用程序 |
| 用户首次启动密码 SSO 应用程序| 用户需要安装"我的应用"扩展 |
| 用户随后启动密码 SSO 应用程序| 用户自动登录到应用程序 |
| 用户从 Office 365 门户启动应用| 用户自动登录到应用程序 |
| 用户从托管浏览器启动应用| 用户自动登录到应用程序 |


#### <a name="application-self-service-capabilities-test-case-examples"></a>应用程序自助服务功能测试用例示例


| 业务案例| 预期结果 |
| - | - |
| 用户可以管理应用程序的成员资格| 用户可以添加/删除有权访问应用的成员 |
| 用户可以编辑应用程序| 用户可以编辑密码 SSO 应用程序的应用程序描述和凭据 |

### <a name="rollback-steps"></a>回滚步骤

必须规划好当部署无法按计划进行时要怎样做。 如果 SSO 配置在部署期间失败，则必须了解如何[解决 SSO 问题](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso)并减少对用户的影响。 在极端情况下，您可能需要[回滚 SSO。](../manage-apps/plan-sso-deployment.md)


## <a name="manage-your-implementation"></a>管理您的实现

应使用特权最低的角色来完成 Azure 活动目录中所需的任务。 [查看可用的不同角色，](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)并选择正确的角色来解决此应用程序每个角色的需求。 某些角色可能需要临时应用并在部署完成后删除。

| 角色| 角色| Azure AD 角色  |
| - | -| -|
| 帮助台管理员| 第 1 层支持| 无 |
| 身份管理员| 当问题影响 Azure AD 时配置和调试| 全局管理员 |
| 应用程序管理员| 用户在应用程序中的证明，对具有权限的用户的配置| 无 |
| 基础架构管理员| 证书滚存所有者| 全局管理员 |
| 企业主/利益相关者| 用户在应用程序中的证明，对具有权限的用户的配置| 无 |

您可以使用[特权标识管理](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)来管理角色，为具有目录权限的用户提供其他审核、控制和访问审核。

### <a name="troubleshoot-access-panel-issues"></a>排除访问面板问题

为具有常见方案的支持组织创建故障排除指南，这些方案将指向其解决方案中的 Microsoft 文档。 您可能希望创建将支持分解为组织使用的层的指南。

请参阅以下故障排除指南，了解以下参考：

[未显示应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-application-not-appearing)

[出现意外应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-unexpected-application)

[用户无法登录到访问面板](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-web-sign-in-problem)

[使用自助服务应用程序访问的问题](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-troubleshoot-self-service-access)

[浏览器扩展的问题](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-access-panel-browser-extension)

## <a name="next-steps"></a>后续步骤

[规划 Azure 多重身份验证的部署](https://aka.ms/deploymentplans/mfa)
