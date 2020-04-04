---
title: 部署 Azure 多重身份验证 - Azure 活动目录
description: 微软 Azure 多重身份验证部署规划
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ae58482ced524958ffcdd6094ae57856d088eaf
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653955"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>规划基于云的 Azure 多重身份验证部署

在日益复杂的场景中，人们正在连接到组织资源。 人们使用智能手机、平板电脑、PC 和笔记本电脑（通常在多个平台上）在公司网络上和公司网络外使用组织拥有、个人和公共设备进行连接。 在这个始终连接、多设备和多平台的世界中，用户帐户的安全性比以往任何时候都更加重要。 跨设备、网络和平台使用的密码无论其复杂性如何，都已不足以确保用户帐户的安全性，尤其是在用户倾向于跨帐户重复使用密码时。 复杂的网络钓鱼和其他社交工程攻击可能导致用户名和密码在暗网中发布和销售。

[Azure 多重身份验证 （MFA）](concept-mfa-howitworks.md)有助于保护对数据和应用程序的访问。 它使用第二种身份验证形式提供额外的安全层。 组织可以使用[条件访问](../conditional-access/overview.md)使解决方案满足其特定需求。

## <a name="prerequisites"></a>先决条件

在开始部署 Azure 多重身份验证之前，应考虑一些先决条件项。

| 方案 | 先决条件 |
| --- | --- |
| 具有现代身份验证**的仅限云**的标识环境 | **无其他先决条件任务** |
| **混合**标识方案 | 部署[Azure AD 连接](../hybrid/whatis-hybrid-identity.md)，并且用户标识与具有 Azure 活动目录的本地活动目录域服务同步或联合。 |
| 为云访问而发布的本地旧版应用程序 | 已部署 Azure AD[应用程序代理](../manage-apps/application-proxy.md)。 |
| 将 Azure MFA 与 RADIUS 身份验证一起使用 | 部署了[网络策略服务器 （NPS）。](howto-mfa-nps-extension.md) |
| 用户拥有 Microsoft Office 2010 或更早版本，或 iOS 11 或更早的 Apple 邮件 | 升级到[Microsoft Office 2013 或更高版本](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o)，以及适用于 iOS 12 或更高版本的 Apple 邮件。 旧身份验证协议不支持条件访问。 |

## <a name="plan-user-rollout"></a>计划用户推出

您的 MFA 推出计划应包括试验部署，然后是支持能力范围内的部署波。 通过将条件访问策略应用于一小群试点用户来开始推出。 评估对试点用户、使用的进程和注册行为的影响后，可以将更多组添加到策略中，也可以向现有组添加更多用户。

### <a name="user-communications"></a>用户通信

在计划的通信中，向用户通报即将进行的更改、Azure MFA 注册要求以及任何必要的用户操作至关重要。 我们建议与组织内部的代表（如通信、变更管理或人力资源部门）协调开发通信。

Microsoft 提供[通信模板](https://aka.ms/mfatemplates)和[最终用户文档](../user-help/security-info-setup-signin.md)，以帮助起草您的通信。 您可以通过选择该页面上[https://myprofile.microsoft.com](https://myprofile.microsoft.com)**的安全信息**链接将用户发送到直接注册。

## <a name="deployment-considerations"></a>部署注意事项

通过强制实施具有条件访问的策略来部署 Azure 多重身份验证。 [条件访问策略](../conditional-access/overview.md)可以要求用户在满足某些条件时执行多重身份验证，例如：

* 所有用户、特定用户、组成员或分配的角色
* 正在访问的特定云应用程序
* 设备平台
* 设备状态
* 网络位置或地理位置 IP 地址
* 客户端应用程序
* 登录风险（需要身份保护）
* 合规的设备
* 混合 Azure AD 加入设备
* 已批准的客户端申请

在[多重身份验证推出材料](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)中使用可自定义的海报和电子邮件模板向您的组织推出多重身份验证。

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>结合条件访问启用多重身份验证

条件访问策略强制注册，要求未注册的用户在第一次登录时完成注册，这是一个重要的安全考虑。

[Azure AD 标识保护](../identity-protection/howto-configure-risk-policies.md)为 Azure 多重身份验证情景提供了注册策略和自动风险检测和补救策略。 当存在身份泄露的威胁时，可以创建策略以强制更改密码，或者当登录被以下[事件](../reports-monitoring/concept-risk-events.md)视为风险时，需要 MFA：

* 已泄漏凭据
* 从匿名 IP 地址登录
* 不可能前往异常位置
* 从不熟悉的位置登录
* 从受感染的设备登录
* 来自具有可疑活动的 IP 地址的登录

Azure 活动目录标识保护检测到的某些风险检测是实时进行的，有些需要脱机处理。 管理员可以选择阻止表现出危险行为并手动修复、需要更改密码或需要多重身份验证的用户，作为条件访问策略的一部分。

## <a name="define-network-locations"></a>定义网络位置

我们建议组织使用条件访问使用[命名位置](../conditional-access/location-condition.md#named-locations)定义其网络。 如果您的组织正在使用标识保护，请考虑使用基于风险的策略，而不是指定位置。

### <a name="configuring-a-named-location"></a>配置命名位置

1. 在 Azure 门户中打开**Azure 活动目录**
2. 选择“安全性”****
3. 在 **"管理**"下，选择**命名位置**
4. 选择**新位置**
5. 在 **"名称"** 字段中，提供有意义的名称
6. 选择是使用*IP 范围*或*国家/地区*定义位置
   1. 如果使用*IP 范围*
      1. 决定是否*将标记为受信任位置*。 从受信任的位置登录可降低用户的登录风险。 仅当您知道输入的 IP 范围在组织中已建立且可信时，才将此位置标记为可信。
      2. 指定 IP 范围
   2. 如果使用*国家/地区*
      1. 展开下拉菜单，并选择要为此命名位置定义的国家或地区。
      2. 决定是否*包括未知区域*。 未知区域是无法映射到国家/地区 IP 地址。
7. 选择 **"创建"**

## <a name="plan-authentication-methods"></a>计划身份验证方法

管理员可以选择他们希望提供给用户的[身份验证方法](../authentication/concept-authentication-methods.md)。 请务必允许多个身份验证方法，以便用户在其主方法不可用时具有可用的备份方法。 以下方法可供管理员启用：

### <a name="notification-through-mobile-app"></a>通过移动应用发送通知

推送通知将发送到移动设备上的 Microsoft 身份验证器应用。 用户查看通知并选择 **"批准"** 以完成验证。 通过移动应用推送通知为用户提供了最少的侵入性选项。 它们也是最可靠和安全的选项，因为它们使用数据连接而不是电话。

> [!NOTE]
> 如果您的组织有工作人员在中国工作或前往中国，则**通过 Android 设备上****的移动应用方法通知**在该国不起作用。 应为这些用户提供替代方法。

### <a name="verification-code-from-mobile-app"></a>通过移动应用发送验证码

像 Microsoft 身份验证器应用这样的移动应用每 30 秒生成一个新的 OATH 验证码。 用户将此验证码输入到登录界面中。 无论手机是否有数据或蜂窝信号，都可以使用移动应用选项。

### <a name="call-to-phone"></a>拨打电话

向用户发出自动语音呼叫。 用户应答呼叫，并在电话键盘**#** 上按压以批准其身份验证。 拨打电话是一种从移动应用通知或验证代码的备份方法。

### <a name="text-message-to-phone"></a>向手机发送短信

包含验证码的文本消息将发送给用户，系统将提示用户将验证码输入登录界面。

### <a name="choose-verification-options"></a>选择验证选项

1. 浏览至“Azure Active Directory”****、“用户”****、“多重身份验证”****。

   ![从 Azure 门户中的“Azure AD 用户”边栏选项卡访问“多重身份验证”门户](media/howto-mfa-getstarted/users-mfa.png)

1. 在打开的新选项卡中，浏览至“服务设置”****。
1. 在“验证选项”下，选中可供用户使用的方法旁的所有框****。

   ![在多重身份验证服务设置选项卡中配置验证方法](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. 单击"**保存**"。
1. 关闭“服务设置”选项卡****。

## <a name="plan-registration-policy"></a>计划注册政策

管理员必须确定用户如何注册其方法。 组织应启用 Azure MFA 和自助服务密码重置 （SSPR）[的新组合注册体验](howto-registration-mfa-sspr-combined.md)。 SSPR 允许用户使用与用于多重身份验证的相同方法以安全方式重置密码。 我们建议此组合注册（当前在公共预览版中），因为它对用户来说是一种很好的体验，能够为这两种服务注册一次。 为 SSPR 和 Azure MFA 启用相同的方法将允许注册用户使用这两种功能。

### <a name="registration-with-identity-protection"></a>身份保护注册

如果您的组织正在使用 Azure 活动目录标识保护，[请配置 MFA 注册策略](../identity-protection/howto-mfa-policy.md)，以提示用户下次以交互方式登录时进行注册。

### <a name="registration-without-identity-protection"></a>无身份保护的注册

如果您的组织没有启用身份保护的许可证，系统将提示用户下次在登录时注册 MFA。 如果用户不使用受 MFA 保护的应用程序，则可能无法注册 MFA。 注册所有用户非常重要，以便不法分子无法猜测用户的密码并代表他们注册 MFA，从而有效地控制帐户。

#### <a name="enforcing-registration"></a>强制执行注册

使用以下步骤，条件访问策略可以强制用户注册多重身份验证

1. 创建组，添加当前未注册的所有用户。
2. 使用条件访问，为此组强制实施多重身份验证以访问所有资源。
3. 定期重新评估组成员身份，并删除从组注册的用户。

您可以使用依赖于[MSOnline PowerShell 模块](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)的 PowerShell 命令标识已注册和非注册的 Azure MFA 用户。

#### <a name="identify-registered-users"></a>识别注册用户

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>识别非注册用户

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>将用户从每个用户 MFA 转换为基于条件访问的 MFA

如果用户启用了每个用户并强制执行了 Azure 多重身份验证，则以下 PowerShell 可以帮助您转换为基于条件访问的 Azure 多重身份验证。

在 ISE 窗口中运行此 PowerShell 或另存为 。要在本地运行的 PS1 文件。

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
> 我们最近相应地更改了上述行为和 PowerShell 脚本。 以前，脚本保存了 MFA 方法、禁用 MFA 并还原了这些方法。 现在，禁用的默认行为未清除方法，这不再需要。

## <a name="plan-conditional-access-policies"></a>计划条件访问策略

要规划条件访问策略策略（这将确定何时需要 MFA 和其他控件），请参阅[Azure 活动目录中的条件访问是什么？](../conditional-access/overview.md)

请务必防止无意中被锁定在 Azure AD 租户之外。 通过在[租户中创建两个或多个紧急访问帐户](../users-groups-roles/directory-emergency-access.md)并将其从条件访问策略中排除，可以减轻这种意外缺乏管理访问权限的影响。

### <a name="create-conditional-access-policy"></a>创建条件访问策略

1. 使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure 活动目录** > **安全** > **条件访问**。
1. 选择“新策略”****。
   ![创建条件访问策略，为试验组中的 Azure 门户用户启用 MFA](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. 为策略提供一个有意义的名称。
1. 在“用户和组”下****：
   * 在“包括”选项卡上，选中“所有用户”单选按钮********
   * 在 **"排除**"选项卡上，选中 **"用户和组**"复选框，然后选择紧急访问帐户。
   * 单击 **“完成”**。
1. 在“云应用”下，选中“所有云应用”单选按钮********。
   * 可选：在“排除”选项卡上，选择组织不需要对其执行 MFA 的云应用。****
   * 单击 **“完成”**。
1. 在“条件”部分下****：
   * 可选：如果已启用 Azure 标识保护，则可以选择在实施该策略的过程中评估登录风险。
   * 可选：如果已配置受信任的位置或命名的位置，则可以指定在策略中包括或排除这些位置。
1. 在“授予”下，确保选中“授权访问”单选按钮********。
    * 选中“要求多重身份验证”复选框****。
    * 单击“选择”。
1. 跳过“会话”**** 部分。
1. 将“启用策略”**** 开关设置为“开”****。
1. 单击“创建”。 

## <a name="plan-integration-with-on-premises-systems"></a>计划与本地系统的集成

某些不直接针对 Azure AD 进行身份验证的旧版和本地应用程序需要使用 MFA 的其他步骤，包括：

* 旧式本地应用程序，需要使用应用程序代理。
* 本地 RADIUS 应用程序，需要将 MFA 适配器与 NPS 服务器一起使用。
* 本地 AD FS 应用程序，需要将 MFA 适配器与 AD FS 2016 或更高版本一起使用。

直接使用 Azure AD 进行身份验证并具有现代身份验证（WS-Fed、SAML、OAuth、OpenID Connect）的应用程序可以直接使用条件访问策略。

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>将 Azure MFA 与 Azure AD 应用程序代理一起使用

驻留在本地的应用程序可以通过[Azure AD 应用程序代理](../manage-apps/application-proxy.md)发布到 Azure AD 租户，如果它们配置为使用 Azure AD 预身份验证，则可以利用 Azure 多重身份验证。

这些应用程序受条件访问策略的约束，这些策略强制实施 Azure 多重身份验证，就像任何其他 Azure AD 集成应用程序一样。

同样，如果对所有用户登录强制实施 Azure 多重身份验证，则使用 Azure AD 应用程序代理发布的本地应用程序将受到保护。

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>将 Azure 多重身份验证与网络策略服务器集成

适用于 Azure MFA 的网络策略服务器 (NPS) 扩展可以使用现有的服务器将基于云的 MFA 功能添加到身份验证基础结构。 使用 NPS 分机，您可以将电话呼叫、短信或电话应用验证添加到现有身份验证流中。 此集成具有以下限制：

* 使用 CHAPv2 协议，仅支持身份验证器应用推送通知和语音呼叫。
* 无法应用条件访问策略。

NPS 扩展充当 RADIUS 和基于云的 Azure MFA 之间的适配器，以提供第二个身份验证因子，以保护[VPN、](howto-mfa-nps-extension-vpn.md)[远程桌面网关连接](howto-mfa-nps-extension-rdg.md)或其他支持 RADIUS 的应用程序。 在此环境中注册 Azure MFA 的用户将对所有身份验证尝试提出质疑，缺少条件访问策略意味着始终需要 MFA。

#### <a name="implementing-your-nps-server"></a>实现 NPS 服务器

如果已部署和正在使用的 NPS 实例，请参阅[将现有的 NPS 基础结构与 Azure 多重身份验证集成](howto-mfa-nps-extension.md)。 如果您是首次设置 NPS，请参阅[网络策略服务器 （NPS）](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)获得说明。 故障排除指南可在"[解决来自 Azure 多重身份验证的 NPS 扩展名中的错误消息](howto-mfa-nps-extension-errors.md)"一文中找到。

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>为未注册 MFA 的用户准备 NPS

选择未注册 MFA 的用户尝试进行身份验证时发生的情况。 使用注册表路径`HKLM\Software\Microsoft\AzureMFA`中的`REQUIRE_USER_MATCH`注册表设置来控制功能行为。 此设置具有单个配置选项。

| 密钥 | 值 | 默认 |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | 真/假 | 未设置（相当于 TRUE） |

此项设置的目的是确定当某个用户未注册 MFA 时要执行哪个操作。 下表列出了更改此设置的效果。

| 设置 | 用户 MFA 状态 | 效果 |
| --- | --- | --- |
| 密钥不存在 | 未注册 | MFA 挑战不成功 |
| 值设置为 True / 未设置 | 未注册 | MFA 挑战不成功 |
| 密钥设置为"错误" | 未注册 | 没有 MFA 的身份验证 |
| 密钥设置为"错误"或"真" | 已注册 | 必须使用 MFA 进行身份验证 |

### <a name="integrate-with-active-directory-federation-services"></a>与活动目录联合服务集成

如果组织与 Azure AD 联合，则可以使用[Azure 多重身份验证来保护本地](multi-factor-authentication-get-started-adfs.md)和云中的 AD FS 资源。 Azure MFA 使您能够减少密码并提供更安全的身份验证方式。 从 Windows 服务器 2016 开始，现在可以为主身份验证配置 Azure MFA。

与 Windows 服务器 2012 R2 中的 AD FS 不同，AD FS 2016 Azure MFA 适配器与 Azure AD 直接集成，不需要本地 Azure MFA 服务器。 Azure MFA 适配器内置于 Windows Server 2016 中，无需进行其他安装。

将 Azure MFA 与 AD FS 2016 一起使用时，目标应用程序受条件访问策略的约束，还有其他注意事项：

* 当应用程序是 Azure AD 的依托方，与 AD FS 2016 或更高版本联合时，条件访问可用。
* 当应用程序是 AD FS 2016 或 AD FS 2019 的依托方，并且与 AD FS 2016 或 AD FS 2019 进行管理或联合时，条件访问不可用。
* 当 AD FS 2016 或 AD FS 2019 配置为使用 Azure MFA 作为主要身份验证方法时，条件访问也不可用。

#### <a name="ad-fs-logging"></a>AD FS 日志记录

标准 AD FS 2016 和 2019 登录 Windows 安全日志和 AD FS 管理日志，包含有关身份验证请求及其成功或失败的信息。 这些事件中的事件日志数据将指示是否使用了 Azure MFA。 例如，AD FS 审核事件 ID 1200 可能包含：

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>续订和管理证书

在每个 AD FS 服务器上，在本地计算机"我的应用商店"中，将有一个名为 OU_Microsoft AD FS Azure MFA 的自签名 Azure MFA 证书，其中包含证书到期日期。 检查每个 AD FS 服务器上的此证书的有效期，以确定到期日期。

如果证书的有效期接近过期，请在每个 AD [FS 服务器上生成并验证新的 MFA 证书](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)。

以下指南详细介绍了如何在 AD FS 服务器上管理 Azure MFA 证书。 使用 Azure MFA 配置 AD FS 时，`New-AdfsAzureMfaTenantCertificate`通过 PowerShell cmdlet 生成的证书的有效期为 2 年。 在过期之前续订并安装续订的证书，以在 MFA 服务中取消中断。

## <a name="implement-your-plan"></a>实施您的计划

现在，您已经规划了解决方案，您可以通过以下步骤实现：

1. 满足任何必要的先决条件
   1. 为任何混合方案部署[Azure AD 连接](../hybrid/whatis-hybrid-identity.md)
   1. 在为云访问发布的任何本地应用上部署[Azure AD 应用程序代理](../manage-apps/application-proxy.md)
   1. 部署[NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)进行任何 RADIUS 身份验证
   1. 确保用户已升级到启用了现代身份验证的 Microsoft Office 支持的版本
1. 配置选定的[身份验证方法](#choose-verification-options)
1. 定义[指定的网络位置](../conditional-access/location-condition.md#named-locations)
1. 选择要开始推出 MFA 的组。
1. 配置[条件访问策略](#create-conditional-access-policy)
1. 配置 MFA 注册策略
   1. [联合多边基金和SSPR](howto-registration-mfa-sspr-combined.md)
   1. 带[身份保护](../identity-protection/howto-mfa-policy.md)
1. 发送用户通信并让用户注册[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [跟踪注册者](#identify-non-registered-users)

> [!TIP]
> 政府云用户可以注册[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>管理解决方案

Azure MFA 的报告

Azure 多重身份验证通过 Azure 门户提供报告：

| 报表 | 位置 | 说明 |
| --- | --- | --- |
| 使用情况和欺诈警报 | Azure AD > 登录 | 提供有关总体使用情况、用户摘要和用户详细信息的信息；以及指定日期范围内提交的欺诈警报的历史记录。 |

## <a name="troubleshoot-mfa-issues"></a>排除 MFA 问题

在 Microsoft 支持中心的["排除 Azure 多重身份验证"一文](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)中查找与 Azure MFA 的常见问题的解决方案。

## <a name="next-steps"></a>后续步骤

* [有哪些身份验证方法？](concept-authentication-methods.md)
* [针对 Azure 多重身份验证和 Azure AD 自助密码重置启用聚合注册](concept-registration-mfa-sspr-converged.md)
* 为什么会提示或不会提示用户执行 MFA？ 请参阅[“Azure 多重身份验证中的报告”一文中的“Azure AD 登录报告”部分](howto-mfa-reporting.md#azure-ad-sign-ins-report)。
