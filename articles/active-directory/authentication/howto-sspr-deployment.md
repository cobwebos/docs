---
title: Azure 活动目录自助服务密码重置的部署注意事项
description: 了解成功实现 Azure AD 自助服务密码重置的部署注意事项和策略
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
ms.openlocfilehash: c11521ec074b63843b873c39102b68bf185d2821
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676730"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>规划 Azure 活动目录自助密码重置部署

> [!IMPORTANT]
> 此部署计划提供了部署 Azure AD 自助服务密码重置 （SSPR） 的指南和最佳实践。
>
> **如果您是最终用户，需要返回您的帐户，请转到[https://aka.ms/sspr](https://aka.ms/sspr)**。

[自助服务密码重置 （SSPR）](https://www.youtube.com/watch?v=tnb2Qf4hTP8)是 Azure 活动目录 （AD） 功能，使用户能够重置其密码，而无需联系 IT 人员寻求帮助。 用户可以快速解除自我阻止，并继续工作，无论他们在哪里或一天的时间。 通过允许员工自行解除阻止，您的组织可以减少大多数常见与密码相关的问题的非生产时间和高支持成本。

SSPR 具有以下关键功能：

* 自助服务允许最终用户重置其过期或未过期的密码，而无需联系管理员或帮助台寻求支持。
* [密码回写](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)允许管理本地密码，并解决通过云的帐户锁定问题。
* 密码管理活动报告使管理员能够深入了解其组织中发生的密码重置和注册活动。

此部署指南演示如何规划然后测试 SSPR 推出。

要快速查看 SSPR 的操作，然后回来了解其他部署注意事项：

> [!div class="nextstepaction"]
> [启用自助服务密码重置 （SSPR）](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>了解 SSPR

了解有关 SSPR 的更多详细信息。 请参阅[其工作原理：Azure AD 自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)。

### <a name="key-benefits"></a>主要优点

启用 SSPR 的主要好处是：

* **管理成本**。 SSPR 允许用户自行重置密码，从而降低 IT 支持成本。 它还减少了由于密码丢失和锁定而损失的时间成本。 

* **直观的用户体验**。 它提供了直观的一次性用户注册过程，允许用户重置密码，并取消阻止任何设备或位置的帐户。 SSPR 允许用户更快地恢复工作并提高工作效率。

* **灵活性和安全性**。 SSPR 使企业能够访问云平台提供的安全性和灵活性。 管理员可以更改设置以适应新的安全要求，并在不中断其登录的情况下向用户推出这些更改。

* **强大的审核和使用跟踪**。 组织可以确保业务系统在用户重置自己的密码时保持安全。 可靠的审核日志包括密码重置过程每个步骤的信息。 这些日志可从 API 获得，并使用户能够将数据导入首选的安全事件和事件监视 （SIEM） 系统。

### <a name="licensing"></a>授权

Azure 活动目录是每个用户的许可，这意味着每个用户都需要一个适当的许可证来用于他们使用的功能。 我们建议基于组的许可进行 SSPR。 

要比较版本和功能并启用基于组或用户的许可，请参阅[Azure AD 自助服务密码重置的许可要求](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)。

有关定价的详细信息，请参阅[Azure 活动目录定价](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites"></a>先决条件

* 一个至少启用了试用版许可证的有效 Azure AD 租户。 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个具有全局管理员权限的帐户。


### <a name="training-resources"></a>培训资源

| 资源| 链接和描述 |
| - | - |
| 视频| [为您的用户提供更好的 IT 可扩展性](https://youtu.be/g9RpRnylxS8) 
| |[什么是自助服务密码重置？](https://youtu.be/hc97Yx5PJiM)|
| |[部署自助服务密码重置](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[如何为 Azure AD 中的用户配置自助服务密码重置？](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[如何 [准备用户] 注册 Azure 活动目录的安全信息](https://youtu.be/gXuh0XS18wA) |
| 在线课程|[管理 Microsoft Azure 活动目录中的标识](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities)使用 SSPR 为用户提供现代、受保护的体验。 请特别查看"[管理 Azure 活动目录用户和组](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)"模块。 |
|复数付费课程 |[身份和访问管理问题](https://www.pluralsight.com/courses/identity-access-management-issues)了解组织中需要注意的 IAM 和安全问题。 请参阅"其他身份验证方法"模块。|
| |[开始使用 Microsoft 企业移动套件](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started)了解以允许身份验证、授权、加密和安全移动体验的方式将本地资产扩展到云的最佳做法。 请特别查看"配置 Microsoft Azure 活动目录高级版的高级功能"模块。
|教程 |[完成 Azure AD 自助密码重置试点推出](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[启用密码写回](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Azure AD 密码从 Windows 10 的登录屏幕重置](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| 常见问题解答|[密码管理常见问题](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>解决方案体系结构

下面的示例描述了常见混合环境的密码重置解决方案体系结构。

![解决方案体系结构图](./media/howto-sspr-deployment//solutions-architecture.png)

工作流描述

要重置密码，用户转到[密码重置门户](https://aka.ms/sspr)。 他们必须验证以前注册的身份验证方法或方法，以证明其身份。 如果他们成功重置密码，则开始重置过程。

* 对于仅限云的用户，SSPR 将新密码存储在 Azure AD 中。 

* 对于混合用户，SSPR 通过 Azure AD 连接服务将密码写回预运行目录。 

注意：对于禁用[密码哈希同步 （PHS）](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs)的用户，SSPR 仅将密码存储在打开状态的活动目录中。

### <a name="best-practices"></a>最佳做法

通过将 SSPR 与组织中的另一个常用应用程序或服务一起部署，可以帮助用户快速注册。 此操作会产生大量的登录，并推动注册过程。

在部署 SSPR 之前，您可以选择确定每个密码重置调用的数量和平均成本。 您可以使用此数据后部署来显示 SSPR 给组织带来的值。

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>启用 SSPR 和 MFA 的组合注册

Microsoft 建议组织启用 SSPR 和多重身份验证的组合注册体验。 启用此合并注册体验时，用户只需选择一次注册信息，即启用这两种功能。

组合注册体验不需要组织同时启用 SSPR 和 Azure 多重身份验证。 合并注册为组织提供更好的用户体验。 有关详细信息，请参阅[组合安全信息注册（预览）](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>规划部署项目

在确定环境中此部署的策略时，请考虑组织需求。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，失败的原因往往是对影响、结果和责任的预期不符。 为了避免这些陷阱，[确保您与合适的利益相关者接触](https://aka.ms/deploymentplans)，并通过记录利益相关者及其项目投入和问责制，充分理解项目中的利益相关者角色。

#### <a name="required-administrator-roles"></a>所需的管理员角色


| 业务角色/人物| Azure AD 角色（如果需要） |
| - | - |
| 1 级帮助台| 密码管理员 |
| 2 级帮助台| 用户管理员 |
| SSPR 管理员| 全局管理员 |


### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功至关重要。 您应该主动与用户沟通他们的体验将如何改变、何时更改，以及如果他们遇到问题，如何获得支持。 有关如何规划最终用户沟通策略的思路，请参阅 [Microsoft 下载中心上的自助式密码重置推广材料](https://www.microsoft.com/download/details.aspx?id=56768)。

### <a name="plan-a-pilot"></a>规划试点

我们建议 SSPR 的初始配置位于测试环境中。 通过为组织中的用户子集启用 SSPR，从试验组开始。 请参阅[飞行员的最佳做法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)。

要创建组，请参阅如何在[Azure 活动目录中创建组和添加成员](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)。 

## <a name="plan-configuration"></a>计划配置

下面是启用 SSPR 所需的设置及其建议值。

| 区域 | 设置 | 值 |
| --- | --- | --- |
| **SSPR 属性** | 已启用自助式密码重置 | 在试运行环境中为“选定组”/在生产环境中为“全部”******** |
| **身份验证方法** | 注册所需的身份验证方法数 | 至少比重置所需的数目多 1 个 |
|   | 重置所需的身份验证方法数 | 1 个或 2 个 |
| **注册** | 要求用户在登录时注册 | 是 |
|   | 用户必须在几天后重新确认其身份验证信息 | 90 ~ 180 天 |
| **通知** | 重置密码时通知用户 | 是 |
|   | 当其他管理员重置其密码时通知所有管理员 | 是 |
| **自定义** | 自定义服务台链接 | 是 |
|   | 自定义服务台电子邮件或 URL | 支持站点或电子邮件地址 |
| **本地集成** | 将密码写回到本地 AD | 是 |
|   | 允许用户在不重置密码的情况下解锁帐户 | 是 |

### <a name="sspr-properties"></a>SSPR 属性

启用 SSPR 时，在试验环境中选择适当的安全组。

* 要为每个人强制实施 SSPR 注册，我们建议使用 **"全部"** 选项。
* 否则，请选择相应的 Azure AD 或 AD 安全组。

### <a name="authentication-methods"></a>身份验证方法

启用 SSPR 后，用户仅当管理员启用的身份验证方法中存在数据时，才能重置其密码。 方法包括电话、身份验证器应用通知、安全问题等。有关详细信息，请参阅[什么是身份验证方法？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

我们建议使用以下身份验证方法设置：

* 将**注册所需的身份验证方法**设置为至少比重置所需的数量多一个。 允许用户在需要重置时灵活进行身份验证。

* 将“重置所需的方法数”设置为适合组织的级别。**** 使用 1 个可以尽量消除不便，但使用 2 个可以改善安全态势。 

注意： 用户必须在[Azure 活动目录中的密码策略和限制中](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)配置身份验证方法。

### <a name="registration-settings"></a>注册设置

将“要求用户在登录时注册”设置为“是”。******** 此设置要求用户在登录时进行注册，以确保所有用户都受到保护。

设置**要求用户重新确认其身份验证信息的天数**在**90**到**180**天之间，除非您的组织需要更短的时间范围。

### <a name="notifications-settings"></a>通知设置

将“重置密码时通知用户”和“当其他管理员重置其密码时通知所有管理员”都设置为“是”。************ 通过确保用户知道重置密码时，在这两个上选择 **"是**"可提高安全性。 它还确保所有管理员在管理员更改密码时都了解。 如果用户或管理员收到通知，但他们还没有启动更改，他们可以立即报告潜在的安全问题。

### <a name="customization-settings"></a>自定义设置

自定义帮助台电子邮件或 URL 以确保遇到问题的用户能够立即获得帮助，这一点至关重要。 请将此选项设置为用户熟悉的常见服务台电子邮件地址或网页。 

有关详细信息，请参阅自定义[Azure AD 功能以进行自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)。

### <a name="password-writeback"></a>密码写回

使用 Azure AD[连接](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)启用**密码回写**，并在云中实时将密码重置写入现有本地目录。 有关详细信息，请参阅[什么是密码回写？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

我们建议使用以下设置：

* 确保**将密码写回本地 AD**设置为 **"是**"。 
* 将 **"允许用户解锁帐户而不重置密码**为**是**"。

默认情况下，Azure AD 在执行密码重置时会解锁帐户。

### <a name="administrator-password-setting"></a>管理员密码设置

管理员帐户具有提升的权限。 本地企业或域管理员无法通过 SSPR 重置其密码。 本地管理员帐户具有以下限制：

* 只能在其预置环境中更改其密码。
* 永远不能使用秘密问题和答案作为重置密码的方法。

我们建议您不要将上置活动目录管理员帐户与 Azure AD 同步。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多个标识管理系统的环境

某些环境具有多个标识管理系统。 本地标识管理器（如 Oracle AM 和 SiteMinder）需要与 AD 同步才能获得密码。 您可以使用 Microsoft 标识管理器 （MIM） 的密码更改通知服务 （PCNS） 等工具执行此操作。 若要查找有关此类较复杂方案的信息，请参阅[在域控制器上部署 MIM 密码更改通知服务](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)一文。

## <a name="plan-testing-and-support"></a>计划测试和支持

在从初始试验组到全组织的部署每个阶段，确保结果按预期进行。

### <a name="plan-testing"></a>计划测试

为确保部署按预期工作，请规划一组测试用例以验证实现。 要评估测试用例，您需要一个带密码的非管理员测试用户。 如果需要创建用户，请参阅[将新用户添加到 Azure 活动目录](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

下表包括有用的测试方案，可用于根据策略记录组织的预期结果。
<br>


| 业务案例| 预期结果 |
| - | - |
| 可从企业网络内部访问 SSPR 门户| 由组织确定 |
| 可从企业网络外部访问 SSPR 门户| 由组织确定 |
| 未为用户启用密码重置时从浏览器重置用户密码| 用户无法访问密码重置流 |
| 用户未注册密码重置时从浏览器重置用户密码| 用户无法访问密码重置流 |
| 用户登录时强制执行进行密码重置注册| 提示用户注册安全信息 |
| 密码重置注册完成后用户登录| 提示用户注册安全信息 |
| 当用户没有许可证时可以访问 SSPR 门户| 可访问 |
| 从 Windows 10 Azure AD 联接或混合 Azure AD 加入设备锁定屏幕重置用户密码| 用户可以重置密码 |
| 管理员可以近实时地使用 SSPR 注册和使用情况数据| 可通过审核日志使用 |

您还可以参考[完成 Azure AD 自助服务密码重置试验卷](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)。 在本教程中，您将在组织中启用 SSPR 的试点推出，并使用非管理员帐户进行测试。

### <a name="plan-support"></a>计划支持

虽然 SSPR 通常不会造成用户问题，但重要的是让支持人员做好准备，以处理可能出现的问题。 虽然管理员可以通过 Azure AD 门户重置最终用户的密码，但最好通过自助服务支持过程帮助解决问题。

要使支持团队取得成功，您可以根据从用户收到的问题创建常见问题解答。 以下是一些示例：

| 方案| 说明 |
| - | - |
| 用户没有任何可用的注册身份验证方法| 用户尝试重置其密码，但没有他们注册的任何身份验证方法可用（例如：他们离开手机在家，无法访问电子邮件） |
| 用户在办公室或手机上未收到短信或呼叫| 用户尝试通过文本或呼叫验证其身份，但未收到文本/呼叫。 |
| 用户无法访问密码重置门户| 用户希望重置其密码，但未启用密码重置功能，并且无法访问页面以更新密码。 |
| 用户无法设置新密码| 用户在密码重置流期间完成验证，但无法设置新密码。 |
| 用户在 Windows 10 设备上看不到重置密码链接| 用户尝试从 Windows 10 锁屏界面重置密码，但设备未加入 Azure AD，或者未启用 Intune 设备策略 |

### <a name="plan-rollback"></a>计划回滚

要回滚部署：

* 对于单个用户，请从安全组中删除该用户 

* 对于组，从 SSPR 配置中删除组

* 对于所有人，禁用 Azure AD 租户的 SSPR

## <a name="deploy-sspr"></a>部署 SSPR

在部署之前，请确保已完成以下操作：

1. 创建并开始执行您的[沟通计划](#plan-communications)。

1. 确定了适当的[配置设置](#plan-configuration)。

1. 标识[了试验](#plan-a-pilot)环境和生产环境的用户和组。

1. [已确定的](#plan-configuration)注册和自助服务配置设置。

1. 如果混合环境配置了[密码写回](#password-writeback)。


**您现在已准备好部署 SSPR！**

有关配置以下区域的完整分步说明，请参阅[启用自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset)。

1. [身份验证方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [注册设置](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [通知设置](#notifications-settings)

1. [自定义设置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [本地集成](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>在 Windows 中启用 SSPR
对于运行 Windows 7、8、8.1 和 10 的计算机，您可以[允许用户在 Windows 登录屏幕重置其密码](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>管理 SSPR

Azure AD 可以通过审核和报告提供有关 SSPR 性能的其他信息。

### <a name="password-management-activity-reports"></a>密码管理活动报告 

您可以在 Azure 门户上使用预构建的报表来测量 SSPR 性能。 如果有相应的授权，还可以创建自定义查询。 有关详细信息，请参阅[Azure AD 密码管理的报告选项](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  您必须[是全局管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)，并且必须选择加入才能为您的组织收集此数据。 要选择加入，必须至少访问 Azure 门户上的"报告"选项卡或审核日志一次。 在此之前，不会为您的组织收集数据。

注册和密码重置的审核日志可供使用 30 天。 如果公司内的安全审核需要更长的保留时间，则需要将日志导出并消耗到 SIEM 工具中，如 Azure Sentinel、Splunk 或 ArcSight。 [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)

![SSPR 报告屏幕截图](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>身份验证方法 - 使用和见解

[使用和见解](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)使您能够了解 Azure MFA 和 SSPR 等功能的身份验证方法在组织中的工作方式。 此报告功能为您的组织提供了了解注册哪些方法以及如何使用它们的方法的方法。

### <a name="troubleshoot"></a>疑难解答

* 请参阅[故障排除自助服务密码重置](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* 遵循[密码管理常见问题](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>有用的文档

* [有哪些身份验证方法？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [工作原理：Azure AD 自助服务密码重置？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [为自助密码重置自定义 Azure AD 功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Azure Active Directory 中的密码策略和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [什么是密码写回？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>后续步骤

* 要开始部署 SSPR，请参阅[启用 Azure AD 自助服务密码重置](tutorial-enable-sspr.md)

* [考虑实施 Azure AD 密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [考虑实现 Azure AD 智能锁定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)