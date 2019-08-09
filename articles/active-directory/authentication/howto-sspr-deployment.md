---
title: 自助服务密码重置部署计划-Azure Active Directory
description: 成功实现 Azure AD 自助服务密码重置的策略
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
ms.openlocfilehash: 7033c7bd3e783157280709b2c7e889473166ac84
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879227"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>部署 Azure AD 自助服务密码重置

自助服务密码重置 (SSPR) 是一项 Azure Active Directory 功能, 使员工无需联系 IT 人员就可重置其密码。 员工必须注册或注册自助密码重置, 然后才能使用服务。 在注册期间, 员工选择一个或多个由其组织启用的身份验证方法。

SSPR 使员工无论在何处或是当天的时间, 都可以快速地取消阻止并继续工作。 通过允许用户取消阻止其自身, 你的组织可以降低大多数常见密码相关问题的非生产时间和高支持成本。

通过在组织中部署 SSPR 与其他应用程序或服务, 帮助用户快速注册。 此操作会生成大量的登录, 并会推动注册操作。

在部署 SSPR 之前, 组织可能需要确定一段时间内发生的密码重置相关咨询台呼叫的数量, 以及每个调用的平均成本。 他们可以使用此数据发布部署来显示 SSPR 为你的组织带来的价值。  

## <a name="how-sspr-works"></a>SSPR 的工作原理

1. 当用户尝试重置密码时, 它们必须验证其以前注册的身份验证方法或方法来证明其身份。
1. 然后, 用户输入新密码。
   1. 对于仅限云的用户, 新密码存储在 Azure Active Directory 中。 有关详细信息, 请参阅[SSPR 的工作](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work)原理一文。
   1. 对于混合用户, 通过 Azure AD Connect 服务将密码写回到本地 Active Directory。 有关详细信息, 请参阅[什么是密码写回](concept-sspr-writeback.md#how-password-writeback-works)。

## <a name="licensing-considerations"></a>许可注意事项

Azure Active Directory 是每用户许可证, 每个用户都必须具有相应的许可证, 才能使用这些功能。

有关许可的详细信息, 请参阅[Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>启用 SSPR 和 MFA 的组合注册

Microsoft 建议组织启用 SSPR 和多重身份验证的组合注册体验。 启用此组合注册体验后, 用户只需选择其注册信息一次即可启用这两种功能。

![组合的安全信息注册](./media/howto-sspr-deployment/combined-security-info.png)

组合注册体验不需要组织同时启用 SSPR 和 Azure 多重身份验证。 与传统的单个组件相比, 组合注册体验为组织提供了更好的用户体验。 有关组合注册以及如何启用的详细信息, 请参阅[结合安全信息注册 (预览)](concept-registration-mfa-sspr-combined.md)一文。

## <a name="plan-the-configuration"></a>规划配置

启用 SSPR 和推荐值需要以下设置。

| 区域 | 设置 | ReplTest1 |
| --- | --- | --- |
| **SSPR 属性** | 已启用自助密码重置 | 用于生产的试点/**全部**组的**所选**组 |
| **身份验证方法** | 注册所需的身份验证方法 | 重置所需的始终为1 |
|   | 重置所需的身份验证方法 | 一两个 |
| **注册** | 要求用户在登录时注册 | 是 |
|   | 要求用户重新确认其身份验证信息之前的天数 | 90–180天 |
| **通知** | 重置密码时通知用户 | 是 |
|   | 当其他管理员重置其密码时通知所有管理员 | 是 |
| **自定义** | 自定义支持人员链接 | 是 |
|   | 自定义支持人员电子邮件或 URL | 支持站点或电子邮件地址 |
| **本地集成** | 将密码写回到本地 AD | 是 |
|   | 允许用户在不重置密码的情况下解锁帐户 | 是 |

### <a name="sspr-properties-recommendations"></a>SSPR 属性建议

启用自助服务密码重置时, 请选择要在试点期间使用的安全组。

当你计划更广泛地启动服务时, 建议使用 All 选项为组织中的每个人强制执行 SSPR。 如果无法设置为 "全部", 请选择相应的 Azure AD 安全组或同步到 Azure AD 的 AD 组。

### <a name="authentication-methods"></a>身份验证方法

将所需的身份验证方法至少注册到重置所需的次数。 允许多个用户在需要重置时提供灵活性。

设置重置为适用于组织的级别**所需的方法数**。 其中一种要求最小的摩擦, 而两个可能会增加您的安全状况。

请参阅[什么是身份验证方法](concept-authentication-methods.md), 详细了解哪些身份验证方法可用于 SSPR、预定义的安全问题, 以及如何创建自定义安全问题。

### <a name="registration-settings"></a>注册设置

设置 "**要求用户在登录时注册** **"** 。 此设置表示用户在登录时被迫注册, 从而确保所有用户都受到保护。

设置在**要求用户重新确认其身份验证信息**为介于**90**和**180**天之间的天数, 除非你的组织有业务需要更短的时间范围。

### <a name="notifications-settings"></a>通知设置

同时配置在**密码重置时通知用户**和在**其他管理员将密码重置**为 **"是**" 时通知所有管理员。 如果选择 **"是**", 则确保用户在密码重置后知道用户是否知道, 并且所有管理员都知道何时管理员更改密码。 如果用户或管理员收到此类通知, 并且他们尚未启动更改, 则他们可以立即报告潜在的安全漏洞。

### <a name="customization"></a>自定义

自定义**支持人员电子邮件或 URL**以确保遇到问题的用户可以快速获得帮助, 这一点很重要。 将此选项设置为用户熟悉的常见支持人员电子邮件地址或网页。

### <a name="on-premises-integration"></a>本地集成

如果你有混合环境, 请确保将 "将**密码写回到本地 AD** " 设置为 **"是"** 。 同时将 "允许用户解锁而不重置密码" 设置为 "是", 因为这样可以提高灵活性。

### <a name="changingresetting-passwords-of-administrators"></a>更改/重置管理员的密码

管理员帐户是具有提升权限的特殊帐户。 若要对其进行保护, 以下限制适用于更改管理员的密码:

- 本地企业管理员或域管理员无法通过 SSPR 重置其密码。 它们只能在本地环境中更改其密码。 因此, 建议不要将本地 AD 管理员帐户同步到 Azure AD。
- 管理员不能使用机密问题 & 应答作为重置密码的方法。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多个标识管理系统的环境

如果环境中有多个标识管理系统 (如 Oracle AM、SiteMinder 或其他系统等本地标识管理器), 则写入 Active Directory 的密码可能需要使用类似于密码更改通知服务 (PCNS) 与 Microsoft Identity Manager (MIM)。 若要查找有关这种更为复杂的情况的信息, 请参阅文章在[域控制器上部署 MIM 密码更改通知服务](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)。

## <a name="plan-deployment-and-support-for-sspr"></a>规划部署和对 SSPR 的支持

### <a name="engage-the-right-stakeholders"></a>吸引适当的利益干系人

当技术项目失败时, 它们通常是由于对影响、结果和责任的预期不匹配而导致的。 若要避免这些问题, 请确保您的利益干系人参与, 并通过记录利益干系人及其项目的输入和责任, 充分了解项目中的利益干系人角色。

### <a name="communications-plan"></a>通信计划

通信对于任何新服务的成功至关重要。 主动与用户交流如何使用该服务, 以及他们在不能按预期工作时获取帮助的方法。 请参阅[Microsoft 下载中心上的自助服务密码重置推广资料](https://www.microsoft.com/download/details.aspx?id=56768), 了解有关如何规划最终用户通信策略的建议。

### <a name="testing-plan"></a>测试计划

若要确保部署按预期运行, 你应该计划一组将用于验证实现的测试用例。 下表包含一些有用的测试方案, 你可以使用这些方案根据你的策略来记录组织预期的结果。

| 商业案例 | 预期结果 |
| --- | --- |
| 可从企业网络内部访问 SSPR 门户 | 由你的组织决定 |
| 可以从企业网络外部访问 SSPR 门户 | 由你的组织决定 |
| 用户未启用密码重置时, 从浏览器重置用户密码 | 用户无法访问密码重置流 |
| 用户未注册密码重置时, 从浏览器重置用户密码 | 用户无法访问密码重置流 |
| 强制密码重置注册时用户登录 | 系统将提示用户注册安全信息 |
| 密码重置注册完成后用户登录 | 不会提示用户注册安全信息 |
| 当用户没有许可证时, 可以访问 SSPR 门户 | 可访问 |
| 用户注册后, 从 Windows 10 AADJ 或 H + AADJ 设备锁定屏幕重置用户密码 | 用户可以重置密码 |
| SSPR 注册和使用情况数据将以近乎实时的速度向管理员提供 | 可通过审核日志获取 |

### <a name="support-plan"></a>支持计划

尽管 SSPR 通常不会产生用户问题, 但对支持人员进行准备来处理可能出现的问题非常重要。

虽然管理员可以通过 Azure AD 门户更改或重置最终用户的密码, 但最好是通过自助服务支持过程来帮助解决问题。

在本文档的 "操作指南" 部分中, 创建支持案例的列表及其可能的原因, 并创建解决方法。

### <a name="auditing-and-reporting"></a>审核和报告

部署后，许多组织想要知道如何使用或者是否已真正使用自助密码重置 (SSPR)。 Azure Active Directory (Azure AD) 提供的报表功能可帮助使用预生成的报表来回答问题。

注册和密码重置的审核日志适用于30天。 因此, 如果公司内的安全审核需要更长的保留期, 则需要将日志导出并使用 SIEM 工具, 如[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 ArcSight。

在表中, 如下所示, 记录备份计划、系统和负责方。 你可能不需要单独的审核和报告备份, 但你应该有一个单独的备份, 你可以从中恢复问题。

|   | 下载频率 | 目标系统 | 责任方 |
| --- | --- | --- | --- |
| 审核备份 |   |   |   |
| 报告备份 |   |   |   |
| 灾难恢复备份 |   |   |   |

## <a name="implementation"></a>实现

实现分三个阶段进行:

- 配置用户和许可证
- 为注册和自助服务配置 Azure AD SSPR
- 配置密码写回 Azure AD Connect

### <a name="communicate-the-change"></a>传达更改

开始实现您在规划阶段开发的通信计划。

### <a name="ensure-groups-are-created-and-populated"></a>确保已创建并填充组

参考 "规划密码身份验证方法" 部分, 确保试点或生产实现的组可用, 并将所有相应的用户添加到组中。

### <a name="apply-licenses"></a>应用许可证

要实现的组必须分配有 Azure AD premium 许可证。 你可以直接将许可证分配给组, 或者可以使用现有的许可证策略, 例如通过 PowerShell 或基于组的许可。

有关将许可证分配到用户组的信息, 请参阅在 Azure Active Directory 中[按组成员身份向用户分配许可证](../users-groups-roles/licensing-groups-assign.md)。

### <a name="configure-sspr"></a>配置 SSPR

#### <a name="enable-groups-for-sspr"></a>为 SSPR 启用组

1. 使用管理员帐户访问 Azure 门户。
1. 选择 "所有服务", 然后在 "筛选器" 框中, 键入 Azure Active Directory, 然后选择 "Azure Active Directory"。
1. 在 "Active Directory" 边栏选项卡上, 选择 "密码重置"。
1. 在 "属性" 窗格中, 选择 "选定"。 如果希望所有用户都处于启用状态, 请选择 "全部"。
1. 在 "默认密码重置策略" 边栏选项卡中, 键入第一个组的名称, 将其选中, 然后单击屏幕底部的 "选择", 并选择屏幕顶部的 "保存"。
1. 为每个组重复此过程。

#### <a name="configure-the-authentication-methods"></a>配置身份验证方法

请参阅本文档的规划密码身份验证方法部分中的计划。

1. 选择 "注册", 在 "要求用户在登录时注册" 下, 选择 "是", 然后设置过期前的天数, 然后选择 "保存"。
1. 选择 "通知", 根据计划进行配置, 然后选择 "保存"。
1. 选择 "自定义", 根据计划进行配置, 然后选择 "保存"。
1. 选择 "本地集成", 根据计划进行配置, 然后选择 "保存"。

### <a name="enable-sspr-in-windows"></a>在 Windows 中启用 SSPR

运行版本1803或更高版本的 windows 10 设备 (Azure AD 联接或混合 Azure AD 联接) 可以在 Windows 登录屏幕上重置其密码。 有关配置此功能的信息和步骤, 请参阅[从登录屏幕 Azure AD 密码重置](tutorial-sspr-windows.md)一文

### <a name="configure-password-writeback"></a>配置密码写回服务

有关为你的组织配置密码写回的步骤, 请参阅["操作方法:配置密码写](howto-sspr-writeback.md)回。

## <a name="manage-sspr"></a>管理 SSPR

用于管理与自助服务密码重置相关的功能的必需角色。

| 业务角色/角色 | Azure AD 角色 (如有必要) |
| :---: | :---: |
| 1级支持人员 | 密码管理员 |
| 2级支持 | 用户管理员 |
| SSPR 管理员 | 全局管理员 |

### <a name="support-scenarios"></a>支持方案

若要使支持团队取得成功, 你可以根据从用户收到的问题创建 FAQ。 下表包含了常见的支持方案。

| 方案 | 描述 |
| --- | --- |
| 用户未提供任何已注册的身份验证方法 | 用户尝试重置其密码, 但没有任何注册的可用身份验证方法 (例如: 他们在家里离开了手机, 无法访问电子邮件) |
| 用户在其办公室或手机上未收到短信或呼叫 | 用户正在尝试通过短信或呼叫来验证其标识, 但没有收到文本/调用。 |
| 用户无法访问密码重置门户 | 用户想要重置其密码, 但未启用密码重置, 因此无法访问该页来更新密码。 |
| 用户无法设置新密码 | 用户在密码重置流期间完成验证, 但不能设置新密码。 |
| 用户在 Windows 10 设备上看不到 "重置密码" 链接 | 用户尝试从 Windows 10 锁屏界面重置密码, 但设备未加入 Azure AD, 或者 Intune 设备策略未启用 |

你可能还需要包含如下所述的信息, 以进行进一步的故障排除。

- 为 SSPR 启用了哪些组。
- 配置了哪些身份验证方法。
- 与企业网络上的相关的访问策略。
- 常见方案的故障排除步骤。

你还可以参阅有关自助服务密码重置故障排除的联机文档, 了解最常见的 SSPR 方案的一般故障排除步骤。

## <a name="next-steps"></a>后续步骤

- [考虑实施 Azure AD 密码保护](concept-password-ban-bad.md)

- [考虑实现 Azure AD 智能锁定](howto-password-smart-lockout.md)
