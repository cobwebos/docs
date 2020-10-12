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
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9a8f572189afaa726f7e01f5e0bbb73340face8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89657222"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>规划 Azure Active Directory 自助式密码重置部署

> [!IMPORTANT]
> 本部署计划提供有关部署 Azure AD 自助式密码重置 (SSPR) 的指导和最佳做法。
>
> **如果你是最终用户并且需要返回到你的帐户，请转到 [https://aka.ms/sspr](https://aka.ms/sspr)** 。

[自助式密码重置 (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) 是一项 Azure Active Directory (AD) 功能，使用户无需联系 IT 人员寻求帮助即可重置密码。 无论何时何地，用户都可以快速解锁帐户并继续工作。 由于员工可以自行解锁，组织可以降低大多数常见密码相关问题造成的非产出时间和较高的支持成本。

SSPR 提供以下重要功能：

* 自助式服务可让最终用户重置其过期或未过期的密码，而无需请求管理员或帮助台提供支持。
* [密码写回](./concept-sspr-writeback.md)可以通过云管理本地密码以及解决帐户锁定问题。
* 借助密码管理活动报告，管理员能够深入了解发生在其组织中的密码重置和注册活动。

本部署指南展示了如何规划并测试 SSPR 推出策略。

若要在实际操作中快速了解 SSPR，然后再返回来了解其他部署注意事项，请执行以下操作：

> [!div class="nextstepaction"]
> [启用自助式密码重置 (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>了解 SSPR

详细了解 SSPR。 参阅[工作原理：Azure AD 自助式密码重置](./concept-sspr-howitworks.md)。

### <a name="key-benefits"></a>主要优点

启用 SSPR 的重要优势包括：

* **控制成本**。 SSPR 可让用户自行重置密码，从而可降低 IT 支持成本。 它还减少了因密码丢失和帐户锁定而造成的时间损失成本。 

* **直观的用户体验**。 它提供直观的一次性用户注册过程，使用户能够在任何设备上或位置按需重置密码及解锁帐户。 SSPR 可让用户更快地恢复工作并提高工作效率。

* **灵活性和安全性**。 SSPR 使企业能够获得云平台所提供的安全性和灵活性。 管理员可以更改设置以适应新的安全要求，并在不干扰用户登录的情况下将这些更改应用到用户。

* **可靠的审核和使用情况跟踪**。 组织可以确保在用户重置其自己的密码时业务系统保持安全。 可靠的审核日志包括密码重置过程的每个步骤的信息。 可以通过 API 访问这些日志，用户可将数据导入到所选的安全事故和事件监视 (SIEM) 系统。

### <a name="licensing"></a>授权

Azure Active Directory 按用户许可，这意味着，每个用户需要为其使用的功能购买相应的许可证。 建议对 SSPR 启用基于组的许可。 

若要比较版本和功能并启用基于组或基于用户的许可，请参阅 [Azure AD 自助式密码重置的许可要求](./concept-sspr-licensing.md)。

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
|Pluralsight 付费课程 |[标识和访问管理问题](https://www.pluralsight.com/courses/identity-access-management-issues) 了解你的组织中需要得到关注的 IAM 和安全问题。 请专门参阅“其他身份验证方法”模块。|
| |[Microsoft 企业移动性套件入门](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) 了解采用一种可进行身份验证、授权、加密的方式和安全移动体验将本地资产扩展到云的最佳实践。 另请参阅“配置 Microsoft Azure Active Directory Premium 的高级功能”模块。
|教程 |[完成 Azure AD 自助式密码重置试点推行](./tutorial-enable-sspr.md) |
| |[启用密码写回](./tutorial-enable-sspr-writeback.md) |
| |[Windows 10 登录屏幕中的 Azure AD 密码重置](./howto-sspr-windows.md) |
| 常见问题解答|[密码管理常见问题解答](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>解决方案体系结构

以下示例描述了常见混合环境的密码重置解决方案体系结构。

![解决方案体系结构图](./media/howto-sspr-deployment//solutions-architecture.png)

工作流的说明

若要重置密码，用户需转到[密码重置门户](https://aka.ms/sspr)。 他们必须验证以前注册的一种或多种身份验证方法来证明其身份。 如果用户成功重置了密码，重置过程将会开始。

* 对于仅限云的用户，SSPR 会将新密码存储在 Azure AD 中。 

* 对于混合用户，SSPR 会通过 Azure AD Connect 服务将密码写回到本地 Active Directory。 

注意：对于已禁用[密码哈希同步 (PHS)](../hybrid/whatis-phs.md) 的用户，SSPR 仅将密码存储在本地 Active Directory 中。

### <a name="best-practices"></a>最佳做法

连同组织中的其他常用应用程序或服务一起部署 SSPR 可帮助用户快速完成注册。 此操作会产生大量登录，可推进注册操作。

在部署 SSPR 之前，可以选择确定每个密码重置调用的数量和平均成本。 可以使用此“数据发布”部署来展示 SSPR 为组织带来的价值。

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>启用 SSPR 和 MFA 的组合注册

Microsoft 建议组织为 SSPR 和多重身份验证启用组合注册体验。 启用此组合注册体验后，用户只需选择其注册信息一次即可启用这两项功能。

组合注册体验不需要组织同时启用 SSPR 和 Azure 多重身份验证。 组合注册可为组织提供更好的用户体验。 有关详细信息，请参阅[组合安全信息注册](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-deployment-project"></a>规划部署项目

在环境中确定此部署的策略时，请考虑组织的需求。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

当技术项目失败时，它们通常是由于在影响、结果和责任方面不符合预期而导致的。 若要避免这些问题，请[确保让合适的利益干系人参与](https://aka.ms/deploymentplans)，并通过记录利益干系人及其项目的输入信息和相应责任，使项目中利益干系人的角色得到充分了解。

#### <a name="required-administrator-roles"></a>所需的管理员角色


| 业务角色/角色| Azure AD 角色（如有必要） |
| - | - |
| 1 级支持人员| 密码管理员 |
| 2 级支持人员| 用户管理员 |
| SSPR 管理员| 全局管理员 |


### <a name="plan-communications"></a>规划沟通

通信对于任何新服务的成功至关重要。 主动与用户沟通，告诉他们其体验会发怎样的变化，何时会有变化，以及在遇到问题时如何获取支持。 请查看 [Microsoft 下载中心上的自助式密码重置推出材料](https://www.microsoft.com/download/details.aspx?id=56768)，了解有关如何规划最终用户通信策略的建议。

### <a name="plan-a-pilot"></a>规划试点

建议使用测试环境中的 SSPR 初始配置。 通过为组织中的一部分用户启用 SSPR，开始使用试点组。 请参阅[关于试点的最佳做法](../fundamentals/active-directory-deployment-plans.md)。

若要创建一个组，请参阅如何[在 Azure Active Directory 中创建组并添加成员](../fundamentals/active-directory-groups-create-azure-portal.md)。 

## <a name="plan-configuration"></a>规划配置

若要启用 SSPR 并使用推荐值，需要以下设置。

| 区域 | 设置 | 值 |
| --- | --- | --- |
| **SSPR 属性** | 已启用自助式密码重置 | 在试运行环境中为“选定组”/在生产环境中为“全部”  |
| **身份验证方法** | 注册所需的身份验证方法数 | 至少比重置所需的数目多 1 个 |
|   | 重置所需的身份验证方法数 | 1 或 2 |
| **注册** | 要求用户在登录时注册 | 是 |
|   | 用户必须在几天后重新确认其身份验证信息 | 90 – 180 天 |
| **通知** | 重置密码时通知用户 | 是 |
|   | 当其他管理员重置其密码时通知所有管理员 | 是 |
| **自定义** | 自定义服务台链接 | 是 |
|   | 自定义服务台电子邮件或 URL | 支持站点或电子邮件地址 |
| **本地集成** | 将密码写回到本地 AD | 是 |
|   | 允许用户在不重置密码的情况下解锁帐户 | 是 |

### <a name="sspr-properties"></a>SSPR 属性

启用 SSPR 时，请在试点环境中选择适当的安全组。

* 若要为每个人强制实施 SSPR 注册，我们建议使用“全部”选项。
* 否则，请选择适当的 Azure AD 或 AD 安全组。

### <a name="authentication-methods"></a>身份验证方法

启用了 SSPR 时，仅当用户在管理员已启用的身份验证方法中输入了数据时，他们才能重置其密码。 方法包括电话、验证器应用通知、安全问题等。有关详细信息，请参阅[什么是身份验证方法？](./concept-authentication-methods.md)。

建议采用以下身份验证方法设置：

* 将“注册所需的身份验证方法数”设置为至少比重置所需的身份验证方法数多 1 个。 允许使用多种身份验证方法可让用户在需要重置时获得灵活性。

* 将“重置时所需的方法数”设置为适合你的组织的数量。 如果只设置一种方法，只能提供最低程度的安全保障，设置两种就可能进一步改善安全状况。 

注意：必须根据 [Azure Active Directory 中的密码策略和限制](./concept-sspr-policy.md)为用户配置身份验证方法。

### <a name="registration-settings"></a>注册设置

将“要求用户在登录时注册”设置为“是” 。 此设置要求用户在登录时注册，确保所有用户受到保护。

将“用户必须在几天后重新确认其身份验证信息”设置为 **90** 到 **180** 天，除非组织需要使用更短的时限。

### <a name="notifications-settings"></a>通知设置

将“在密码重置时通知用户”和“当有管理员重置其密码时通知所有管理员”均设置为“是”  。 对这两个选项都选择“是”可以提高安全性，因为这可以确保用户知道其密码已重置。 此外，可以确保当某个管理员更改密码时，所有管理员都知道这种情况。 如果用户或管理员收到通知但他们并未发起更改，他们可以立即报告潜在的安全问题。

### <a name="customization-settings"></a>自定义设置

自定义支持电子邮件或 URL，确保遇到问题的用户能够立即获得帮助，这一点至关重要。 将此选项设置为用户熟悉的常见支持人员电子邮件地址或网页。 

有关详细信息，请参阅[自定义自助式密码重置的 Azure AD 功能](./howto-sspr-customization.md)。

### <a name="password-writeback"></a>密码写回

**密码写回**与 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) 一同启用，可以实时将云中的密码重置写回到现有的本地目录。 有关详细信息，请参阅[什么是密码写回？](./concept-sspr-writeback.md)

我们建议使用以下设置：

* 确保“将密码写回到本地 AD”设置为“是”。  
* 将“允许用户在不重置密码的情况下解锁帐户”设置为“是”。 

默认情况下，Azure AD 在执行密码重置时会解锁帐户。

### <a name="administrator-password-setting"></a>管理员密码设置

管理员帐户拥有提升的权限。 本地企业或域管理员无法通过 SSPR 重置其密码。 本地管理员帐户具有以下限制：

* 只能在本地环境中更改其密码。
* 不得使用机密问题和回答作为重置密码的方法。

建议不要将本地 Active Directory 管理员帐户与 Azure AD 同步。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多个标识管理系统的环境

某些环境具有多个标识管理系统。 Oracle AM 和 SiteMinder 等本地标识管理器要求与 AD 同步以管理密码。 为此，可以将密码更改通知服务 (PCNS) 这类工具与 Microsoft Identity Manager (MIM) 配合使用。 若要查找更为复杂的相关方案的信息，请参阅文章[在域控制器上部署 MIM 密码更改通知服务](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)。

## <a name="plan-testing-and-support"></a>规划测试和支持

在从初始试点组到组织范围的部署的每个阶段，请确保结果与预期相符。

### <a name="plan-testing"></a>规划测试

为了确保部署按预期方式工作，请规划好一套可用于验证实施结果的测试用例。 若要评估测试用例，需要一个带密码的非管理员测试用户。 如果需要创建用户，请参阅[将新用户添加到 Azure Active Directory](../fundamentals/add-users-azure-active-directory.md)。

下表提供了有用的测试方案，你可以参考这些方案根据自己的策略来阐述组织预期的结果。
<br>


| 业务案例| 预期结果 |
| - | - |
| 可以从企业网络内部访问 SSPR 门户| 由组织确定 |
| 可从企业网络外部访问 SSPR 门户| 由组织确定 |
| 未为用户启用密码重置时从浏览器重置用户密码| 用户无法访问密码重置流 |
| 用户未注册密码重置时从浏览器重置用户密码| 用户无法访问密码重置流 |
| 强制实施密码重置注册时用户登录| 提示用户注册安全信息 |
| 密码重置注册完成时用户登录| 提示用户注册安全信息 |
| 当用户没有许可证时，可以访问 SSPR 门户| 可访问 |
| 从已加入 Windows 10 Azure AD 或已加入混合 Azure AD 的设备锁屏界面重置用户密码| 用户可以重置密码 |
| 管理员可以近实时地使用 SSPR 注册和使用情况数据| 可通过审核日志获取 |

另请参阅 [全面完成 Azure AD 自助式密码重置试点推行](./tutorial-enable-sspr.md)。 在本教程中，你将在组织中启用 SSPR 的试点推行，并使用非管理员帐户进行测试。

### <a name="plan-support"></a>规划支持

尽管 SSPR 通常不会产生用户问题，但支持人员必须准备好应对可能出现的问题。 虽然管理员可以通过 Azure AD 门户为最终用户重置密码，但最好是借助自助式支持过程来解决问题。

为使支持团队取得成功，可以根据用户发来的问题撰写常见问题解答。 以下是一些示例：

| 方案| 说明 |
| - | - |
| 用户无法使用任何已注册的可用身份验证方法| 用户正在尝试重置其密码，但无法使用已注册的任何身份验证方法（例如：其手机遗忘在家中，并且无法访问电子邮件） |
| 用户未在其办公室电话或手机上收到短信或呼叫| 用户正在尝试通过短信或呼叫来验证其身份，但未收到短信/呼叫。 |
| 用户无法访问密码重置门户| 用户想要重置其密码，但未启用密码重置，因此无法访问该页来更新密码。 |
| 用户无法设置新密码| 用户在密码重置流期间完成了验证，但无法设置新密码。 |
| 用户在 Windows 10 设备上未看到“重置密码”链接| 用户尝试从 Windows 10 锁屏界面重置密码，但设备未加入 Azure AD，或 Intune 设备策略未启用 |

### <a name="plan-rollback"></a>规划回滚

若要回滚部署：

* 对于单个用户，可从安全组中删除该用户 

* 对于某个组，可从 SSPR 配置中删除该组

* 对于所有人，可对 Azure AD 租户禁用 SSPR

## <a name="deploy-sspr"></a>部署 SSPR

在部署之前，请确保已完成以下操作：

1. 已创建并开始执行[沟通计划](#plan-communications)。

1. 确定适当的[配置设置](#plan-configuration)。

1. 已确定[试点](#plan-a-pilot)和生产环境的用户与组。

1. 已确定注册和自助服务的[配置设置](#plan-configuration)。

1. [已配置密码写回](#password-writeback)（如果使用混合环境）。


**现在可以开始部署 SSPR！**

请参阅[启用自助式密码重置](./tutorial-enable-sspr.md#enable-self-service-password-reset)，获取有关配置以下区域的完整分步指导。

1. [身份验证方法](./concept-authentication-methods.md)

1. [注册设置](./concept-registration-mfa-sspr-combined.md)

1. [通知设置](#notifications-settings)

1. [自定义设置](./howto-sspr-customization.md)

1. [本地集成](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>在 Windows 中启用 SSPR
对于运行 Windows 7、8、8.1、10 的计算机，可以[在 Windows 登录屏幕上允许用户重置其密码](./howto-sspr-windows.md)。

## <a name="manage-sspr"></a>管理 SSPR

Azure AD 可以通过审核和报告提供有关 SSPR 性能的附加信息。

### <a name="password-management-activity-reports"></a>密码管理活动报告 

可以使用 Azure 门户上预生成的报告来衡量 SSPR 性能。 如果有相应的授权，还可以创建自定义查询。 有关详细信息，请参阅 [Azure AD 密码管理的报告选项](./howto-sspr-reporting.md)

> [!NOTE]
>  必须是[全局管理员](../users-groups-roles/directory-assign-admin-roles.md)，并且必须选择为组织收集这些数据。 若要做出此选择，必须在 Azure 门户上至少访问“报告”选项卡或审核日志一次。 在此之前，不会为组织收集数据。

注册和密码重置的审核日志可保留 30 天。 如果公司内部的安全审核要求保留更长的期限，需要将日志导出到 SIEM 工具（如 [Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 ArcSight）中使用。

![SSPR 报表屏幕快照](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>身份验证方法 - 使用情况和见解

使用[使用情况和见解](./howto-authentication-methods-usage-insights.md)可以了解针对 Azure MFA 和 SSPR 等功能的身份验证方法在组织中的运作方式。 此报告功能可让组织了解注册的方法，以及这些方法的用法。

### <a name="troubleshoot"></a>疑难解答

* 参阅[排查自助式密码重置问题](./active-directory-passwords-troubleshoot.md) 

* 遵循[密码管理常见问题解答](./active-directory-passwords-faq.md) 

### <a name="helpful-documentation"></a>可提供帮助的文档

* [有哪些身份验证方法？](./concept-authentication-methods.md)

* [工作原理：Azure AD 自助式密码重置？](./concept-sspr-howitworks.md)

* [自定义自助式密码重置的 Azure AD 功能](./howto-sspr-customization.md)

* [Azure Active Directory 中的密码策略和限制](./concept-sspr-policy.md)

* [什么是密码写回？](./concept-sspr-writeback.md)

## <a name="next-steps"></a>后续步骤

* 若要开始部署 SSPR，请参阅[启用 Azure AD 自助式密码重置](tutorial-enable-sspr.md)

* [考虑实施 Azure AD 密码保护](./concept-password-ban-bad.md)

* [考虑实施 Azure AD 智能锁定](./howto-password-smart-lockout.md)