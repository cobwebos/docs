---
title: 部署 Azure 多重身份验证-Azure Active Directory
description: Microsoft Azure 多重身份验证部署规划
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cab4b57513f90a5cbe80724d4937f8d0f87b5517
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381783"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>规划基于云的 Azure 多重身份验证部署

在日益复杂的方案中，人们正在连接到组织资源。 使用智能手机、平板电脑、Pc 和便携式计算机（通常在多个平台上），用户使用智能手机、平板电脑、Pc 和便携式计算机从组织拥有、个人和公共设备连接到企业网络。 在此始终连接的多设备和多平台环境中，用户帐户的安全性比以往更重要。 密码，无论是在设备、网络和平台上使用的复杂程度，都不再足以确保用户帐户的安全性，特别是当用户倾向于跨帐户重复使用密码时。 复杂的网络钓鱼和其他社会工程攻击可能导致在黑色 web 上发布和销售用户名和密码。

[Azure 多重身份验证（MFA）](concept-mfa-howitworks.md)可帮助保护对数据和应用程序的访问。 它使用第二种形式的身份验证提供额外的安全层。 组织可以使用[条件性访问](../conditional-access/overview.md)来使解决方案满足其特定需求。

## <a name="prerequisites"></a>先决条件

在开始部署 Azure 多重身份验证之前，需要考虑必备项。

| 应用场景 | 先决条件 |
| --- | --- |
| 具有新式身份验证的**仅限云**的标识环境 | **无其他先决条件任务** |
| **混合**标识方案 | 部署[Azure AD Connect](../hybrid/whatis-hybrid-identity.md) ，并且用户标识与 Azure Active Directory 的本地 Active Directory 域服务同步或联合。 |
| 为云访问发布的本地旧应用程序 | 已部署 Azure AD[应用程序代理](../manage-apps/application-proxy.md)。 |
| 将 Azure MFA 用于 RADIUS 身份验证 | 已部署[网络策略服务器（NPS）](howto-mfa-nps-extension.md) 。 |
| 用户 Microsoft Office 2010 或更早版本，或 Apple Mail for iOS 11 或更早版本 | 升级到[Microsoft Office 2013 或更高版本](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o)，以及适用于 iOS 12 或更高版本的 Apple mail。 旧身份验证协议不支持条件性访问。 |

## <a name="plan-user-rollout"></a>规划用户推出

你的 MFA 推出计划应包括一个试验部署，后跟支持容量中的部署波。 通过将条件访问策略应用于一小组试验用户，开始推出。 在评估对试验用户、使用的进程和注册行为的影响后，可以将更多的组添加到策略，或将更多的用户添加到现有组。

### <a name="user-communications"></a>用户通信

通知用户、计划内的更改、Azure MFA 注册要求以及任何必要的用户操作，这一点非常重要。 建议以与组织内部的代表（如通信、更改管理或人力资源部门）共同开发通信。

Microsoft 提供[通信模板](https://aka.ms/mfatemplates)和[最终用户文档](../user-help/security-info-setup-signin.md)来帮助草拟你的通信。 您可以通过选择该页面上的**安全信息**链接，将用户发送到[https://myprofile.microsoft.com](https://myprofile.microsoft.com)以直接注册。

## <a name="deployment-considerations"></a>部署注意事项

Azure 多重身份验证是通过使用条件访问实施策略来部署的。 [条件性访问策略](../conditional-access/overview.md)可能要求用户在满足某些条件时执行多重身份验证，例如：

* 所有用户、特定用户、组成员或分配的角色
* 要访问的特定云应用程序
* 设备平台
* 设备状态
* 网络位置或地理位置的 IP 地址
* 客户端应用程序
* 登录风险（需要标识保护）
* 合规的设备
* 混合 Azure AD 加入设备
* 批准的客户端应用程序

使用[多重身份验证推出材料](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)中的可自定义海报和电子邮件模板向组织推出多重身份验证。

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>结合条件访问启用多重身份验证

条件性访问策略强制实施注册，要求未注册的用户在首次登录时完成注册，这是一个重要的安全注意事项。

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md)同时为 Azure 多因素身份验证故事提供注册策略和自动风险检测和修正策略。 当存在被泄露的身份威胁时，可以创建策略来强制更改密码，或者在通过以下[事件](../reports-monitoring/concept-risk-events.md)认为登录有风险时要求进行 MFA：

* 已泄漏凭据
* 从匿名 IP 地址登录
* 不可能前往异常位置
* 从不熟悉的位置登录
* 从受感染的设备登录
* 从具有可疑活动的 IP 地址登录

Azure Active Directory Identity Protection 检测到的某些风险检测是实时发生的，一些则需要脱机处理。 管理员可以选择阻止显示有风险行为的用户和手动修正，要求更改密码，或者需要多重身份验证作为其条件访问策略的一部分。

## <a name="define-network-locations"></a>定义网络位置

建议组织使用条件性访问，通过[命名位置](../conditional-access/location-condition.md#named-locations)来定义其网络。 如果你的组织使用的是 Identity Protection，请考虑使用基于风险的策略，而不是命名位置。

### <a name="configuring-a-named-location"></a>配置命名位置

1. 打开 Azure 门户中的**Azure Active Directory**
2. 单击 "**条件访问**"
3. 单击 "**命名位置**"
4. 单击 "**新建位置**"
5. 在 "**名称**" 字段中，提供有意义的名称
6. 选择是使用 IP 范围还是国家/地区来定义位置
   1. 如果使用 IP 范围
      1. 决定是否将位置标记为受信任。 从受信任的位置登录可降低用户的登录风险。 如果你知道输入的 IP 范围已在组织中建立并可靠，则仅将此位置标记为受信任。
      2. 指定 IP 范围
   2. 如果使用国家/地区
      1. 展开下拉菜单，然后选择要为此命名位置定义的国家或地区。
      2. 决定是否包含未知区域。 未知区域是无法映射到国家/地区的 IP 地址。
7. 单击“创建”

## <a name="plan-authentication-methods"></a>规划身份验证方法

管理员可以选择他们想要供用户使用的[身份验证方法](../authentication/concept-authentication-methods.md)。 必须允许使用多种身份验证方法，以便用户在其主要方法不可用的情况下具有可用的备份方法。 管理员可以通过以下方法启用：

### <a name="notification-through-mobile-app"></a>通过移动应用发送通知

向移动设备上的 Microsoft Authenticator 应用发送推送通知。 用户查看通知并选择 "**批准**" 以完成验证。 通过移动应用推送通知可为用户提供最具侵入性的选项。 它们也是最可靠和最安全的选项，因为它们使用数据连接而不是电话服务。

> [!NOTE]
> 如果你的组织有员工在中国工作或出差，则通过**Android 设备**上的**移动应用方法发出的通知**在该国家/地区不起作用。 应为这些用户提供备用方法。

### <a name="verification-code-from-mobile-app"></a>通过移动应用发送验证码

移动应用（如 Microsoft Authenticator 应用）每隔30秒生成一次新的 OATH 验证码。 用户将此验证码输入到登录界面中。 无论手机是否有数据或手机信号，都可以使用 "移动应用" 选项。

### <a name="call-to-phone"></a>拨打电话

自动向用户发出语音呼叫。 用户需要接听电话，并按电话键盘上的 **#** 来批准其身份验证。 电话呼叫是适用于移动应用中的通知或验证码的一种很好的备份方法。

### <a name="text-message-to-phone"></a>向手机发送短信

将向用户发送包含验证码的短信，系统将提示用户在登录界面中输入验证码。

### <a name="choose-verification-options"></a>选择验证选项

1. 浏览至“Azure Active Directory”、“用户”、“多重身份验证”。

   ![从 Azure 门户中的“Azure AD 用户”边栏选项卡访问“多重身份验证”门户](media/howto-mfa-getstarted/users-mfa.png)

1. 在打开的新选项卡中，浏览至“服务设置”。
1. 在“验证选项”下，选中可供用户使用的方法旁的所有框。

   ![在多重身份验证服务设置选项卡中配置验证方法](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. 单击“保存”。
1. 关闭“服务设置”选项卡。

## <a name="plan-registration-policy"></a>计划注册策略

管理员必须确定用户注册其方法的方式。 组织应为 Azure MFA 和自助密码重置（SSPR）[启用新的组合注册体验](howto-registration-mfa-sspr-combined.md)。 SSPR 允许用户使用用于多重身份验证的相同方法，以安全的方式重置密码。 我们建议使用此组合注册（目前为公共预览版），因为这对于这两种服务都是一个很好的体验。 为 SSPR 和 Azure MFA 启用相同的方法将允许注册用户使用这两种功能。

### <a name="registration-with-identity-protection"></a>向 Identity Protection 注册

如果你的组织使用 Azure Active Directory Identity Protection，则[配置 MFA 注册策略](../identity-protection/howto-mfa-policy.md)，以提示用户在下次以交互方式登录时注册。

### <a name="registration-without-identity-protection"></a>无标识保护的注册

如果你的组织没有启用标识保护的许可证，则系统会提示用户在下次登录时注册 MFA。 如果用户不使用通过 MFA 保护的应用程序，则不能为 MFA 注册用户。 必须让所有用户都注册，使不良的执行组件不能猜测用户的密码并代表用户注册 MFA，从而有效控制帐户。

#### <a name="enforcing-registration"></a>强制注册

使用以下步骤可以强制用户注册多重身份验证

1. 创建组，添加当前未注册的所有用户。
2. 使用条件性访问，对此组强制执行多重身份验证，以访问所有资源。
3. 定期重新评估组成员身份，并从组中删除已注册的用户。

你可以通过依赖于[MSOnline powershell 模块](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)的 powershell 命令标识注册和非注册 Azure MFA 用户。

#### <a name="identify-registered-users"></a>标识注册用户

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>标识非注册用户

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>将用户从每用户 MFA 转换为基于条件访问的 MFA

如果你的用户已启用按用户启用并强制执行 Azure 多重身份验证，则以下 PowerShell 可帮助你将转换转换为基于 Azure 多重身份验证的条件性访问。

在 ISE 窗口或 "另存为" 中运行此 PowerShell。PS1 要本地运行的文件。

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> 我们最近更改了上述行为和 PowerShell 脚本。 以前，该脚本保存在 MFA 方法之外，禁用了 MFA，并还原了这些方法。 现在不再需要此操作，因为默认情况下禁用不会清除这些方法。

## <a name="plan-conditional-access-policies"></a>规划条件访问策略

若要规划确定何时需要 MFA 和其他控制的条件访问策略策略，请参阅[什么是 Azure Active Directory？中的条件性访问](../conditional-access/overview.md)。

必须防止无意中锁定 Azure AD 租户，这一点很重要。 可以通过[在租户中创建两个或更多紧急访问帐户](../users-groups-roles/directory-emergency-access.md)并将其从条件访问策略中排除，来减轻这种意外的管理访问权限。

### <a name="create-conditional-access-policy"></a>创建条件性访问策略

1. 使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory”、“条件访问”。
1. 选择“新策略”。
   ![创建条件性访问策略，以便在试点组中为 Azure 门户用户启用 MFA](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. 为策略提供一个有意义的名称。
1. 在“用户和组”下：
   * 在“包括”选项卡上，选中“所有用户”单选按钮
   * 在 "**排除**" 选项卡上，选中 "**用户和组**" 旁边的框，然后选择紧急访问帐户。
   * 单击“Done”（完成）。
1. 在“云应用”下，选中“所有云应用”单选按钮。
   * 可选：在“排除”选项卡上，选择组织不需要对其执行 MFA 的云应用。
   * 单击“Done”（完成）。
1. 在“条件”部分下：
   * 可选：如果已启用 Azure 标识保护，则可以选择在实施该策略的过程中评估登录风险。
   * 可选：如果已配置受信任的位置或命名的位置，则可以指定在策略中包括或排除这些位置。
1. 在“授予”下，确保选中“授权访问”单选按钮。
    * 选中“要求多重身份验证”复选框。
    * 单击“选择”。
1. 跳过“会话”部分。
1. 将“启用策略”开关设置为“开”。
1. 单击“**创建**”。

## <a name="plan-integration-with-on-premises-systems"></a>规划与本地系统的集成

某些不会直接对 Azure AD 进行身份验证的旧版应用程序和本地应用程序需要额外的步骤才能使用 MFA，其中包括：

* 旧的本地应用程序，需要使用应用程序代理。
* 本地 RADIUS 应用程序，需要将 MFA 适配器与 NPS 服务器一起使用。
* 本地 AD FS 应用程序，这些应用程序需要使用 AD FS 2016 或更高版本的 MFA 适配器。

直接使用 Azure AD 进行身份验证并具有新式身份验证（WS-FEDERATION、SAML、OAuth 和 OpenID Connect）的应用程序可以直接使用条件性访问策略。

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>将 Azure MFA 与 Azure AD 应用程序代理配合使用

驻留在本地的应用程序可以通过[Azure AD 应用程序代理](../manage-apps/application-proxy.md)发布到 Azure AD 租户，并可利用 Azure 多重身份验证（如果这些应用程序已配置为使用 Azure AD 预身份验证）。

这些应用程序遵循强制执行 Azure 多重身份验证的条件性访问策略，就像任何其他 Azure AD 集成的应用程序一样。

同样，如果对所有用户登录强制执行 Azure 多重身份验证，则使用 Azure AD 应用程序代理发布的本地应用程序将受到保护。

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>将 Azure 多重身份验证与网络策略服务器集成

适用于 Azure MFA 的网络策略服务器 (NPS) 扩展可以使用现有的服务器将基于云的 MFA 功能添加到身份验证基础结构。 通过 NPS 扩展，你可以将电话呼叫、短信或电话应用验证添加到现有的身份验证流。 此集成具有以下限制：

* 借助 CHAPv2 协议，仅支持验证器应用推送通知和语音呼叫。
* 不能应用条件访问策略。

NPS 扩展在 RADIUS 与基于云的 Azure MFA 之间充当适配器，以提供另一因素身份验证来保护[VPN](howto-mfa-nps-extension-vpn.md)、[远程桌面网关连接](howto-mfa-nps-extension-rdg.md)或其他支持 RADIUS 的应用程序。 在此环境中注册 Azure MFA 的用户将面临所有身份验证尝试的挑战，缺少条件性访问策略意味着始终需要 MFA。

#### <a name="implementing-your-nps-server"></a>实现 NPS 服务器

如果已部署了一个 NPS 实例并且已在使用，则引用将[现有 NPS 基础结构与 Azure 多重身份验证集成](howto-mfa-nps-extension.md)。 如果是第一次设置 NPS，请参阅[网络策略服务器（NPS）](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) ，了解相关说明。 可在[解决 Azure 多重身份验证的 NPS 扩展中的错误消息](howto-mfa-nps-extension-errors.md)一文中找到疑难解答指南。

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>为未注册 MFA 的用户准备 NPS

选择在未注册 MFA 的用户尝试进行身份验证时所发生的情况。 使用注册表路径中的注册表设置 `REQUIRE_USER_MATCH` `HKLM\Software\Microsoft\AzureMFA` 控制功能行为。 此设置包含一个配置选项。

| Key | 值 | 默认 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE/FALSE | 未设置（相当于 TRUE） |

此项设置的目的是确定当某个用户未注册 MFA 时要执行哪个操作。 下表列出了更改此设置的效果。

| 设置 | 用户 MFA 状态 | Effects |
| --- | --- | --- |
| 项不存在 | 未注册 | MFA 质询失败 |
| 值设置为 True/未设置 | 未注册 | MFA 质询失败 |
| 项设置为 False | 未注册 | 无 MFA 身份验证 |
| 键设置为 False 或 True | 参加 | 必须通过 MFA 进行身份验证 |

### <a name="integrate-with-active-directory-federation-services"></a>与 Active Directory 联合身份验证服务集成

如果你的组织与 Azure AD 联合，则可以使用[Azure 多重身份验证来保护](multi-factor-authentication-get-started-adfs.md)本地和云中 AD FS 资源。 Azure MFA 使你能够减少密码，并提供更安全的身份验证方式。 从 Windows Server 2016 开始，你现在可以将 Azure MFA 配置为进行主要身份验证。

与 Windows Server 2012 R2 中的 AD FS 不同，AD FS 2016 Azure MFA 适配器与 Azure AD 直接集成，无需本地 Azure MFA 服务器。 Azure MFA 适配器内置于 Windows Server 2016 中，无需额外安装。

将 Azure MFA 与 AD FS 2016 一起使用时，如果目标应用程序受条件性访问策略的限制，则需要考虑其他事项：

* 当应用程序是要 Azure AD 的依赖方、联合 AD FS 2016 或更高版本时，条件访问将可用。
* 当应用程序是信赖方 AD FS 2016 或 AD FS 2019 时，条件访问不可用，并使用 AD FS 2016 或 AD FS 2019 进行管理或联合。
* 如果 AD FS 2016 或 AD FS 2019 配置为使用 Azure MFA 作为主要身份验证方法，则条件访问也不可用。

#### <a name="ad-fs-logging"></a>AD FS 日志记录

Windows 安全日志和 AD FS 管理员日志中的标准 AD FS 2016 和2019日志记录均包含有关身份验证请求及其成功或失败的信息。 这些事件中的事件日志数据指示是否使用了 Azure MFA。 例如，AD FS 审核事件 ID 1200 可能包含：

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>续订和管理证书

在每个 AD FS 服务器上，在本地计算机的 "我的应用商店" 中，有一个名为 "OU = Microsoft AD FS Azure MFA" 的自签名 Azure MFA 证书，其中包含证书过期日期。 检查每个 AD FS 服务器上此证书的有效期，以确定到期日期。

如果证书有效期即将过期，请[在每个 AD FS 服务器上生成并验证新的 MFA 证书](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)。

以下指南详细介绍了如何管理 AD FS 服务器上的 Azure MFA 证书。 当你通过 Azure MFA 配置 AD FS 时，通过 `New-AdfsAzureMfaTenantCertificate` PowerShell cmdlet 生成的证书有效期为2年。 在过期之前，请续订并安装续订的证书，以在 MFA 服务中 ovoid 中断。

## <a name="implement-your-plan"></a>实现计划

现在，你已计划解决方案，可以按照以下步骤实现：

1. 满足任何必需的先决条件
   1. 为任何混合方案部署[Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
   1. 为云访问发布的任何本地应用上的部署[Azure AD 应用程序代理](../manage-apps/application-proxy.md)
   1. 部署用于任何 RADIUS 身份验证的[NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)
   1. 确保用户已升级到支持的 Microsoft Office 版本，并启用新式验证
1. 配置所选的[身份验证方法](#choose-verification-options)
1. 定义[命名的网络位置](../conditional-access/location-condition.md#named-locations)
1. 选择组以开始推出 MFA。
1. 配置[条件访问策略](#create-conditional-access-policy)
1. 配置 MFA 注册策略
   1. [合并的 MFA 和 SSPR](howto-registration-mfa-sspr-combined.md)
   1. 具有[Identity Protection](../identity-protection/howto-mfa-policy.md)
1. 发送用户通信并使用户注册[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [跟踪注册者](#identify-non-registered-users)

> [!TIP]
> 政府云用户可以注册[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>管理解决方案

Azure MFA 的报告

Azure 多重身份验证通过 Azure 门户提供报告：

| 报表 | 位置 | 说明 |
| --- | --- | --- |
| 使用情况和欺诈警报 | Azure AD > 登录 | 提供有关总体使用情况、用户摘要和用户详细信息的信息；以及指定日期范围内提交的欺诈警报的历史记录。 |

## <a name="troubleshoot-mfa-issues"></a>排查 MFA 问题

在 Microsoft 支持部门中心对 azure[多重身份验证进行故障排除一文](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)中的 azure MFA 查找常见问题的解决方案。

## <a name="next-steps"></a>后续步骤

* [有哪些身份验证方法？](concept-authentication-methods.md)
* [针对 Azure 多重身份验证和 Azure AD 自助密码重置启用聚合注册](concept-registration-mfa-sspr-converged.md)
* 为什么会提示或不会提示用户执行 MFA？ 请参阅[“Azure 多重身份验证中的报告”一文中的“Azure AD 登录报告”部分](howto-mfa-reporting.md#azure-ad-sign-ins-report)。
