---
title: 规划和执行的 Azure 多重身份验证部署的 Azure Active Directory
description: Microsoft Azure 多重身份验证部署规划
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c2c5006eb050b70b783ab8199724e0e98766381
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359296"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication"></a>规划基于云的 Azure 多重身份验证

用户要连接到组织中日益复杂的方案的资源。 从组织所拥有、 个人、 和公共设备打开和关闭使用智能手机、 平板电脑、 Pc 和便携式计算机，通常在多个平台上的公司网络连接的人员。 在这个始终连接、 多设备和多平台的世界，用户帐户的安全性是比以往更加重要。 无论其复杂性，跨设备、 网络和平台使用的密码，将不再不足以确保用户帐户的安全性，尤其是当用户往往会重复使用跨帐户的密码。 复杂的网络钓鱼和其他社交工程攻击可能导致在用户名和密码正在跨暗网上销售以及发布。

[Azure 多重身份验证 (MFA)](concept-mfa-howitworks.md)可帮助保护数据和应用程序的访问。 它提供了额外的安全使用第二个窗体的身份验证。 组织可以使用[条件性访问](../conditional-access/overview.md)才能使解决方案适合其特定需求。

## <a name="prerequisites"></a>必备组件

在开始之前部署的 Azure 多重身份验证，是应考虑的必备项。

| 场景 | 先决条件 |
| --- | --- |
| **仅限云的**采用新式身份验证的标识环境 | **没有其他系统必备任务** |
| **混合**身份验证方案 | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)部署和用户标识是同步的还是与本地 Active Directory 域服务与 Azure Active Directory 联合。 |
| 在本地的旧版应用程序发布用于云访问 | Azure AD[应用程序代理](../manage-apps/application-proxy.md)部署。 |
| 使用 Azure MFA 的 RADIUS 身份验证 | 一个[网络策略服务器 (NPS)](howto-mfa-nps-extension.md)部署。 |
| 用户具有 Microsoft Office 2010 或更早版本或 Apple Mail 适用于 iOS 11 或更早版本 | 升级到[Microsoft Office 2013 或更高版本](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o)和 Apple mail 适用于 iOS 12 或更高版本。 旧式身份验证协议不支持条件性访问。 |

## <a name="plan-user-rollout"></a>计划用户推出

MFA 推出计划应包括跟你支持的容量内的部署波次的试验部署。 通过将条件性访问策略应用于一组少量试运行用户在开始部署。 评估对试运行用户、 进程使用和注册行为影响之后, 可以将多个组添加到策略或将更多的用户添加到现有组中。

### <a name="user-communications"></a>用户进行通信

若要提示用户，在计划内通信中的有关即将发生的变化，Azure MFA 注册要求和任何必要的用户操作至关重要。 我们建议以与你的组织，例如通信、 更改管理或人力资源部门中的代表协同开发通信。

Microsoft 提供了[通信模板](https://aka.ms/mfatemplates)并[最终用户文档](../user-help/security-info-setup-signin.md)帮助草稿通信。 您可以将用户发送至[ https://myprofile.microsoft.com ](https://myprofile.microsoft.com)若要通过选择直接注册**安全信息**该页面上的链接。

## <a name="deployment-considerations"></a>部署注意事项

Azure 多重身份验证被部署的强制实施条件性访问策略。 一个[条件性访问策略](../conditional-access/overview.md)可以要求用户执行多重身份验证，如满足某些条件时：

* 所有用户、 特定用户、 组或已分配角色的成员
* 要访问特定的云应用程序
* 设备平台
* 设备状态
* 网络位置或地理上位于 IP 地址
* 客户端应用程序
* 登录风险 （需要标识保护）
* 合规的设备
* 混合 Azure AD 加入设备
* 批准的客户端应用程序
 

使用 [多重身份验证推出材料] 中的可自定义的海报和电子邮件模板以向你的组织推出多重身份验证。 (https://www.microsoft.com/en-us/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>结合条件访问启用多重身份验证

条件性访问策略强制注册，需取消注册的用户完成在首次登录，重要的安全注意事项的注册。


[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md)参与的注册策略以及向 Azure 多重身份验证情景的自动的风险检测和修正策略。 策略可以创建以遭到入侵的标识的威胁时强制更改密码或要求登录被视为有风险由以下时使用 MFA[事件](../reports-monitoring/concept-risk-events.md):

* 已泄漏凭据
* 从匿名 IP 地址登录
* 不可能前往异常位置
* 从不熟悉的位置登录
* 从受感染的设备登录
* 从具有可疑活动的 IP 地址登录

某些检测到的 Azure Active Directory Identity Protection 风险事件发生时实时和一些需要进行脱机处理。 管理员可以选择阻止表现出危险行为和手动修正，需要更改密码，或作为其条件性访问策略的一部分需要多重身份验证的用户。

## <a name="define-network-locations"></a>定义网络位置

我们建议组织使用条件性访问来定义其网络 using[命名位置](../conditional-access/location-condition.md#named-locations)。 如果你的组织使用 Identity Protection，请考虑使用基于风险的策略而不命名位置。

### <a name="configuring-a-named-location"></a>配置命名的位置

1. 打开**Azure Active Directory**在 Azure 门户中
2. 单击**条件性访问**
3. 单击**命名位置**
4. 单击**新位置**
5. 在中**名称**字段中，提供有意义的名称
6. 选择是否要定义使用 IP 范围或国家/地区的位置
   1. 如果使用的 IP 范围
      1. 决定是否标记为受信任的位置。 从受信任的位置登录可降低用户的登录风险。 如果知道输入的 IP 范围已建立且在组织中可信，则只能将标记为受信任此位置。
      2. 指定的 IP 范围
   2. 如果使用的国家/地区
      1. 展开下拉列表菜单并选择国家/地区或你想要为此命名位置定义的区域。
      2. 决定是否包含未知的区域。 未知区域是指无法映射到某国家/地区的 IP 地址。
7. 单击“创建” 

## <a name="plan-authentication-methods"></a>规划身份验证方法

管理员可以选择[身份验证方法](../authentication/concept-authentication-methods.md)他们想要将提供给用户。 请务必允许多个单个身份验证方法，以便用户有可用的备用方式，以防主要方法不可用。 管理员可以启用以下方法有：

### <a name="notification-through-mobile-app"></a>通过移动应用发送通知

推送通知发送到你的移动设备上的 Microsoft Authenticator 应用。 用户将查看通知并选择**批准**以完成验证。 通过移动应用的推送通知提供用户的干扰最少选项。 它们也是最可靠、 安全选项，因为它们使用的数据连接，而不是电话服务。

> [!NOTE]
> 如果你的组织有员工在工作或出差到中国**移动应用的通知**方法**Android 设备**在该国家/地区中不适用。 备用方法应可为这些用户。

### <a name="verification-code-from-mobile-app"></a>通过移动应用发送验证码

Microsoft Authenticator 应用之类的移动应用会生成新的 OATH 验证码每隔 30 秒。 用户将此验证码输入到登录界面中。 可以使用移动应用程序选项，电话具有数据或移动电话信号。

### <a name="call-to-phone"></a>拨打电话

自动的语音呼叫放置到用户。 用户接听电话和按下**#** 在电话键盘上批准身份验证。 电话呼叫是从移动应用通知还是验证码的出色备份方法。

### <a name="text-message-to-phone"></a>向手机发送短信

包含验证码的短信发送给用户，系统将提示用户在登录界面中输入验证码。

### <a name="choose-verification-options"></a>选择验证选项

1. 浏览至“Azure Active Directory”、“用户”、“多重身份验证”。

   ![从 Azure 门户中的“Azure AD 用户”边栏选项卡访问“多重身份验证”门户](media/howto-mfa-getstarted/users-mfa.png)

1. 在打开的新选项卡中，浏览至“服务设置”。
1. 在“验证选项”下，选中可供用户使用的方法旁的所有框。

   ![在多重身份验证服务设置选项卡中配置验证方法](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. 单击“保存” 。
1. 关闭“服务设置”选项卡。

## <a name="plan-registration-policy"></a>计划注册策略

管理员必须确定用户将如何注册其方法。 组织应[启用新的组合的注册体验](howto-registration-mfa-sspr-combined.md)针对 Azure MFA 和自助服务密码重置 (SSPR)。 SSPR，用户可以使用他们用于多重身份验证的相同方法以安全方式重置其密码。 我们建议通过结合注册，目前处于公共预览状态，因为它是用户，能够一次注册这两个服务提供出色体验。 为 SSPR 和 Azure MFA 启用的相同方法将允许用户注册为使用这两项功能。

### <a name="registration-with-identity-protection"></a>使用 Identity Protection 的注册

如果你的组织使用 Azure Active Directory Identity Protection[配置 MFA 注册策略](../identity-protection/howto-mfa-policy.md)以提示用户注册他们在下次登录的时以交互方式。

### <a name="registration-without-identity-protection"></a>注册而无需标识保护

如果你的组织没有启用 Identity Protection 许可证，会提示用户注册登录时需要进行 MFA 的下一次。 如果它们不使用应用程序使用 MFA 保护，用户可能未注册 mfa。 请务必获取的所有用户注册，以便猜出用户的密码并代表他们注册 MFA 不良参与方不能有效地执行控制的帐户。

#### <a name="enforcing-registration"></a>强制注册

使用以下步骤的条件性访问策略可以强制用户注册多重身份验证

1. 创建一个组添加当前未注册的所有用户。
2. 使用条件性访问，强制实施多重身份验证为此组的所有资源的访问权限。
3. 我们会定期重新评估组成员身份，并从组中删除已注册用户。

您可以使用依赖的 PowerShell 命令用标识已注册和未注册 Azure MFA 用户[MSOnline PowerShell 模块](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)。

#### <a name="identify-registered-users"></a>标识已注册的用户

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>标识非注册用户

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

## <a name="plan-conditional-access-policies"></a>计划条件性访问策略

若要计划条件性访问策略，这将确定 MFA 和其他控件需要时，请参阅[什么是 Azure Active Directory 中的条件访问？](../conditional-access/overview.md)。

非常重要，阻止无意中被你的 Azure AD 租户。 您可以减轻此意外丧失管理访问权限由[在你的租户中创建两个或多个紧急访问帐户](../users-groups-roles/directory-emergency-access.md)和条件访问策略中排除它们。

### <a name="create-conditional-access-policy"></a>创建条件访问策略

1. 使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure Active Directory**，**条件性访问**。
1. 选择“新策略”。
1. 为策略提供一个有意义的名称。
1. 在“用户和组”下：
   * 在“包括”选项卡上，选中“所有用户”单选按钮
   * 上**排除**选项卡上，选中的复选框**用户和组**选择紧急访问帐户。
   * 单击“完成”。
1. 在“云应用”下，选中“所有云应用”单选按钮。
   * 可选：在“排除”选项卡上，选择组织不需要对其执行 MFA 的云应用。
   * 单击“完成”。
1. 在“条件”部分下：
   * 可选：如果已启用 Azure Identity Protection，则可以选择在实施该策略的过程中评估登录风险。
   * 可选：如果已配置受信任的位置或命名的位置，则可以指定在策略中包括或排除这些位置。
1. 在“授予”下，确保选中“授权访问”单选按钮。
    * 选中“要求多重身份验证”复选框。
    * 单击“选择”。
1. 跳过“会话”部分。
1. 将“启用策略”开关设置为“开”。
1. 单击**创建**。

![创建条件访问策略，为试验组中的 Azure 门户用户启用 MFA](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>计划与在本地系统的集成

不直接与 Azure AD 验证某些在本地和旧版应用程序需要额外的步骤以使用 MFA 包括：

* 旧的本地应用程序，将需要使用应用程序代理。
* 本地 RADIUS 的应用程序，将需要使用 MFA 适配器与 NPS 服务器。
* 本地 AD FS 应用程序，将需要与 AD FS 2016 使用 MFA 适配器。

直接与 Azure AD 进行身份验证和新式身份验证 （WS 联合身份验证、 SAML、 OAuth、 OpenID Connect） 的应用程序可发出直接使用的条件性访问策略。

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>使用 Azure MFA 与 Azure AD 应用程序代理

通过应用程序本地驻留可以发布到 Azure AD 租户[Azure AD 应用程序代理](../manage-apps/application-proxy.md)和可以充分利用 Azure 多重身份验证如果它们已配置为使用 Azure AD 预身份验证。

这些应用程序受到实施 Azure 多重身份验证，就像任何其他 Azure AD 集成应用程序的条件性访问策略。

同样，如果所有的用户登录名强制实施，Azure 多重身份验证，在本地与 Azure AD 应用程序代理发布的应用程序将受到保护。

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>将 Azure 多重身份验证与网络策略服务器相集成

适用于 Azure MFA 的网络策略服务器 (NPS) 扩展可以使用现有的服务器将基于云的 MFA 功能添加到身份验证基础结构。 使用 NPS 扩展，可以将电话呼叫、 短信或电话应用验证添加到现有的身份验证流。 这种集成具有以下限制：

* 使用 CHAPv2 协议，支持仅身份验证器应用程序推送通知和语音呼叫。
* 不能应用条件性访问策略。

NPS 扩展充当 RADIUS 和基于云的 Azure MFA 提供身份验证的第二个因素，以保护之间的适配器[VPN](howto-mfa-nps-extension-vpn.md)，[远程桌面网关连接](howto-mfa-nps-extension-rdg.md)，或其他 RADIUS 支持应用程序。 用户在此环境中的注册 Azure MFA 将面对所有身份验证尝试的难题，条件性访问策略平均值 MFA 缺乏始终是必需的。

#### <a name="implementing-your-nps-server"></a>实现您的 NPS 服务器

如果必须部署 NPS 实例，在使用中，引用[将现有 NPS 基础结构与 Azure 多重身份验证集成](howto-mfa-nps-extension.md)。 如果您设置了 NPS 第一次，请参阅[网络策略服务器 (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)有关的说明。 可以在文章中找到故障排除指南[解决的 Azure 多重身份验证的 NPS 扩展的错误消息](howto-mfa-nps-extension-errors.md)。

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>未注册 MFA 的用户做好准备 NPS

选择未注册 mfa 的用户尝试进行身份验证时，会发生什么情况。 使用注册表设置`REQUIRE_USER_MATCH`注册表路径中`HKLM\Software\Microsoft\AzureMFA`来控制功能行为。 此设置有一个单一配置选项。

| 密钥 | 值 | 默认 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | 未设置（相当于 TRUE） |

此项设置的目的是确定当某个用户未注册 MFA 时要执行哪个操作。 下表中列出的更改此设置影响。

| 设置 | 用户 MFA 状态 | Effects |
| --- | --- | --- |
| 项不存在 | 未注册 | MFA 质询不成功 |
| 值设置为 True / 未设置 | 未注册 | MFA 质询不成功 |
| 密钥设置为 False | 未注册 | 而无需 MFA 的身份验证 |
| 密钥设置为 False 或 True | 注册 | 必须使用 MFA 进行身份验证 |

### <a name="integrate-with-active-directory-federation-services"></a>将与 Active Directory 联合身份验证服务集成

如果你的组织已与 Azure AD 联合，则可以使用[Azure 多重身份验证来保护 AD FS 资源](multi-factor-authentication-get-started-adfs.md)，同时在本地和云中。 Azure MFA，可降低密码并提供更安全的方式进行身份验证。 从 Windows Server 2016 开始，你现在可以配置 Azure MFA 进行主要身份验证。

与不同 Windows Server 2012 R2 中的 AD FS 与 AD FS 2016 Azure MFA 适配器直接与 Azure AD 集成并不需要在本地 Azure MFA 服务器。 Azure MFA 适配器内置于 Windows Server 2016，无需其他安装。

使用 Azure MFA 的 AD FS 2016 和目标应用程序需遵守条件性访问策略时，有一些其他注意事项：

* 与 AD FS 2016 联合到 Azure AD 信赖方应用程序时，都可以使用条件性访问。
* 当应用程序是信赖方对 AD FS 2016 和是托管或联合与 AD FS 2016 时，条件性访问不可用。
* 条件性访问还时不可用 AD FS 2016 配置为使用 Azure MFA 的主要身份验证方法。

#### <a name="ad-fs-logging"></a>AD FS 日志记录

标准 AD FS 2016 中的日志记录 Windows 安全日志和 AD FS 管理员日志中，包含有关身份验证请求及其成功或失败信息。 在这些事件的事件日志数据将指示是否使用了 Azure MFA。 例如，AD FS 审核事件 ID 1200 可能包含：

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>续订和管理证书

在每个 AD FS 服务器上，在本地计算机应用商店的 My，将有自签名的 Azure MFA 证书一节的 OU = Microsoft AD FS Azure MFA，其中包含证书到期日期。 检查以确定到期日期每个 AD FS 服务器上此证书的有效期。

如果即将过期，你的证书的有效期达到[生成并验证每个 AD FS 服务器上的新 MFA 证书](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)。

以下指南详细介绍了如何管理在 AD FS 服务器上的 Azure MFA 证书。 通过使用 Azure MFA 配置 AD FS 时，生成证书`New-AdfsAzureMfaTenantCertificate`PowerShell cmdlet 的有效期为 2 年。 续订和过期之前续订的证书安装到 ovoid MFA 服务中断。

## <a name="implement-your-plan"></a>实现你的计划

现在，在规划你的解决方案，可实现通过执行以下步骤：

1. 满足任何必要的先决条件
   1. 部署[Azure AD Connect](../hybrid/whatis-hybrid-identity.md)为任何混合方案
   1. 部署[Azure AD 应用程序代理](../manage-apps/application-proxy.md)对于对任何本地应用程序发布用于云访问
   1. 部署[NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)任何 RADIUS 身份验证
   1. 确保用户已启用新式身份验证与升级到支持的 Microsoft Office 版本
1. 配置所选[身份验证方法](#choose-verification-options)
1. 定义在[命名网络位置](../conditional-access/location-condition.md#named-locations)
1. 选择要开始推出 MFA 的组。
1. 配置你[条件性访问策略](#create-conditional-access-policy)
1. 配置 MFA 注册策略
   1. [组合的 MFA 和 SSPR](howto-registration-mfa-sspr-combined.md)
   1. 使用[标识保护](../identity-protection/howto-mfa-policy.md)
1. 发送用户进行通信并获取用户在注册 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [跟踪的用户注册](#identify-non-registered-users)

## <a name="manage-your-solution"></a>管理解决方案

适用于 Azure MFA 的报表

Azure 多重身份验证提供了通过 Azure 门户中的报表：

| 报表 | Location | 描述 |
| --- | --- | --- |
| 使用情况和欺诈警报 | Azure AD > 登录 | 提供有关总体使用情况、用户摘要和用户详细信息的信息；以及指定日期范围内提交的欺诈警报的历史记录。 |

## <a name="troubleshoot-mfa-issues"></a>对 MFA 问题进行故障排除

查找有关在 Azure MFA 的常见问题的解决方案[故障排除 Azure 多重身份验证文章](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)位于 Microsoft Support Center。

## <a name="next-steps"></a>后续步骤

* [有哪些身份验证方法？](concept-authentication-methods.md)
* [针对 Azure 多重身份验证和 Azure AD 自助密码重置启用聚合注册](concept-registration-mfa-sspr-converged.md)
* 为什么会提示或不会提示用户执行 MFA？ 请参阅[“Azure 多重身份验证中的报告”一文中的“Azure AD 登录报告”部分](howto-mfa-reporting.md#azure-ad-sign-ins-report)。
