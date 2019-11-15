---
title: 自助式密码重置部署计划 - Azure Active Directory
description: 有关成功实现 Azure AD 自助式密码重置的策略
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: b648d6f914b5e3004ea3b62019bbec33e5a4871d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081521"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>部署 Azure AD 自助式密码重置

> [!NOTE]
> 本指南介绍了自助服务密码重置以及如何部署。 如果你正在寻找自助服务密码重置工具以返回到你的帐户，请转到[https://aka.ms/sspr](https://aka.ms/sspr)。 

自助式密码重置 (SSPR) 是一项 Azure Active Directory 功能，可让员工自行重置其密码，而无需联系 IT 工作人员。 员工必须注册自助式密码重置或者在其中注册才能使用该服务。 在注册期间，员工可以选择其组织启用的一种或多种身份验证方法。

无论何时何地，SSPR 都可让员工快速消除阻碍并继续工作。 由于用户可以自行消除阻碍，组织可以降低大多数常见密码相关问题造成的非产出时间和较高的支持成本。

连同组织中的其他应用程序或服务一起部署 SSPR 可帮助用户快速完成注册。 此操作会产生大量的登录，并推动注册过程。

在部署 SSPR 之前，组织可能需要确定一段时间内发生的与密码重置相关的服务台呼叫数，以及每次呼叫的平均成本。 部署后，组织可以使用此数据来显示 SSPR 为其带来的价值。  

## <a name="how-sspr-works"></a>SSPR 的工作原理

1. 当用户尝试重置密码时，他们必须验证以前注册的一种或多种身份验证方法来证明其身份。
1. 然后用户输入新密码。
   1. 对于仅限云的用户，新密码将存储在 Azure Active Directory 中。 有关详细信息，请参阅 [SSPR 的工作原理](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work)一文。
   1. 对于混合用户，密码将通过 Azure AD Connect 服务写回到本地 Active Directory。 有关详细信息，请参阅[什么是密码写回](concept-sspr-writeback.md#how-password-writeback-works)一文。

## <a name="licensing-considerations"></a>许可注意事项

Azure Active Directory 按用户许可，这意味着，每个用户必须为其使用的功能购买相应的许可证。

在 [Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory/)上可以找到有关许可的详细信息

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>启用 SSPR 和 MFA 的组合注册

Microsoft 建议组织启用 SSPR 和多重身份验证的组合注册体验。 启用此组合注册体验后，用户只需选择其注册信息一次即可启用这两种功能。

![组合的安全信息注册](./media/howto-sspr-deployment/combined-security-info.png)

组合注册体验不需要组织同时启用 SSPR 和 Azure 多重身份验证。 与传统的单个组件相比，组合注册体验为组织提供了更好的用户体验。 有关组合注册以及如何启用的详细信息，请参阅[结合安全信息注册（预览）](concept-registration-mfa-sspr-combined.md)一文。

## <a name="plan-the-configuration"></a>规划配置

下面是启用 SSPR 所需的设置及其建议值。

| 区域 | 设置 | 值 |
| --- | --- | --- |
| **SSPR 属性** | 已启用自助式密码重置 | 在试运行环境中为“选定组”/在生产环境中为“全部” |
| **身份验证方法** | 注册所需的身份验证方法数 | 至少比重置所需的数目多 1 个 |
|   | 重置所需的身份验证方法数 | 1 或 2 |
| **注册** | 要求用户在登录时注册 | 是 |
|   | 用户必须在几天后重新确认其身份验证信息 | 90–180天 |
| **通知** | 重置密码时通知用户 | 是 |
|   | 当其他管理员重置其密码时通知所有管理员 | 是 |
| **自定义** | 自定义服务台链接 | 是 |
|   | 自定义服务台电子邮件或 URL | 支持站点或电子邮件地址 |
| **本地集成** | 将密码写回到本地 AD | 是 |
|   | 允许用户在不重置密码的情况下解锁帐户 | 是 |

### <a name="sspr-properties-recommendations"></a>SSPR 属性建议

启用自助式密码重置时，请选择要在试运行期间使用的安全组。

如果你打算更广泛地推出该服务，我们建议使用“全部”选项，以针对组织中的每个人强制实施 SSPR。 如果无法设置为“全部”，请选择相应的 Azure AD 安全组，或已同步到 Azure AD 的 AD 组。

### <a name="authentication-methods"></a>身份验证方法

将“注册所需的身份验证方法数”设置为至少比重置所需的身份验证方法数多 1 个。 允许使用多个方法可让用户在需要重置时获得灵活性。

将“重置所需的方法数”设置为适合组织的级别。 使用 1 个可以尽量消除不便，但使用 2 个可以改善安全态势。

请参阅[什么是身份验证方法](concept-authentication-methods.md)，详细了解哪些身份验证方法可用于 SSPR、预定义的安全问题，以及如何创建自定义安全问题。

### <a name="registration-settings"></a>注册设置

将“要求用户在登录时注册”设置为“是”。 此设置表示用户在登录时必须注册，这可以确保所有用户受到保护。

将“用户必须在几天后重新确认其身份验证信息”设置为 **90** 到 **180** 天，除非组织需要使用更短的时限。

### <a name="notifications-settings"></a>通知设置

将“重置密码时通知用户”和“当其他管理员重置其密码时通知所有管理员”都设置为“是”。 对这两个选项都选择“是”可以提高安全性，因为这可以确保当用户密码重置时，用户知道这种情况；当某个管理员更改密码时，所有管理员都知道这种情况。 如果用户或管理员收到此类通知但他们并未发起更改，他们可以立即报告潜在的安全违规。

### <a name="customization"></a>自定义

自定义**服务台电子邮件或 URL**，以确保遇到问题的用户能够快速获得帮助，这一点至关重要。 请将此选项设置为用户熟悉的常见服务台电子邮件地址或网页。

### <a name="on-premises-integration"></a>本地集成

如果你使用混合环境，请确保将“将密码写回到本地 AD”设置为“是”。 同时，将“允许用户在不重置密码的情况下解锁帐户”设置为“是”，因为这可以让用户获得更大的灵活性。

### <a name="changingresetting-passwords-of-administrators"></a>更改/重置管理员的密码

管理员帐户是权限已提升的特殊帐户。 为了对其进行保护，更改管理员的密码时存在以下限制：

- 本地企业管理员或域管理员无法通过 SSPR 重置其密码。 他们只能在本地环境中更改其密码。 因此，建议不要将本地 AD 管理员帐户同步到 Azure AD。
- 管理员不能使用机密问答作为重置密码的方法。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多个标识管理系统的环境

如果环境中有多个标识管理系统（例如 Oracle AM、SiteMinder 等本地标识管理器）或其他系统，则可能需要使用某种工具（例如，配合使用密码更改通知服务 (PCNS) 和 Microsoft Identity Manager (MIM)）将写入 Active Directory 的密码同步到其他系统。 若要查找有关此类较复杂方案的信息，请参阅[在域控制器上部署 MIM 密码更改通知服务](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)一文。

## <a name="plan-deployment-and-support-for-sspr"></a>规划部署和 SSPR 支持

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时，失败的原因往往是对影响、结果和责任的预期不符。 若要避免这些问题，请确保吸引适当的利益干系人，并通过阐述利益干系人及其项目输入和责任，来充分了解项目中的利益干系人角色。

### <a name="communications-plan"></a>沟通计划

沟通对于任何新服务的成功至关重要。 主动与用户沟通，告诉他们如何使用该服务，以及在某项功能不按预期方式工作时他们可以如何获得帮助。 有关如何规划最终用户沟通策略的思路，请参阅 [Microsoft 下载中心上的自助式密码重置推广材料](https://www.microsoft.com/download/details.aspx?id=56768)。

### <a name="testing-plan"></a>测试计划

为了确保部署按预期方式工作，应该规划好一套可用于验证实施结果的测试用例。 下表提供了一些有用的测试方案，你可以参考这些方案根据自己的策略来阐述组织预期的结果。

| 业务案例 | 预期结果 |
| --- | --- |
| 可从企业网络内部访问 SSPR 门户 | 由组织确定 |
| 可从企业网络外部访问 SSPR 门户 | 由组织确定 |
| 未为用户启用密码重置时从浏览器重置用户密码 | 用户无法访问密码重置流 |
| 用户未注册密码重置时从浏览器重置用户密码 | 用户无法访问密码重置流 |
| 强制实施密码重置注册时用户登录 | 提示用户注册安全信息 |
| 完成密码重置注册时用户登录 | 不提示用户注册安全信息 |
| 当用户没有许可证时可以访问 SSPR 门户 | 可访问 |
| 用户注册后从 Windows 10 AADJ 或 H+AADJ 设备锁屏界面重置用户密码 | 用户可以重置密码 |
| 管理员可以近实时地使用 SSPR 注册和使用情况数据 | 可通过审核日志使用 |

### <a name="support-plan"></a>支持计划

尽管 SSPR 通常不会产生用户问题，但支持人员必须随时准备好应对可能出现的问题。

尽管管理员可以通过 Azure AD 门户更改或重置最终用户的密码，但最好是帮助最终用户通过自助支持过程来解决问题。

在本文档的操作指南部分，创建支持案例的列表及其可能的原因，并创建有关解决方法的指导。

### <a name="auditing-and-reporting"></a>审核和报告

部署后，许多组织想要知道如何使用或者是否已真正使用自助密码重置 (SSPR)。 Azure Active Directory (Azure AD) 提供的报表功能可帮助使用预生成的报表来回答问题。

注册和密码重置的审核日志可供使用 30 天。 因此，如果公司内的安全审核需要更长的保留期，则需要将日志导出并使用 SIEM 工具，如[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 ArcSight。

在如下所示的表中阐述备份计划、系统和负责方。 你可能不需要单独的审核和报告备份，但应该单独创建一个备份，以便在出现问题时可从中恢复。

|   | 下载频率 | 目标系统 | 责任方 |
| --- | --- | --- | --- |
| 审核备份 |   |   |   |
| 报告备份 |   |   |   |
| 灾难恢复备份 |   |   |   |

## <a name="implementation"></a>实现

实施按三个阶段进行：

- 配置用户和许可证
- 在 Azure AD SSPR 中配置注册和自助服务
- 在 Azure AD Connect 中配置密码写回

### <a name="communicate-the-change"></a>传达更改

开始实施在规划阶段制定的沟通计划。

### <a name="ensure-groups-are-created-and-populated"></a>确保已创建并填充组

参考“规划密码身份验证方法”部分，确保用于试运行或生产实施方案的组可用，并将所有适当的用户添加到组中。

### <a name="apply-licenses"></a>应用许可证

要实施的组必须分配有 Azure AD Premium 许可证。 可以直接将许可证分配到组，或者使用现有的许可证策略，例如通过 PowerShell 许可或使用基于组的许可。

有关将许可证分配到用户组的信息，请参阅[在 Azure Active Directory 中按组成员身份将许可证分配到用户](../users-groups-roles/licensing-groups-assign.md)一文。

### <a name="configure-sspr"></a>配置 SSPR

#### <a name="enable-groups-for-sspr"></a>为组启用 SSPR

1. 使用管理员帐户访问 Azure 门户。
1. 选择“所有服务”，在“筛选器”框中键入 Azure Active Directory，然后选择“Azure Active Directory”。
1. 在“Active Directory”边栏选项卡上，选择“密码重置”。
1. 在“属性”窗格中选择“选定”。 若要为所有用户启用该服务，请选择“全部”。
1. 在“默认密码重置策略”边栏选项卡中，键入第一个组的名称，将其选中，单击屏幕底部的“选择”，然后选择屏幕顶部的“保存”。
1. 对每个组重复此过程。

#### <a name="configure-the-authentication-methods"></a>配置身份验证方法

参考本文档的“规划密码身份验证方法”部分中的规划。

1. 选择“注册”，在“要求用户在登录时注册”下选择“是”，设置过期前的天数，然后选择“保存”。
1. 选择“通知”，根据计划进行配置，然后选择“保存”。
1. 选择“自定义”，根据计划进行配置，然后选择“保存”。
1. 选择“本地集成”，根据计划进行配置，然后选择“保存”。

### <a name="enable-sspr-in-windows"></a>在 Windows 中启用 SSPR

在运行 1803 或更高版本的、已加入 Azure AD 或混合 Azure AD 的 Windows 10 设备上，用户可在 Windows 登录屏幕上重置其密码。 有关配置此功能的信息和步骤，请参阅[从登录屏幕重置 Azure AD 密码](tutorial-sspr-windows.md)一文

### <a name="configure-password-writeback"></a>配置密码写回服务

有关为你的组织配置密码写回的步骤，请参阅文章[操作方法：配置密码写回](howto-sspr-writeback.md)。

## <a name="manage-sspr"></a>管理 SSPR

管理与自助式密码重置相关的功能所需的角色。

| 业务角色/角色 | Azure AD 角色（如果需要） |
| :---: | :---: |
| 1 级支持人员 | 密码管理员 |
| 2 级支持人员 | 用户管理员 |
| SSPR 管理员 | 全局管理员 |

### <a name="support-scenarios"></a>支持方案

为使支持团队取得成功，可以基于用户发来的问题创建 FAQ。 下表包含了常见的支持方案。

| 方案 | 说明 |
| --- | --- |
| 用户无法使用任何已注册的身份验证方法 | 用户正在尝试重置其密码，但无法使用他们已注册的任何身份验证方法（例如：他们的手机遗忘在家中，并且无法访问电子邮件） |
| 用户的办公电话或手机上未收到短信或呼叫 | 用户正在尝试通过短信或呼叫来验证其身份，但未收到短信/呼叫。 |
| 用户无法访问密码重置门户 | 用户想要重置其密码，但未启用密码重置，因此无法访问该页来更新密码。 |
| 用户无法设置新密码 | 用户在密码重置流期间完成了验证，但无法设置新密码。 |
| 用户在 Windows 10 设备上未看到“重置密码”链接 | 用户正在尝试从 Windows 10 锁屏界面重置密码，但该设备未加入 Azure AD，或者未启用 Intune 设备策略 |

还可以包含如下所述的信息，以进一步进行故障排除。

- 为哪些组启用了 SSPR。
- 配置了哪些身份验证方法。
- 与企业网络内部或外部相关的访问策略。
- 常见方案的故障排除步骤。

还可以参阅有关自助式密码重置故障排除的联机文档，以了解最常见 SSPR 方案的一般故障排除步骤。

## <a name="next-steps"></a>后续步骤

- [考虑实施 Azure AD 密码保护](concept-password-ban-bad.md)

- [考虑实现 Azure AD 智能锁定](howto-password-smart-lockout.md)
