---
title: Azure AD Connect：通过分阶段推出进行云身份验证 |Microsoft Docs
description: 本文介绍如何使用分阶段推出从联合身份验证迁移到云身份验证。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/12/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fbe76fb18e33efaa161d2e2b488b48fa5c8580d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644153"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>使用分阶段推出迁移到云身份验证（预览）

使用分阶段推出方法，可以从联合身份验证迁移到云身份验证。 本文介绍如何进行这种切换。 但是，在开始分步推出之前，如果满足以下一个或多个条件，就应当考虑影响：
    
-  当前正在使用本地多重身份验证服务器。 
-  使用智能卡进行身份验证。 
-  当前服务器提供某些仅限联合的功能。

在尝试此功能之前，建议查看有关选择正确身份验证方法的指南。 有关详细信息，请参阅[为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)中的“比较方法”表。

有关功能概述，请参阅此“Azure Active Directory：什么是分阶段推出？” 视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>先决条件

-   你拥有具有联合域的 Azure Active Directory (Azure AD) 租户。

-   你已决定改用以下两个选项之一：
    - **选项 A** - 密码哈希同步（同步） + 无缝单一登录 (SSO)
    - **选项 B** - 直通身份验证 + 无缝 SSO
    
    尽管无缝 SSO 是可选的，但我们建议使用它，为运行着公司网络中加入域的计算机的用户提供无提示登录体验。

-   你已配置了要迁移到云身份验证的用户所需的所有相应租户品牌和条件访问策略。

-   如果计划使用 Azure 多重身份验证，建议使用[自助式密码重置 (SSPR) 和多重身份验证的聚合注册](../authentication/concept-registration-mfa-sspr-combined.md)，让你的用户注册其身份验证方法一次。

-   若要使用分阶段推出功能，你必须是租户的全局管理员。

-   若要在特定 Active Directory 林上启用无缝 SSO，你必须是域管理员。


## <a name="supported-scenarios"></a>支持的方案

分阶段推出支持以下场景。 该功能仅适用于：

- 使用 Azure AD Connect 预配到 Azure AD 的用户。 它不适用于仅限云的用户。

- 浏览器和新式身份验证客户端上的用户登录流量。 使用旧身份验证的应用程序或云服务将回退到联合身份验证流。 例如关闭了新式身份验证的 Exchange Online，或不支持新式身份验证的 Outlook 2010。
- 组大小当前限制为 50,000 个用户。  如果你拥有超过 50,000 个用户的组，建议将此组拆分为多个组以便分阶段推出。

## <a name="unsupported-scenarios"></a>不支持的方案

分阶段推出不支持以下场景：

- 某些应用程序在身份验证过程中会将“domain_hint”查询参数发送到 Azure AD。 这些流将继续运行，允许进行分阶段推出的用户将继续使用联合身份验证。

<!-- -->

- 管理员可以使用安全组来推出云身份验证。 为了避免在使用本地 Active Directory 安全组时出现同步延迟，建议使用云安全组。 下列条件适用：

    - 每个功能最多可以使用 10 个组。 也就是说，密码哈希同步、直通身份验证和无缝 SSO 各可以使用 10 个组。  
    - 不支持嵌套组。 此范围也适用于公共预览。
    - 分阶段推出不支持动态组。
    - 组内的联系人对象会阻止添加组。

- 仍需要使用 Azure AD Connect 或 PowerShell 进行从联合身份验证到云身份验证的最终转换。 分阶段部署不会将域从联合域切换到托管域。

- 首次为分阶段推出添加安全组时，限制于 200 个用户，以避免 UX 超时。添加组后，可以根据需要直接向其添加更多用户。

>[!NOTE]
> 由于租用终结点不发送登录提示，因此不支持对其进行分阶段推出。  SAML 应用程序使用租用终结点，也不支持分阶段推出。

## <a name="get-started-with-staged-rollout"></a>分阶段推出入门

若要使用分阶段推出测试密码哈希同步登录，请遵循下一节中的准备工作说明。

有关要使用的 PowerShell cmdlet 的信息，请参阅 [Azure AD 2.0 预览版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="pre-work-for-password-hash-sync"></a>密码哈希同步的准备工作

1. 从 ** Azure AD Connect [可选功能](how-to-connect-install-custom.md#optional-features)页启用密码哈希同步。 

   ![Azure Active Directory Connect 中“可选功能”页的屏幕截图](media/how-to-connect-staged-rollout/sr1.png)

1. 请确保已运行完整的密码哈希同步循环，将所有用户的密码哈希同步到 Azure AD。 若要检查密码哈希同步的状态，可以使用[通过 Azure AD Connect 同步排查密码哈希同步问题](tshoot-connect-password-hash-synchronization.md)中的 PowerShell 诊断。

   ![AADConnect 故障排除日志的屏幕截图](./media/how-to-connect-staged-rollout/sr2.png)

如果要使用分阶段推出测试直通身份验证登录，请按照下一节中的准备工作说明启用该功能。

## <a name="pre-work-for-pass-through-authentication"></a>直通身份验证的准备工作

1. 识别运行 Windows Server 2012 R2 或更高版本的服务器，你希望在该服务器上运行直通身份验证代理。 

   请勿选择 Azure AD Connect 服务器。 确保服务器已加入域，可以通过 Active Directory 对选定用户进行身份验证，并且可以在出站端口和 URL 上与 Azure AD 通信。 有关详细信息，请参阅[快速入门：Azure AD 无缝单一登录](how-to-connect-sso-quick-start.md)中的“步骤 1：检查先决条件”部分。

1. [下载 Azure AD Connect 身份验证代理](https://aka.ms/getauthagent)，并将其安装在服务器上。 

1. 若要启用 [高可用性](how-to-connect-sso-quick-start.md)，请在其他服务器上安装额外的身份验证代理。

1. 请确保已正确配置[智能锁定设置](../authentication/howto-password-smart-lockout.md)。 这样做有助于确保用户的本地 Active Directory 帐户不会被恶意行为者锁定。

建议无论为分阶段推出选择哪种登录方法（密码哈希同步或直通身份验证），都启用无缝 SSO。   若要启用无缝 SSO，请遵循下一节中的准备工作说明。

## <a name="pre-work-for-seamless-sso"></a>无缝 SSO 的准备工作

使用 PowerShell 在 ** Active Directory 林中启用无缝 SSO。 如果有多个 Active Directory 林，请分别为每个林启用它。仅对选中要进行分阶段推出的用户触发无缝 ** SSO。 这不会影响现有联合设置。

通过执行以下操作，启用无缝 SSO：

1. 登录到 Azure AD Connect 服务器。

2. 转到 ** %programfiles%\\Microsoft Azure Active Directory Connect 文件夹。

3. 使用以下命令导入无缝 SSO PowerShell 模块： 

   `Import-Module .\AzureADSSO.psd1`

4. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令将打开一个窗格，你可以在其中输入租户的全局管理员凭据。

5. 调用 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令将显示一个已在其上启用此功能的 Active Directory 林列表（请查看“域”列表）。 默认情况下，它在租户级别设置为 false。

   ![Windows PowerShell 输出示例](./media/how-to-connect-staged-rollout/sr3.png)

6. 调用 `$creds = Get-Credential`。 出现提示时，输入目标 Active Directory 林的域管理员凭据。

7. 调用 `Enable-AzureADSSOForest -OnPremCredentials $creds`。 此命令从本地域控制器为无缝 SSO 所需的 Active Directory 林创建 AZUREADSSOACC 计算机帐户。

8. 无缝 SSO 要求 URL 位于 Intranet 区域中。 若要使用组策略部署这些 URL，请参阅[快速入门：Azure AD 无缝单一登录](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)。

9. 如需完整的演练，还可以下载我们的无缝 SSO [部署计划](https://aka.ms/SeamlessSSODPDownload)。

## <a name="enable-staged-rollout"></a>启用分阶段推出

若要将特定功能（直通身份验证、密码哈希同步或无缝 SSO）推出到组中的一群用户，请遵循后续部分中的说明。  

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>在租户上启用特定功能的分阶段推出

可以推出下列选项之一：

- **选项 A** - 密码哈希同步 + 无缝 SSO
- **选项 B** - 直通身份验证 + 无缝 SSO
- **不支持** - 密码哈希同步 + 直通身份验证 + 无缝 SSO

请执行以下操作：

1. 要访问预览版 UX，请登录到 [Azure AD 门户](https://aka.ms/stagedrolloutux)。

2. 选择“为托管用户登录名启用分阶段推出(预览版)”链接。

   例如，如果想要启用选项 A，请将“密码哈希同步”和“无缝单一登录”控件滑动到“开”，如下图所示。  

   ![Azure AD Connect 页面](./media/how-to-connect-staged-rollout/sr4.png)

   ![“启用分阶段推出功能(预览版)”页面](./media/how-to-connect-staged-rollout/sr5.png)

3. 将组添加到该功能，以启用直通身份验证和无缝 SSO。  若要避免 UX 超时，请确保安全组最初仅包含至多 200 个成员。

   ![“管理密码哈希同步的组(预览版)”页面](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >将自动为组中成员启用分阶段推出。 分阶段推出不支持嵌套和动态组。

## <a name="auditing"></a>审核

对于为分阶段推出而执行的各种操作，已启用审核事件：

- 为密码哈希同步、直通身份验证或无缝 SSO 启用分阶段推出时审核事件。  

  >[!NOTE]
  >使用分阶段推出打开无缝 SSO 时，将记录审核事件。

  ![“为功能创建推出策略”窗格 -“活动”选项卡](./media/how-to-connect-staged-rollout/sr7.png)

  ![“为功能创建推出策略”窗格 -“已修改的属性”选项卡](./media/how-to-connect-staged-rollout/sr8.png)

- 将组添加到密码哈希同步、直通身份验证或无缝 SSO 时审核事件。  

  >[!NOTE]
  >将组添加到密码哈希同步以实现分阶段推出时，记录审核事件。

  ![“向功能推出添加组”窗格 -“活动”选项卡](./media/how-to-connect-staged-rollout/sr9.png)

  ![“向功能推出添加组”窗格 -“已修改的属性”选项卡](./media/how-to-connect-staged-rollout/sr10.png)

- 为添加到组中的用户启用分阶段推出时，审核事件。

  ![“向功能推出添加用户”窗格 -“活动”选项卡](media/how-to-connect-staged-rollout/sr11.png)

  ![“向功能推出添加用户”窗格 -“目标”选项卡](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>验证

若要测试密码哈希同步或直通身份验证登录（用户名和密码登录），请执行以下操作： 

1. 在 Extranet 上，在私密浏览器会话中转到[应用页面](https://myapps.microsoft.com)，然后输入已选择要进行分阶段推出的用户帐户的 UserPrincipalName (UPN)。

   不会将已设为分阶段推出目标的用户重定向到联合登录页。 而会在 Azure AD 租户品牌的登录页上要求其登录。

1. 使用 UserPrincipalName 进行筛选，确保登录名成功显示在 [Azure AD 登录活动报告](../reports-monitoring/concept-sign-ins.md)中。

测试无缝 SSO 登录：

1. 在 Intranet 上，在私密浏览器会话中转到[应用页面](https://myapps.microsoft.com)，然后输入已选择要进行分阶段推出的用户帐户的 UserPrincipalName (UPN)。

   对于已设为无缝 SSO 分步推出目标的用户，将显示“正在尝试登录…”消息，然后以无提示方式将其登录。

1. 使用 UserPrincipalName 进行筛选，确保登录名成功显示在 [Azure AD 登录活动报告](../reports-monitoring/concept-sign-ins.md)中。

   若要跟踪所选分阶段推出用户仍使用 Active Directory 联合身份验证服务 (AD FS) 进行的用户登录，请遵循 [AD FS 排除故障：事件和日志记录](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)中的说明。 查看有关如何在第三方联合身份验证提供程序上检查此项的供应商文档。

## <a name="remove-a-user-from-staged-rollout"></a>从分阶段推出中删除用户

从组中删除用户将为该用户禁用分阶段推出。 若要禁用分阶段推出功能，请将控件滑回“关”。

## <a name="frequently-asked-questions"></a>常见问题

**问：能否在生产中使用此功能？**

A:是的，可以在生产租户中使用此功能，但建议首先在测试租户中试用。

**问：此功能能否用于维持永久“共存状态”，即某些用户使用联合身份验证、其他用户使用云身份验证的情况？**

A:不能，此功能设计用于从联合身份验证分阶段迁移到云身份验证，最终会全部转换为云身份验证。 建议不要使用永久混合状态，因为这种方法可能导致意外的身份验证流。

**问：可以使用 PowerShell 执行分阶段推出吗？**

A:是的。 若要了解如何使用 PowerShell 执行分阶段推出，请参阅 [Azure AD 预览版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="next-steps"></a>后续步骤
- [Azure AD 2.0 预览版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
