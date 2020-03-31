---
title: Azure AD 连接：通过分阶段推出进行云身份验证 |微软文档
description: 本文介绍如何使用分阶段推出从联合身份验证迁移到云身份验证。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74915235"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>使用分阶段推出（预览）迁移到云身份验证

通过使用分阶段的推出方法，可以从联合身份验证迁移到云身份验证。 本文讨论了如何进行切换。 但是，在开始分阶段推出之前，如果以下一个或多个条件为 true，则应考虑其影响：
    
-  您当前正在使用本地多因素身份验证服务器。 
-  您使用的是智能卡进行身份验证。 
-  当前服务器提供某些仅联合功能。

在尝试此功能之前，我们建议您查看我们的指南，选择正确的身份验证方法。 有关详细信息，请参阅[选择 Azure 活动目录混合标识解决方案的正确身份验证方法中的](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)"比较方法"表。

有关该功能的概述，请查看此"Azure 活动目录：什么是暂存部署？ 视频：

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>先决条件

-   您具有具有联合域的 Azure 活动目录 （Azure AD） 租户。

-   您已决定转到两个选项之一：
    - **选项** - *密码哈希同步（同步）* + *无缝单一登录 （SSO）*
    - **选项 B** - *传递身份验证* + *无缝 SSO*
    
    尽管*无缝 SSO*是可选的，但我们建议启用它，以便从公司网络内运行域加入计算机的用户实现静默登录体验。

-   您已为迁移到云身份验证的用户配置了所需的所有适当的租户品牌和条件访问策略。

-   如果计划使用 Azure 多重身份验证，我们建议您使用[融合注册进行自助服务密码重置 （SSPR） 和多重身份验证](../authentication/concept-registration-mfa-sspr-combined.md)，以便用户注册一次身份验证方法。

-   要使用暂存的推出功能，您需要是租户上的全局管理员。

-   要在特定活动目录林上启用*无缝 SSO，* 您需要是域管理员。

## <a name="supported-scenarios"></a>支持的方案

以下方案支持分阶段推出。 该功能仅适用于：

- 使用 Azure AD 连接预配到 Azure AD 的用户。 它不适用于仅云用户。

- 浏览器*和现代身份验证*客户端上的用户登录流量。 使用旧版身份验证的应用程序或云服务将回退到联合身份验证流。 例如，关闭现代身份验证的 Exchange 联机或不支持现代身份验证的 Outlook 2010。

## <a name="unsupported-scenarios"></a>不支持的方案

暂存的推出不支持以下方案：

- 某些应用程序在身份验证期间将"domain_hint"查询参数发送到 Azure AD。 这些流将继续，启用暂存部署的用户将继续使用联合身份验证。

<!-- -->

- 管理员可以使用安全组推出云身份验证。 为了避免在使用本地活动目录安全组时出现同步延迟，我们建议您使用云安全组。 下列条件适用：

    - 每个要素最多可以使用 10 个组。 也就是说，您可以使用 10 个组进行*密码哈希同步*、*传递身份验证*和*无缝 SSO。*
    - *不支持嵌套*组。 此范围也适用于公共预览版。
    - 暂存推出*不支持*动态组。
    - 组内的联系人对象将阻止添加该组。

- 您仍然需要使用 Azure AD 连接或 PowerShell 进行从联合身份验证到云身份验证的最终选择。 分阶段推出不会将域从联合切换到托管域。

- 首次为分阶段推出添加安全组时，将限制为 200 个用户，以避免 UX 超时。添加组后，可以根据需要直接向其添加更多用户。

## <a name="get-started-with-staged-rollout"></a>开始分阶段推出

要使用暂存部署来测试*密码哈希同步*登录，请按照下一节中的前期说明进行操作。

有关要使用的 PowerShell cmdlet 的信息，请参阅[Azure AD 2.0 预览](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="pre-work-for-password-hash-sync"></a>密码哈希同步的预热工作

1. 从 Azure AD Connect 中的 [可选功能](how-to-connect-install-custom.md#optional-features) 页启用 *密码哈希同步* 。 

   ![Azure 活动目录连接中"可选功能"页面的屏幕截图](media/how-to-connect-staged-rollout/sr1.png)

1. 确保已运行完整的*密码哈希同步*周期，以便所有用户的密码哈希已同步到 Azure AD。 要检查*密码哈希同步*的状态，可以使用"[疑难解答"密码哈希同步中的](tshoot-connect-password-hash-synchronization.md)PowerShell 诊断与 Azure AD 连接同步。

   ![AADConnect 故障排除日志的屏幕截图](./media/how-to-connect-staged-rollout/sr2.png)

如果要使用暂存部署来测试*直通身份验证*登录，请按照下一节中的前期说明启用它。

## <a name="pre-work-for-pass-through-authentication"></a>传递身份验证的前期工作

1. 标识运行 Windows Server 2012 R2 或更高版本的服务器，您希望*传递身份验证*代理运行的位置。 

   *不要*选择 Azure AD 连接服务器。确保服务器已加入域，可以使用 Active Directory 对选定的用户进行身份验证，并且可以在出站端口和 URL 上与 Azure AD 通信。 有关详细信息，请参阅[快速入门：Azure AD 无缝单一登录](how-to-connect-sso-quick-start.md)的"步骤 1：检查先决条件"部分。

1. [下载 Azure AD 连接身份验证代理](https://aka.ms/getauthagent)，并将其安装到服务器上。 

1. 要启用 [高可用性](how-to-connect-sso-quick-start.md)，请在其他服务器上安装其他身份验证代理。

1. 确保已正确配置[智能锁定设置](../authentication/howto-password-smart-lockout.md)。 这样做有助于确保用户的本地活动目录帐户不会被不法分子锁定。

我们建议启用*无缝的 SSO，* 而不考虑您为分阶段推出选择的登录方法（*密码哈希同步*或*传递身份验证*）。 要启用*无缝的 SSO，* 请按照下一节中的前期说明进行操作。

## <a name="pre-work-for-seamless-sso"></a>无缝 SSO 的前期工作

使用 PowerShell 在活动目录林上启用 *无缝 SSO。*  如果您有多个活动目录林，请为每个林单独启用它。 *无缝 SSO*仅针对选择分阶段推出的用户触发。 它不会影响现有的联合设置。

通过执行以下操作实现*无缝 SSO：*

1. 登录到 Azure AD 连接服务器。

2. 转到 *%程序文件%\\Microsoft Azure 活动目录连接* 文件夹。

3. 通过运行以下命令导入*无缝的 SSO* PowerShell 模块： 

   `Import-Module .\AzureADSSO.psd1`

4. 以管理员身份运行 PowerShell。 在 PowerShell `New-AzureADSSOAuthenticationContext`中，调用 。 此命令将打开一个窗格，您可以在其中输入租户的全局管理员凭据。

5. 致电 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令显示已启用此功能的活动目录林的列表（请参阅"域"列表）。 默认情况下，它在租户级别设置为 false。

   ![Windows 电源外壳输出的示例](./media/how-to-connect-staged-rollout/sr3.png)

6. 致电 `$creds = Get-Credential`。 在提示符处，输入预期的活动目录林的域管理员凭据。

7. 调用 `Enable-AzureADSSOForest -OnPremCredentials $creds`。 此命令从活动目录林的本地域控制器创建 AZUREADSSOACC 计算机帐户，这是*无缝 SSO*所需的。

8. *无缝 SSO*要求 URL 位于 Intranet 区域中。 要使用组策略部署这些 URL，请参阅[快速入门：Azure AD 无缝单一登录](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)。

9. 对于完整的演练，您还可以下载我们的*无缝 SSO*[部署计划](https://aka.ms/SeamlessSSODPDownload)。

## <a name="enable-staged-rollout"></a>启用分阶段推出

要将特定功能（*直通身份验证*、*密码哈希同步*或*无缝 SSO）* 推出到组中的一组选定的用户，请按照以下各节中的说明进行操作。

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>在租户上启用特定功能的分阶段推出

您可以推出以下选项之一：

- **选项** - *密码哈希同步* + *无缝 SSO*
- **选项 B** - *传递身份验证* + *无缝 SSO*
- **不支持** - *密码哈希同步* + *直通身份验证* + *无缝 SSO*

请执行以下操作：

1. 要访问预览 UX，请登录到[Azure AD 门户](https://aka.ms/stagedrolloutux)。

2. 为**托管用户登录（预览）链接选择启用暂存推出**。

   例如，如果要启用*选项 A，* 请将**密码哈希同步**和**无缝单一登录**控件滑动到**On**，如下图所示。

   ![Azure AD 连接页](./media/how-to-connect-staged-rollout/sr4.png)

   !["启用分阶段推出功能（预览）"页面](./media/how-to-connect-staged-rollout/sr5.png)

3. 将组添加到功能中，以启用*直通身份验证*和*无缝 SSO。* 为了避免 UX 超时，请确保安全组最初包含的成员不超过 200 个。

   !["管理密码哈希同步组（预览）"页面](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >组中的成员将自动启用以进行分阶段推出。 暂存的推出不支持嵌套组和动态组。

## <a name="auditing"></a>审核

我们为分阶段部署执行的各种操作启用了审核事件：

- 当您为*密码哈希同步*、*传递身份验证*或*无缝 SSO*启用分阶段推出时，将发生审核事件。

  >[!NOTE]
  >使用暂存部署打开*无缝 SSO*时，将记录审核事件。

  !["为功能创建推出策略"窗格 - 活动选项卡](./media/how-to-connect-staged-rollout/sr7.png)

  !["为功能创建推出策略"窗格 - 修改属性选项卡](./media/how-to-connect-staged-rollout/sr8.png)

- 当组添加到*密码哈希同步*、*传递身份验证*或*无缝 SSO*时，审核事件。

  >[!NOTE]
  >当组添加到分阶段推出时的*密码哈希同步*时，将记录审核事件。

  !["添加组以功能推出"窗格 - 活动选项卡](./media/how-to-connect-staged-rollout/sr9.png)

  !["添加组以功能推出"窗格 - 修改属性选项卡](./media/how-to-connect-staged-rollout/sr10.png)

- 启用添加到组的用户进行暂存部署时的审核事件。

  !["将用户添加到功能推出"窗格 - 活动选项卡](media/how-to-connect-staged-rollout/sr11.png)

  !["将用户添加到功能推出"窗格 - 目标选项卡](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>验证

要使用*密码哈希同步*或*传递身份验证*（用户名和密码登录）测试登录，请执行以下操作：

1. 在外联网上，转到专用浏览器会话中的["应用"页面](https://myapps.microsoft.com)，然后输入为分阶段推出而选择的用户帐户的用户主体名称 （UPN）。

   被暂有部署目标的用户不会重定向到您的联合登录页。 相反，他们被要求在 Azure AD 租户品牌的登录页上登录。

1. 通过使用 UserTheName 进行筛选，确保登录成功显示在[Azure AD 登录活动报表](../reports-monitoring/concept-sign-ins.md)中。

要使用*无缝 SSO*测试登录：

1. 在 Intranet 上，转到专用浏览器会话中的["应用"页](https://myapps.microsoft.com)，然后输入为分阶段推出而选择的用户帐户的用户主体名称 （UPN）。

   被锁定为*分阶段推出无缝 SSO*的用户将显示一个"尝试登录..."消息之前，他们默默地登录。

1. 通过使用 UserTheName 进行筛选，确保登录成功显示在[Azure AD 登录活动报表](../reports-monitoring/concept-sign-ins.md)中。

   要跟踪在活动目录联合服务 （AD FS） 上仍针对所选暂有推出用户的用户登录，请按照[AD FS 故障排除中的说明：事件和日志记录](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)。 查看有关如何检查第三方联合提供程序的供应商文档。

## <a name="remove-a-user-from-staged-rollout"></a>从分阶段推出中删除用户

从组中删除用户将禁用该用户的暂存推出。 要禁用暂存的推出功能，请将控件滑回 **"关闭**"。

## <a name="frequently-asked-questions"></a>常见问题

**问：是否可以在生产中使用此功能？**

答：是的，您可以在生产租户中使用此功能，但我们建议您首先在测试租户中试用此功能。

**问：此功能是否可用于维护永久的"共存"，即某些用户使用联合身份验证，而其他用户使用云身份验证？**

答：不，此功能旨在分阶段从联合身份验证迁移到云身份验证，然后最终迁移到云身份验证。 我们不建议使用永久混合状态，因为此方法可能会导致意外的身份验证流。

**问：我可以使用 PowerShell 执行分阶段的推出吗？**

答：是的。 要了解如何使用 PowerShell 执行暂存部署，请参阅[Azure AD 预览](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="next-steps"></a>后续步骤
- [Azure AD 2.0 预览版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
