---
title: 自助服务密码重置部署-Azure Active Directory
description: 成功实现 Azure AD 自助服务密码重置的策略
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
ms.openlocfilehash: 785a8a031a10232a37b235711ba919fdc1df35d3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061399"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset"></a>规划 Azure Active Directory 自助服务密码重置

> [!NOTE]
> 此部署计划提供部署 Azure AD 自助服务密码重置（SSPR）的规划指南和最佳实践。 <br>**如果你正在寻找 SSPR 工具以返回到你的帐户，请转到[https://aka.ms/sspr](https://aka.ms/sspr)** 。

[自助服务密码重置（SSPR）](https://www.youtube.com/watch?v=tnb2Qf4hTP8)是一项 AZURE ACTIVE DIRECTORY （AD）功能，使用户能够重置其密码，而无需联系 IT 人员获取帮助。 用户可以快速取消阻止自己，而不管他们在哪一天或一天时间，都可以继续工作。 通过允许员工取消阻止其自身，你的组织可以降低大多数常见密码相关问题的非生产时间和高支持成本。 

SSPR 具有以下关键功能：

* 自助服务允许最终用户重置其过期或未过期的密码，而无需联系管理员或支持人员提供支持。

* [密码写回](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)功能允许管理本地密码，并通过云解析帐户锁定。

* 密码管理活动报告允许管理员深入了解在其组织中发生的密码重置和注册活动。

## <a name="learn-about-sspr"></a>了解 SSPR

了解有关 SSPR 的详细信息。 了解[其工作原理： Azure AD 的自助密码重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)。

### <a name="key-benefits"></a>主要优点

启用 SSPR 的主要优势包括：

* **管理成本**。 SSPR 使用户能够自行重置密码，从而降低了 IT 支持成本。 它还减少了因密码丢失和锁定而丢失的时间成本。 

* **直观的用户体验**。 它提供了一个直观的一次性用户注册过程，允许用户从任何设备或位置按需重置密码和取消阻止帐户。 SSPR 使用户能够更快地工作并提高工作效率。

* **灵活性和安全性**。 SSPR 使企业能够访问云平台提供的安全性和灵活性。 管理员可以更改设置以适应新的安全要求，并在用户无需中断登录的情况下将更改滚动到用户。

* **可靠的审核和使用情况跟踪**。 组织可以确保业务系统在用户重置其自己的密码时保持安全。 可靠的审核日志包括密码重置过程中每个步骤的信息。 可以通过 API 使用这些日志，并使用户能够将数据导入到所选的安全事件和事件监视（SIEM）系统中。

### <a name="licensing"></a>许可

Azure Active Directory 按用户授予许可，每个用户都需要适当的许可证才能使用他们所使用的功能。 建议对 SSPR 进行基于组的许可。 

若要比较版本和功能并启用组或基于用户的许可，请参阅[Azure AD 自助服务密码重置的许可要求](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)。

有关定价的详细信息，请参阅[定价 Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)。

### <a name="prerequisites"></a>先决条件

* 一个至少启用了试用版许可证的有效 Azure AD 租户。 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 一个具有全局管理员权限的帐户。


### <a name="training-resources"></a>定型资源

| 资源| 链接和说明 |
| - | - |
| 视频| [为用户提供更好的可伸缩性](https://youtu.be/g9RpRnylxS8) 
| |[什么是自助服务密码重置？](https://youtu.be/hc97Yx5PJiM)|
| |[部署自助服务密码重置](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[如何为 Azure AD 中的用户配置自助服务密码重置？](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[如何 [准备用户到] 注册 Azure Active Directory 的安全信息](https://youtu.be/gXuh0XS18wA) |
| 联机课程|[管理 Microsoft Azure Active Directory 中的标识](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities)使用 SSPR 为用户指定新式、受保护的体验。 请参阅 "[管理 Azure Active Directory 用户和组](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)" 模块。 |
|Pluralsight 付费课程 |[身份标识和访问管理的问题](https://www.pluralsight.com/courses/identity-access-management-issues)了解要在你的组织中了解的 IAM 和安全问题。 另请参阅 "其他身份验证方法" 模块。|
| |[通过 Microsoft 企业移动性套件入门](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started)了解将本地资产扩展到云的最佳实践，以允许进行身份验证、授权、加密和安全移动体验的方式。 另请参阅 "配置 Microsoft Azure Active Directory Premium 的高级功能" 模块。
|教程 |[完成 Azure AD 自助服务密码重置试验推出](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[启用密码写回](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[从 Windows 10 登录屏幕 Azure AD 密码重置](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| 常见问题|[密码管理常见问题](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>解决方案体系结构

下面的示例介绍常见混合环境的密码重置解决方案体系结构。

![解决方案体系结构示意图](./media/howto-sspr-deployment//solutions-architecture.png)

工作流的说明

若要重置密码，用户需要切换到[密码重置门户](https://aka.ms/sspr)。 它们必须验证以前注册的身份验证方法或方法来证明其身份。 如果用户成功重置了密码，则会开始重置过程。

* 对于仅限云的用户，SSPR 会将新密码存储在 Azure AD 中。 

* 对于混合用户，SSPR 通过 Azure AD Connect 服务将密码写回到本地 Active Directory。 

注意：对于禁用了[密码哈希同步（PHS）](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs)的用户，SSPR 仅将密码存储在本地 Active Directory 中。

### <a name="best-practices"></a>最佳做法

你可以通过在组织中部署 SSPR 和其他常用的应用程序或服务，帮助用户快速注册。 此操作会生成大量的登录，并会推动注册操作。

在部署 SSPR 之前，可以选择确定每个密码重置调用的数量和平均成本。 您可以使用此数据发布部署来显示 SSPR 为组织带来的价值。

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>启用 SSPR 和 MFA 的组合注册

Microsoft 建议组织启用 SSPR 和多重身份验证的组合注册体验。 启用此组合注册体验后，用户只需选择其注册信息一次即可启用这两种功能。

组合注册体验不需要组织同时启用 SSPR 和 Azure 多重身份验证。 组合注册为组织提供了更好的用户体验。 有关详细信息，请参阅[结合安全信息注册（预览）](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>规划部署项目

在环境中确定此部署的策略时，请考虑你的组织需求。

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，它们通常是由于对影响、结果和责任的预期不匹配而导致的。 若要避免这些问题，请[确保您的利益干系人参与](https://aka.ms/deploymentplans)到项目中，并通过记录利益干系人及其项目输入和责任来充分了解项目中的利益干系人角色。

#### <a name="required-administrator-roles"></a>所需的管理员角色


| 业务角色/角色| Azure AD 角色（如有必要） |
| - | - |
| 1级支持人员| 密码管理员 |
| 2级支持| 用户管理员 |
| SSPR 管理员| 全局管理员 |


### <a name="plan-communications"></a>规划沟通

通信对于任何新服务的成功至关重要。 你应主动与用户进行交流，了解他们的体验将如何更改，何时发生更改，以及在遇到问题时如何获取支持。 请参阅[Microsoft 下载中心上的自助服务密码重置推广资料](https://www.microsoft.com/download/details.aspx?id=56768)，了解有关如何规划最终用户通信策略的建议。

### <a name="plan-a-pilot"></a>规划试点

建议在测试环境中使用 SSPR 的初始配置。 通过为组织中的一部分用户启用 SSPR，开始使用试点组。 请参阅[试验的最佳实践](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)。

若要创建组，请参阅如何[在 Azure Active Directory 中创建组并添加成员](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)。 

## <a name="plan-configuration"></a>计划配置

启用 SSPR 和推荐值需要以下设置。

| 区域 | 设置 | 值 |
| --- | --- | --- |
| **SSPR 属性** | 已启用自助密码重置 | 用于生产的试点/**全部**组的**所选**组 |
| **身份验证方法** | 注册所需的身份验证方法 | 重置所需的始终为1 |
|   | 重置所需的身份验证方法 | 1 个或 2 个 |
| **注册** | 要求用户在登录时注册 | 是 |
|   | 用户必须在几天后重新确认其身份验证信息 | 90–180天 |
| **通知** | 重置密码时通知用户 | 是 |
|   | 当其他管理员重置其密码时通知所有管理员 | 是 |
| **自定义** | 自定义支持人员链接 | 是 |
|   | 自定义支持人员电子邮件或 URL | 支持站点或电子邮件地址 |
| **本地集成** | 将密码写回到本地 AD | 是 |
|   | 允许用户在不重置密码的情况下解锁帐户 | 是 |

### <a name="sspr-properties"></a>SSPR 属性

启用 SSPR 时，在试验环境中选择适当的安全组。

* 若要为每个人强制 SSPR 注册，建议使用**All**选项。
* 否则，请选择相应的 Azure AD 或 AD 安全组。

### <a name="authentication-methods"></a>身份验证方法

当启用 SSPR 时，用户只能重置其密码（如果他们在管理员已启用的身份验证方法中存在数据）。 方法包括电话、验证器应用通知、安全问题等。有关详细信息，请参阅[什么是身份验证方法？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)。

建议采用以下身份验证方法设置：

* 将**所需的身份验证方法设置为至少注册**到重置所需的数量。 允许多次身份验证可让用户在需要重置时提供灵活性。

* 设置重置为适用于组织的级别**所需的方法数**。 其中一种要求最小的摩擦，而两个可能会增加您的安全状况。 

注意：用户必须在[Azure Active Directory 的密码策略和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)中配置身份验证方法。

### <a name="registration-settings"></a>注册设置

设置 "**要求用户在登录时注册** **"** 。 此设置要求用户在登录时注册，以确保所有用户都受到保护。

设置在**要求用户重新确认其身份验证信息**在**90**到**180**天之间的天数，除非您的组织有业务需要更短的时间范围。

### <a name="notifications-settings"></a>通知设置

同时配置在**密码重置时通知用户**和在**其他管理员将密码重置**为 **"是**" 时通知所有管理员。 选择 **"是**" 会通过确保用户在密码重置时知道用户是否知道，提高安全性。 它还可确保管理员在更改密码时知道所有管理员。 如果用户或管理员收到通知，而他们尚未启动更改，则他们可以立即报告潜在的安全问题。

### <a name="customization-settings"></a>自定义设置

自定义支持人员电子邮件或 URL 以确保遇到问题的用户可以立即获得帮助，这一点很重要。 将此选项设置为用户熟悉的常见支持人员电子邮件地址或网页。 

有关详细信息，请参阅[自定义自助服务密码重置的 Azure AD 功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)。

### <a name="password-writeback"></a>密码写回

使用[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity)启用**密码写回**，并实时将云中的密码重置写回现有的本地目录。 有关详细信息，请参阅[什么是密码写回？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

建议采用以下设置：

* 确保 **"将密码写回到本地 AD** " 设置为 **"是"** 。 
* 将 "**允许用户解除帐户锁定而不重置密码**" 设置为 **"是"** 。

默认情况下，Azure AD 在执行密码重置时会解锁帐户。

### <a name="administrator-password-setting"></a>管理员密码设置

管理员帐户具有提升的权限。 本地企业或域管理员无法通过 SSPR 重置其密码。 本地管理员帐户具有以下限制：

* 只能在本地环境中更改其密码。
* 永远不能使用机密问题和答案作为重置密码的方法。

建议你不要将本地 Active Directory 管理员帐户与 Azure AD 同步。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多个标识管理系统的环境

某些环境有多个标识管理系统。 Oracle AM 和 SiteMinder 等本地标识管理器需要与 AD 同步以获取密码。 为此，可以使用密码更改通知服务（PCNS）之类的工具与 Microsoft Identity Manager （MIM）。 若要查找有关这种更为复杂的情况的信息，请参阅文章在[域控制器上部署 MIM 密码更改通知服务](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)。

## <a name="plan-testing-and-support"></a>规划测试和支持

在部署的每个阶段，从整个组织范围内的初始试点组，确保结果与预期相同。

### <a name="plan-testing"></a>规划测试

若要确保部署按预期方式工作，请计划一组测试用例来验证实现。 若要评估测试用例，需要一个具有密码的非管理员测试用户。 如果需要创建用户，请参阅[将新用户添加到 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)。

下表包含有用的测试方案，你可以使用这些方案根据你的策略来记录组织预期的结果。
<br>


| 业务案例| 预期结果 |
| - | - |
| 可从企业网络内部访问 SSPR 门户| 由你的组织决定 |
| 可以从企业网络外部访问 SSPR 门户| 由你的组织决定 |
| 用户未启用密码重置时，从浏览器重置用户密码| 用户无法访问密码重置流 |
| 用户未注册密码重置时，从浏览器重置用户密码| 用户无法访问密码重置流 |
| 当强制执行密码重置注册时，用户登录| 提示用户注册安全信息 |
| 密码重置注册完成后用户登录| 提示用户注册安全信息 |
| 当用户没有许可证时，可以访问 SSPR 门户| 可访问 |
| Azure AD 联接或混合 Azure AD 已加入设备锁定屏幕重置 Windows 10 中的用户密码| 用户可以重置密码 |
| SSPR 注册和使用情况数据将以近乎实时的速度向管理员提供| 可通过审核日志获取 |

你还可以参阅[完成 Azure AD 自助服务密码重置试验滚动](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)。 在本教程中，你将在组织中启用 SSPR 的试点，并使用非管理员帐户进行测试。

### <a name="plan-support"></a>计划支持

尽管 SSPR 通常不会创建用户问题，但必须准备支持人员来处理可能出现的问题。 虽然管理员可以通过 Azure AD 门户为最终用户重置密码，但最好是通过自助服务支持过程来帮助解决问题。

为了使支持团队取得成功，你可以基于你从用户处收到的问题创建常见问题解答。 以下是一些示例：

| 方案| 说明 |
| - | - |
| 用户未提供任何已注册的身份验证方法| 用户尝试重置其密码，但没有任何注册的可用身份验证方法（例如：他们在家里离开了手机，无法访问电子邮件） |
| 用户在其办公室或手机上未收到文本或呼叫| 用户正在尝试通过短信或呼叫来验证其身份，但没有收到文本/调用。 |
| 用户无法访问密码重置门户| 用户想要重置其密码，但未启用密码重置，并且无法访问该页面来更新密码。 |
| 用户无法设置新密码| 用户在密码重置流期间完成验证，但不能设置新密码。 |
| 用户在 Windows 10 设备上看不到 "重置密码" 链接| 用户尝试从 Windows 10 锁屏界面重置密码，但设备未加入 Azure AD，或者 Intune 设备策略未启用 |

### <a name="plan-roll-back"></a>计划回滚

若要回滚部署：

* 对于单个用户，请从安全组中删除该用户 

* 对于组，请从 SSPR 配置中删除组

* 对于所有人，禁用 Azure AD 租户的 SSPR

## <a name="deploy-sspr"></a>部署 SSPR

在部署之前，请确保已完成以下操作：

1. 已创建并已开始执行你的[通信计划](#plan-communications)。

1. 确定相应的[配置设置](#plan-configuration)。

1. 确定用于[试点](#plan-a-pilot)和生产环境的用户和组。

1. 确定注册和自助服务的[配置设置](#plan-configuration)。

1. 如果你有混合环境，则[配置了密码写回](#password-writeback)。


**你现在已准备好部署 SSPR！**

请参阅[启用自助密码重置](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset)，获取有关配置以下各区域的完整分步说明。

1. [身份验证方法](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [注册设置](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [通知设置](#notifications-settings)

1. [自定义设置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [本地集成](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>在 Windows 中启用 SSPR
对于运行 Windows 7、8、8.1 和10的计算机，你可以[允许用户在 Windows 登录屏幕上重置其密码](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>管理 SSPR

Azure AD 可以通过审核和报告提供 SSPR 性能的附加信息。

### <a name="password-management-activity-reports"></a>密码管理活动报告 

您可以使用 Azure 门户上的预建报表来度量 SSPR 性能。 如果有相应的授权，还可以创建自定义查询。 有关详细信息，请参阅[Azure AD 密码管理的报告选项](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)

> [!NOTE]
>  你必须是[全局管理员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)，并且你必须选择将此数据收集到你的组织。 若要选择加入，你必须至少访问 Azure 门户上的 "报表" 选项卡或审核日志。 在此之前，数据不会为你的组织收集。

注册和密码重置的审核日志适用于30天。 如果公司内的安全审核需要更长的保留期，则需要将日志导出并使用 SIEM 工具，如[Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)、Splunk 或 ArcSight。

![SSPR 报告屏幕快照](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>身份验证方法-使用情况和见解

[使用情况和见解](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)使你能够了解 Azure MFA 和 SSPR 等功能的身份验证方法在你的组织中的工作方式。 此报告功能为您的组织提供了了解注册哪些方法以及如何使用这些方法的方法。

### <a name="troubleshoot"></a>故障排除

* 请参阅[排除自助服务密码重置问题](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* 跟踪[密码管理常见问题](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>帮助文档

* [有哪些身份验证方法？](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [工作原理： Azure AD 自助服务密码重置？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [自定义自助服务密码重置的 Azure AD 功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Azure Active Directory 中的密码策略和限制](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [什么是密码写回？](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>后续步骤

* 若要开始部署 SSPR，请参阅[完成 Azure AD 自助服务密码重置试验推出](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)

* [考虑实施 Azure AD 密码保护](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [考虑实现 Azure AD 智能锁定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)