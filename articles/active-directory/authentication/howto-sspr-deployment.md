---
title: 自助服务密码重置部署计划-Azure Active Directory
description: 策略以确保成功实现 Azure AD 自助服务密码重置
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
ms.openlocfilehash: 8b566bfc3f6c49f6cb9fe31f166356f6ae351e38
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440946"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>将部署到 Azure AD 自助服务密码重置

自助服务密码重置 (SSPR) 是一种 Azure Active Directory 功能，使员工能够重置其密码，而无需与 IT 人员联系。 员工必须先注册，或者进行注册自助服务密码重置使用该服务。 在注册期间，员工选择启用其组织的一个或多个身份验证方法。

SSPR 使员工能够快速获取未被阻止并继续工作，无论身在何处或一天的时间。 通过允许用户取消阻止自身，你的组织可以减少的非工作时间和最常见的与密码相关问题的较高的支持成本。

帮助用户快速注册的另一个应用程序或你的组织中的服务一起部署 SSPR。 此操作将会生成大量的登录名，并将驱动器注册。

在部署之前 SSPR，组织可能希望确定多少个密码重置相关的技术支持呼叫发生时间和每个调用的平均成本。 它们可以使用此部署的数据后即可显示值 SSPR 引入你的组织。  

## <a name="how-sspr-works"></a>SSPR 的工作原理

1. 当用户尝试重置密码时，他们必须验证其之前注册的身份验证方法或方法来证明其身份。
1. 然后，用户输入新密码。
   1. 对于仅限云的用户，新密码存储在 Azure Active Directory 中。 有关详细信息，请参阅文章[如何 SSPR 工作原理](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work)。
   1. 对于混合用户，密码是写回到本地 Active Directory 通过 Azure AD Connect 服务。 有关详细信息，请参阅文章[什么是密码写回](concept-sspr-writeback.md#how-password-writeback-works)。

## <a name="licensing-considerations"></a>许可注意事项

Azure Active Directory 是许可证的每个用户意义上的每个用户必须具有相应的许可证，它们使用的功能。

- Azure AD 基本版或更高版本提供自助服务密码重置为仅限云的用户。
- 自助服务密码重置的本地写回的混合环境需要 Azure AD Premium P1 或更高版本。

可以上找到有关许可的详细信息[Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>为 SSPR 和 MFA 启用组合的注册

Microsoft 建议组织启用 SSPR 和多重身份验证的组合的注册体验。 如果启用此组合的注册体验，用户只需选择一次其注册信息以启用这两项功能。

![组合的安全信息注册](./media/howto-sspr-deployment/combined-security-info.png)

组合的注册体验不需要组织来启用 SSPR 和 Azure 多重身份验证来使用。 组合的注册体验提供了更好的用户体验相比传统的各个组件的组织。 可以在文章中找到有关组合的注册，以及如何启用，详细信息[结合安全信息注册 （预览版）](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>规划配置

需要以下设置来启用 SSPR，以及建议的值。

| 区域 | 设置 | 值 |
| --- | --- | --- |
| **SSPR 属性** | 启用自助服务密码重置 | **所选**试点组 /**所有**用于生产 |
| **身份验证方法** | 注册所需的身份验证方法 | 不是重置所需的始终还有 1 项 |
|   | 重置所需的身份验证方法 | 一个或两个 |
| **注册** | 要求用户在登录时注册 | 是 |
|   | 之前要求用户重新确认其身份验证信息的天数 | 90-180 天 |
| **通知** | 重置密码时通知用户 | 是 |
|   | 当其他管理员重置其密码时通知所有管理员 | 是 |
| **自定义** | 自定义帮助台链接 | 是 |
|   | 自定义帮助服务台电子邮件或 URL | 支持站点或电子邮件地址 |
| **本地集成** | 将密码写回到本地 AD | 是 |
|   | 允许用户解锁而无需重置密码的帐户 | 是 |

### <a name="sspr-properties-recommendations"></a>SSPR 属性建议

在启用自助服务密码重置时，选择要在试用期间使用的安全组。

当您打算更广泛地启动该服务时，我们建议使用所有选项为组织中的每个人都强制实施 SSPR。 如果您不能设置为 all，选择相应的 Azure AD 安全组或同步到 Azure AD 的 AD 组。

### <a name="authentication-methods"></a>身份验证方法

设置注册到至少一个比重置所需的数目更多所需的身份验证方法。 允许多个允许用户灵活地在需要重置时。

设置**重置所需的方法数**到适合于组织的级别。 其中一个需要最少的阻力，而两个可能会增加您的安全状况。

请参阅[什么是身份验证方法](concept-authentication-methods.md)上的身份验证方法是可用于 SSPR、 预定义的安全问题，以及如何创建自定义的安全问题的详细信息。

### <a name="registration-settings"></a>注册设置

设置**要求用户在登录时注册**到**是**。 此设置意味着，用户将不得不在中，登录时注册确保受保护的所有用户。

设置**之前要求用户重新确认其身份验证信息的天数**到之间**90**并**180**天，除非你的组织有业务需要较短的时间范围。

### <a name="notifications-settings"></a>通知设置

同时配置两者**时通知用户密码重置**并**当其他管理员重置其密码时通知所有管理员**到**是**。 选择**是**上都可提高通过确保用户时，才会知道已重置其密码，并且所有管理员，都必须知道当管理员更改密码的安全性。 如果用户或管理员会收到此类通知，并且它们不具有启动更改，他们可以立即报告潜在的安全漏洞。

### <a name="customization"></a>自定义

自定义至关重要**支持人员电子邮件或 URL**以确保出现问题的用户可以快速获得帮助。 此选项设置为公用的支持人员电子邮件地址或用户熟悉的 web 页。

### <a name="on-premises-integration"></a>本地集成

如果你有混合环境，请确保**将密码写回到本地 AD**设置为**是**。 此外设置允许用户解锁而无需为是，重置密码的帐户，因为它提供更大的灵活性。

### <a name="changingresetting-passwords-of-administrators"></a>管理员更改/重置密码

管理员帐户是特殊帐户使用提升的权限。 若要保护它们，具有以下限制更改管理员密码：

- 在本地企业管理员或域管理员无法重置其密码通过 SSPR。 它们仅可以更改其密码在其本地环境中。 因此，我们建议不同步的本地 AD 管理员帐户到 Azure AD。
- 管理员将无法使用机密问题与解答作为一种方法来重置密码。

### <a name="environments-with-multiple-identity-management-systems"></a>具有多个标识管理系统的环境

如果有多个标识管理系统的本地标识管理器，如 Oracle AM 等环境中，SiteMinder，或其他系统，然后写入到 Active Directory 的密码可能需要将同步到其他系统之类的工具密码更改通知服务 (PCNS) 使用 Microsoft Identity Manager (MIM)。 若要查找此更复杂方案的信息，请参阅文章[部署 MIM 密码更改通知服务的域控制器上](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)。

## <a name="plan-deployment-and-support-for-sspr"></a>计划的部署和支持的 SSPR

### <a name="engage-the-right-stakeholders"></a>接触右利益干系人

技术项目失败时，用户通常这样做由于不匹配期望上影响、 结果和职责。 若要避免这些缺陷，请确保你具有吸引力的右利益干系人和利益干系人项目中的角色是好理解的记录利益干系人及其项目输入和责任。

### <a name="communications-plan"></a>沟通计划

通信对任何新服务的成功至关重要。 如何使用服务以及可以如何获取帮助，如果内容未按预期工作主动与用户通信。 审阅[自助服务密码重置 Microsoft 下载中心上的推出资料](https://www.microsoft.com/download/details.aspx?id=56768)以了解如何规划您的最终用户的通信策略。

### <a name="testing-plan"></a>测试计划

若要确保你的部署按预期方式工作，应规划好测试用例将用于验证该实施的一组。 下表包含可向你组织的预期的结果基于策略的文档使用一些有用的测试方案。

| 商业案例 | 预期结果 |
| --- | --- |
| SSPR 门户是可从公司网络中访问 | 由你的组织 |
| SSPR 门户是可从公司网络外部访问 | 由你的组织 |
| 用户未启用密码重置时重置用户密码从浏览器 | 用户不能访问密码重置流 |
| 用户尚未注册进行密码重置时重置用户密码从浏览器 | 用户不能访问密码重置流 |
| 强制执行用户登录时密码重置注册 | 系统会提示用户注册安全信息 |
| 已完成用户登录时密码重置注册 | 不提示用户注册安全信息 |
| 当用户没有许可证时，SSPR 门户是可访问 | 可访问 |
| 用户已注册后，重置用户密码从 Windows 10 AADJ 或 H + AADJ 设备锁定屏幕 | 用户可以重置密码 |
| SSPR 注册和使用情况数据可供管理员以近实时 | 可通过审核日志 |

### <a name="support-plan"></a>支持计划

SSPR 通常不创建用户问题，而是必须支持人员准备好处理可能出现的问题。

管理员可以更改或重置的最终用户通过 Azure AD 门户的密码，而是更好的做法帮助解决这些问题通过自助服务支持过程。

在本文档的操作指南部分中，创建一系列支持用例和其可能的原因，并创建用于解析的指南。

### <a name="auditing-and-reporting"></a>审核和报告

部署后，许多组织想要知道如何使用或者是否已真正使用自助密码重置 (SSPR)。 Azure Active Directory (Azure AD) 提供的报表功能可帮助使用预生成的报表来回答问题。

30 天内会提供注册和密码重置审核日志。 因此，如果某家公司内安全审核需要更长时间保留，日志需要导出，并如使用到 SIEM 工具[Azure Sentinel](../../sentinel/connect-azure-active-directory.md)，Splunk、 或 ArcSight。

在表中，如下所示，记录的备份计划、 系统和负责的。 你可能不需要单独的审核和报告的备份，但应具有单独的备份可以恢复从出现问题。

|   | 下载的频率 | 目标系统 | 负责人 |
| --- | --- | --- | --- |
| 审核备份 |   |   |   |
| 报表的备份 |   |   |   |
| 灾难恢复备份 |   |   |   |

## <a name="implementation"></a>实现

实现分为三个阶段：

- 配置用户和许可证
- 为注册和自助服务配置 Azure AD SSPR
- 配置 Azure AD Connect 用于密码写回

### <a name="communicate-the-change"></a>更改进行通信

开始在规划阶段中开发的通信计划的实现。

### <a name="ensure-groups-are-created-and-populated"></a>确保创建和填充组

引用的规划密码身份验证方法部分，并确保试验或生产实现的组可用，以及所有相应的用户添加到组。

### <a name="apply-licenses"></a>将许可证

要实现的组必须具有 Azure AD premium 许可证分配给他们。 可以直接向组分配许可证，或者可以使用现有许可证策略，例如通过 PowerShell 或基于组的许可。

有关将许可证分配给用户组的信息可在本文中，找到[将许可证分配给 Azure Active Directory 中的组成员资格用户](../users-groups-roles/licensing-groups-assign.md)。

### <a name="configure-sspr"></a>配置 SSPR

#### <a name="enable-groups-for-sspr"></a>为 SSPR 启用组

1. 访问 Azure 门户中的使用管理员帐户。
1. 选择所有的服务，并在筛选器框中，键入 Azure Active Directory，然后选择 Azure Active Directory。
1. 在 Active Directory 边栏选项卡，选择密码重置。
1. 在属性窗格中，选择所选。 如果你希望所有用户启用，所有选择。
1. 在默认密码重置策略边栏选项卡，键入第一个组的名称、 选择它，然后单击底部的屏幕上，选择并选择保存屏幕的顶部。
1. 对于每个组重复此过程。

#### <a name="configure-the-authentication-methods"></a>配置身份验证方法

引用你规划从本文档的规划密码身份验证方法部分。

1. 选择注册，在要求用户在登录时注册，然后选择是，然后制定的在过期前的天数，然后选择保存。
1. 选择通知，并配置每个你计划，然后选择保存。
1. 选择自定义项，并配置每个你计划，然后选择保存。
1. 选择在本地集成，并配置每个你计划，然后选择保存。

### <a name="enable-sspr-in-windows"></a>启用 Windows 中的 SSPR

Windows 10 设备运行的版本 1803 版或更高版本的任一加入了 Azure AD 或已加入的混合 Azure AD 可以重置其密码在 Windows 登录屏幕。 可以一文中找到的信息和步骤来配置此功能[从登录屏幕重置 Azure AD 密码](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>配置密码写回服务

步骤为你的组织可以发现一文中配置密码写回[操作方法：配置密码写回](howto-sspr-writeback.md)。

## <a name="manage-sspr"></a>管理 SSPR

所需的角色来管理自助服务密码重置与关联的功能。

| 业务角色/角色 | Azure AD 角色 （如有必要） |
| :---: | :---: |
| 1 级支持人员 | 密码管理员 |
| 第 2 级支持人员 | 用户管理员 |
| SSPR 管理员 | 全局管理员 |

### <a name="support-scenarios"></a>支持的方案

若要启用您支持团队取得成功，可以创建列出基于接收来自用户的问题的常见问题。 下表包含常见的支持方案。

| 方案 | 描述 |
| --- | --- |
| 用户不具有任何可用的已注册的身份验证方法 | 用户尝试重置其密码，但不包含任何身份验证方法是否注册可用 (示例： 它们在家里处于其移动电话，并不能访问电子邮件) |
| 用户未收到短信或在其 office 或移动电话上调用 | 用户尝试验证其身份通过短信或呼叫，但未收到文本/调用。 |
| 用户无法访问密码重置门户 | 用户想要重置其密码，但未启用密码重置，因此不能访问页后，可以更新密码。 |
| 用户不能设置新密码 | 用户完成密码重置流期间验证但不能设置新密码。 |
| 用户将看不重置密码链接在 Windows 10 设备上 | 用户尝试从 Windows 10 锁定屏幕中，重置密码，但设备或者未加入到 Azure AD 中，或未启用 Intune 设备策略 |

您可能想要包括如下所示的其他故障排除信息。

- 哪些组启用的 SSPR。
- 配置身份验证方法。
- 与相关或在企业网络的访问策略。
- 针对常见方案的故障排除步骤。

此外可以指我们的联机文档排查自助密码重置以了解最常见的 SSPR 方案的常规故障排除步骤。

## <a name="next-steps"></a>后续步骤

- [请考虑实施 Azure AD 密码保护](concept-password-ban-bad.md)

- [请考虑实施 Azure AD 的智能锁定](howto-password-smart-lockout.md)
