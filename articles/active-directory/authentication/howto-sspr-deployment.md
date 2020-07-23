---
title: Azure Active Directory 自助式密码重置的部署注意事项
description: 了解成功实现 Azure AD 自助式密码重置的部署注意事项和策略
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a56f7248d5782b63befc55c4215360e0f5cb52b2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84338560"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>规划 Azure Active Directory 自助式密码重置部署

> [!IMPORTANT]
> 此部署计划提供有关部署 Azure AD 自助式密码重置 (SSPR) 的指南和最佳做法。
>
> **如果你是最终用户并且需要返回到你的帐户，请转到 [https://aka.ms/sspr](https://aka.ms/sspr) **。

[自助式密码重置 (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) 是一项 Azure Active Directory (AD) 功能，使用户无需联系 IT 人员寻求帮助即可重置密码。 用户可以随时随地快速重置密码并继续顺畅工作。 通过允许员工自行重置密码，你的组织可以减少大多数常见的密码相关问题所导致的低效时间和高支持成本。

SSPR 具有以下关键功能：

* 借助自助式，最终用户可以重置过期的或未过期的密码，而无需请求管理员或帮助台提供支持。
* 借助[密码写回](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)，可通过云来管理本地密码和解决帐户锁定的问题。
* 借助密码管理活动报告，管理员能够深入了解发生在其组织中的密码重置和注册活动。

本部署指南介绍了如何计划并测试 SSPR 的推出。

若要快速查看使用中的 SSPR，然后返回并了解其他部署注意事项：

> [!div class="nextstepaction"]
> [启用自助式密码重置 (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>了解 SSPR

了解有关 SSPR 的详细信息。 请参阅[工作原理：Azure AD 自助式密码重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)。

### <a name="key-benefits"></a>主要优点

启用 SSPR 的主要优势包括：

* 管理成本。 SSPR 使用户能够自行重置密码，从而减少了 IT 支持成本。 它还减少了因密码丢失和帐户锁定而损失的时间成本。 

* 直观的用户体验。 它提供了一个直观的一次性用户注册流程，允许用户通过任何设备或从任何位置按需重置密码和解除帐户锁定。 SSPR 使用户能够更快投入工作并提高工作效率。

* **灵活性和安全性**。 SSPR 使企业能够获得云平台提供的安全性和灵活性。 管理员可以更改设置以满足新的安全要求，并在用户无需中断登录的情况下将相关更改推广到用户。

* 可靠的审核和使用情况跟踪。 组织可以确保业务系统在用户重置其自己的密码时始终处于安全状态。 可靠的审核日志包含密码重置过程中每个步骤的信息。 可以通过 API 获取这些日志，借助日志，用户能够将数据导入所选的安全信息和事件管理 (SIEM) 系统中。

### <a name="licensing"></a>授权

按用户授予 Azure Active Directory 许可意味着每个用户都需要获取适当的许可证才能使用他们所需的功能。 建议使用基于组的 SSPR 许可。 

若要比较版本和功能并启用组或基于用户的许可，请参阅 [Azure AD 自助式密码重置的许可要求](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)。

有关定价的详细信息，请参阅 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites"></a>先决条件

* 一个至少启用了试用版许可证的有效 Azure AD 租户。 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个具有全局管理员权限的帐户。


### <a name="training-resources"></a>培训资源

| 资源| 链接和说明 |
| - | - |
| 视频| [通过更好的 IT 可伸缩性为用户提供助力](https://youtu.be/g9RpRnylxS8) 
| |[什么是自助式密码重置？](https://youtu.be/hc97Yx5PJiM)|
| |[部署自助式密码重置](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[如何在 Azure AD 中为用户配置自助式密码重置？](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[如何 [让用户准备好] 注册 [其] Azure Active Directory 安全信息](https://youtu.be/gXuh0XS18wA) |
| 在线课程|[在 Microsoft Azure Active Directory 中管理标识](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) 使用 SSPR 为用户提供受保护的新式体验。 另请参阅“[管理 Azure Active Directory 用户和组](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)”模块。 |
|Pluralsight 付费课程 |[标识和访问管理问题](https://www.pluralsight.com/courses/identity-access-management-issues) 了解你的组织中需要得到关注的 IAM 和安全问题。 另请参阅“其他身份验证方法”模块。|
| |[Microsoft 企业移动性套件入门](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) 了解采用一种可进行身份验证、授权、加密的方式和安全移动体验将本地资产扩展到云的最佳实践。 另请参阅“配置 Microsoft Azure Active Directory Premium 的高级功能”模块。
|教程 |[完成 Azure AD 自助密码重置试点推广](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[启用密码写回](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[从 Windows 10 的登录屏幕进行 Azure AD 密码重置](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| 常见问题解答|[密码管理常见问题](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>解决方案体系结构

下面的示例介绍适用于常见混合环境的密码重置解决方案体系结构。

![解决方案体系结构关系图](./media/howto-sspr-deployment//solutions-architecture.png)

工作流说明

若要重置密码，用户需要转到[密码重置门户](https://aka.ms/sspr)。 他们必须验证之前注册过的身份验证方法来证明其身份。 如果用户成功重置了密码，便会启动重置过程。

* 对于仅使用云的用户，SSPR 会将新密码存储在 Azure AD 中。 

* 对于混合用户，SSPR 通过 Azure AD Connect 服务将密码写回到本地 Active Directory。 

注意：对于禁用了[密码哈希同步 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 的用户，SSPR 将密码仅存储在本地 Active Directory 中。

### <a name="best-practices"></a>最佳做法

可以通过在组织中将 SSPR 与其他常用应用程序或服务一起部署，来帮助用户快速注册。 此操作会产生大量登录，可推进注册操作。

在部署 SSPR 之前，可以选择确定每个密码重置调用的数量和平均成本。 可以使用此“数据发布”部署来展示 SSPR 为组织带来的价值。

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>启用 SSPR 和 MFA 的组合注册

Microsoft 建议组织启用 SSPR 和多重身份验证的组合注册体验。 启用此组合注册体验后，用户只需选择其注册信息一次即可启用这两种功能。

组合注册体验不需要组织同时启用 SSPR 和 Azure 多重身份验证。 组合注册可为组织提供更好的用户体验。 有关详细信息，请参阅[组合安全信息注册](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>规划部署项目

在环境中确定此部署的策略时，请考虑你的组织需求。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

当技术项目失败时，它们通常是由于在影响、结果和责任方面不符合预期而导致的。 若要避免这些问题，请[确保让合适的利益干系人参与](https://aka.ms/deploymentplans)，并通过记录利益干系人及其项目的输入信息和相应责任，使项目中利益干系人的角色得到充分了解。

#### <a name="required-administrator-roles"></a>所需的管理员角色


| 业务角色/角色| Azure AD 角色（如有必要） |
| - | - |
| 1 级支持人员| 密码管理员 |
| 2 级支持人员| 用户管理员 |
| SSPR 管理员| 全局管理员 |


### <a name="plan-communications"></a>规划沟通

通信对于任何新服务的成功至关重要。 应主动与用户交流他们的体验将如何更改、何时会更改以及在遇到问题时如何获取支持。 请查看 [Microsoft 下载中心上的自助式密码重置推出材料](https://www.microsoft.com/download/details.aspx?id=56768)，了解有关如何规划最终用户通信策略的建议。

### <a name="plan-a-pilot"></a>规划试点

建议在测试环境中执行 SSPR 的初始配置。 通过为组织中的一部分用户启用 SSPR，开始使用试点组。 请参阅[关于试点的最佳做法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)。

若要创建一个组，请参阅如何[在 Azure Active Directory 中创建组并添加成员](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)。 

## <a name="plan-configuration"></a>规划配置

若要启用 SSPR 并使用推荐值，需要以下设置。

| 区域 | 设置 | 值 |
| --- | --- | --- |
| SSPR 属性 | 已启用自助式密码重置 | “选定”的组用于试点/“全部”用于生产 |
| **身份验证方法** | 注册时所需的身份验证方法 | 始终比重置所需的方法多 1 个 |
|   | 注册时所需的身份验证方法 | 1 个或 2 个 |
| **注册** | 要求用户在登录时注册 | 是 |
|   | 用户必须在几天后重新确认其身份验证信息 | 90 – 180 天 |
| **通知** | 重置密码时通知用户 | 是 |
|   | 当其他管理员重置其密码时通知所有管理员 | 是 |
| **自定义** | 自定义支持人员链接 | 是 |
|   | 自定义支持人员电子邮件或 URL | 支持站点或电子邮件地址 |
| **本地集成** | 将密码写回到本地 AD | 是 |
|   | 允许用户在不重置密码的情况下解锁帐户 | 是 |

### <a name="sspr-properties"></a>SSPR 属性

启用 SSPR 时，在试验环境中选择一个合适的安全组。

* 若要为每个人强制执行 SSPR 注册，建议使用“全部”选项。
* 否则，请选择合适的 Azure AD 或 AD 安全组。

### <a name="authentication-methods"></a>身份验证方法

启用了 SSPR 时，仅当用户在管理员已启用的身份验证方法中输入了数据时，他们才能重置其密码。 方法包括电话、验证器应用通知、安全问题等。有关详细信息，请参阅[什么是身份验证方法？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)。

建议采用以下身份验证方法设置：

* 将“注册时所需的身份验证方法”数量设置为至少比重置所需的数量多一个。 允许多种身份验证方法使用户在需要重置时能够灵活选择和操作。

* 将“重置时所需的方法数”设置为适合你的组织的数量。 如果只设置一种方法，只能提供最低程度的安全保障，设置两种就可能进一步改善安全状况。 

注意：用户需要在 [Azure Active Directory 中的密码策略和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)中配置身份验证方法。

### <a name="registration-settings"></a>注册设置

将“要求用户在登录时注册”设置为“是” 。 此设置要求用户在登录时进行注册，以确保所有用户都受到保护。

将“在要求用户重新确认其身份验证信息之前需要等待的天数”设置在“90”到“180”天之间，除非你的组织因业务需要将其设置为更短的时间范围  。

### <a name="notifications-settings"></a>通知设置

将“在密码重置时通知用户”和“当有管理员重置其密码时通知所有管理员”均设置为“是”  。 将这两项均设置为“是”可以确保用户在其密码被重置时能够知悉，从而提高了安全性。 它还可确保当某个管理员更改密码时，所有管理员均能够知悉。 如果用户或管理员收到通知，且尚未发起更改，他们可以立即报告潜在的安全问题。

### <a name="customization-settings"></a>自定义设置

自定义支持人员电子邮件或 URL 以确保遇到问题的用户可以立即获得帮助，这一点是非常重要的。 将此选项设置为用户熟悉的常见支持人员电子邮件地址或网页。 

有关详细信息，请参阅[自定义用于自助式密码重置的 Azure AD 功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)。

### <a name="password-writeback"></a>密码写回

“密码写回”是使用 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) 启用的功能，可将云中的密码重置实时写回到现有的本地目录。 有关详细信息，请参阅[什么是密码写回？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

建议使用以下设置：

* 确保将“将密码写回到本地 AD”设置为“是” 。 
* 将“允许用户在不重置密码的情况下解锁帐户”设置为“是” 。

默认情况下，Azure AD 在执行密码重置时会解锁帐户。

### <a name="administrator-password-setting"></a>管理员密码设置

管理员帐户具有提升的权限。 本地企业或域管理员无法通过 SSPR 重置其密码。 本地管理员帐户具有以下限制：

* 只能在本地环境中更改其密码。
* 永远不能使用机密问题和答案作为重置密码的方法。

建议不要将本地 Active Directory 管理员帐户与 Azure AD 进行同步。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多个标识管理系统的环境

某些环境具有多个标识管理系统。 本地标识管理器（如 Oracle AM 和 SiteMinder）需要与 AD 同步以获取密码。 为此，可以将密码更改通知服务 (PCNS) 这类工具与 Microsoft Identity Manager (MIM) 配合使用。 若要查找更为复杂的相关方案的信息，请参阅文章[在域控制器上部署 MIM 密码更改通知服务](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)。

## <a name="plan-testing-and-support"></a>规划测试和支持

在部署的每个阶段（从最初的试点组到整个组织范围的部署），确保结果与预期一致。

### <a name="plan-testing"></a>规划测试

若要确保部署按预期方式运作，应规划一组测试用例来验证实现。 若要评估测试用例，需要一个具有密码的非管理员测试用户。 如果需要创建用户，请参阅[向 Azure Active Directory 添加新用户](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

下表包含有用的测试方案，可以使用这些方案根据你的策略来记录组织预期的结果。
<br>


| 业务案例| 预期结果 |
| - | - |
| 可以从企业网络内部访问 SSPR 门户| 由组织决定 |
| 可以从企业网络外部访问 SSPR 门户| 由组织决定 |
| 未为用户启用密码重置时，从浏览器重置用户密码| 用户无法访问密码重置流 |
| 用户未注册密码重置时，从浏览器重置用户密码| 用户无法访问密码重置流 |
| 用户在强制执行了密码重置注册后登录| 提示用户注册安全信息 |
| 用户在密码重置注册完成后登录| 提示用户注册安全信息 |
| 当用户没有许可证时，可以访问 SSPR 门户| 可访问 |
| 从加入了 Windows 10 Azure AD 或加入了混合 Azure AD 的设备的锁定屏幕中重置用户密码| 用户可以重置密码 |
| 管理员可近乎实时地访问 SSPR 注册和使用情况数据| 可通过审核日志获取 |

还可以参阅[完成 Azure AD 自助式密码重置试点推广](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)。 在本教程中，你将在组织中启用 SSPR 的试点推广，并使用非管理员帐户进行测试。

### <a name="plan-support"></a>规划支持

虽然通常不会产生有关 SSPR 的用户问题，但应让支持人员做好准备、能够处理可能出现的问题。 虽然管理员可以通过 Azure AD 门户为最终用户重置密码，但最好是借助自助式支持过程来解决问题。

为了帮助支持团队顺利开展工作，可以基于用户反馈的问题来创建常见问题解答。 以下是一些示例：

| 方案| 说明 |
| - | - |
| 用户无法使用任何已注册的可用身份验证方法| 用户尝试重置其密码，但无法使用任何已注册的可用身份验证方法（例如：他们将手机遗落在家里，从而无法访问电子邮件） |
| 用户未在其办公室电话或手机上收到短信或呼叫| 用户正在尝试通过短信或呼叫来验证其身份，但没有收到短信/呼叫。 |
| 用户无法访问密码重置门户| 用户想要重置其密码，但未启用密码重置，因此无法访问用于更新密码的页面。 |
| 用户无法设置新密码| 用户在密码重置流期间完成了验证，但无法设置新密码。 |
| 用户在 Windows 10 设备上看不到“重置密码”链接| 用户尝试从 Windows 10 锁屏界面重置密码，但设备未加入 Azure AD，或 Intune 设备策略未启用 |

### <a name="plan-rollback"></a>规划回滚

若要回滚部署：

* 对于单个用户，请从安全组中删除用户 

* 对于组，请从 SSPR 配置中删除组

* 对于所有人，禁用 Azure AD 租户的 SSPR

## <a name="deploy-sspr"></a>部署 SSPR

在部署之前，确保已完成以下操作：

1. 已创建并开始执行[通信计划](#plan-communications)。

1. 已确定合适的[配置设置](#plan-configuration)。

1. 已分别确定[试点](#plan-a-pilot)和生产环境中的用户和组。

1. 对于注册和自助流程，[已确定配置设置](#plan-configuration)。

1. 如果是混合环境，[已配置密码写回](#password-writeback)。


**现在可以开始部署 SSPR 了！**

请参阅[启用自助式密码重置](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset)，获取有关配置以下区域的完整分步指导。

1. [身份验证方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [注册设置](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [通知设置](#notifications-settings)

1. [自定义设置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [本地集成](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>在 Windows 中启用 SSPR
对于运行 Windows 7、8、8.1、10 的计算机，可让[用户在 Windows 登录屏幕上重置其密码](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>管理 SSPR

Azure AD 可以通过审核和报告，提供更多有关 SSPR 性能的信息。

### <a name="password-management-activity-reports"></a>密码管理活动报告 

可以使用 Azure 门户上的预建报表来度量 SSPR 性能。 如果有相应的授权，还可以创建自定义查询。 有关详细信息，请参阅[用于 Azure AD 密码管理的报告选项](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  你必须是[全局管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)，并且必须选择为你的组织收集这些数据。 要做出此选择，必须至少访问一次 Azure 门户上的“报告”选项卡或审核日志。 在此之前，系统不会为你的组织收集数据。

注册和密码重置的审核日志可保留 30 天。 如果公司内部的安全审核要求保留更长的期限，需要将日志导出到 SIEM 工具（如 [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)、Splunk 或 ArcSight）中使用。

![SSPR 报表屏幕快照](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>身份验证方法 - 使用情况和见解

借助[使用情况和见解](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)可以了解适用于 Azure MFA 和 SSPR 等功能的身份验证方法在你的组织中是如何运作的。 此报表功能为你的组织提供了一种方法，使其能够了解需要注册哪些身份验证方法以及如何使用这些方法。

### <a name="troubleshoot"></a>疑难解答

* 请参阅[排查自助式密码重置问题](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* 请根据[密码管理常见问题](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq)中的说明操作 

### <a name="helpful-documentation"></a>可提供帮助的文档

* [有哪些身份验证方法？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [工作原理：Azure AD 自助式密码重置？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [自定义用于自助式密码重置的 Azure AD 功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Azure Active Directory 中的密码策略和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [什么是密码写回？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>后续步骤

* 若要开始部署 SSPR，请参阅[启用 Azure AD 自助式密码重置](tutorial-enable-sspr.md)

* [考虑实施 Azure AD 密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [考虑实施 Azure AD 智能锁定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)
