---
title: Azure AD Connect：通过分阶段推出进行云身份验证 |Microsoft Docs
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915235"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>使用分阶段推出（预览版）迁移到云身份验证

通过使用分阶段推出方法，你可以从联合身份验证迁移到云身份验证。 本文介绍如何进行切换。 但是，在开始分步推出之前，如果满足以下一个或多个条件，则应考虑这种影响：
    
-  当前正在使用本地多重身份验证服务器。 
-  使用智能卡进行身份验证。 
-  当前服务器提供了某些仅限联合功能。

在尝试此功能之前，我们建议您查看本指南，以了解如何选择正确的身份验证方法。 有关详细信息，请参阅中的 "比较方法" 表[为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)。

有关该功能的概述，请查看此 "Azure Active Directory：什么是分阶段推出？" 显示

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>必备组件

-   你有一个具有联合域的 Azure Active Directory （Azure AD）租户。

-   您已决定移动到以下两个选项之一：
    - **选项 A** - *密码哈希同步（同步）*  + *无缝单一登录（SSO）*
    - **选项 B** + *无缝 SSO* - *传递身份验证*
    
    尽管*无缝 SSO*是可选的，但我们建议启用它以为从企业网络内部运行已加入域的计算机的用户提供无提示登录体验。

-   你已配置了要迁移到云身份验证的用户所需的所有相应租户品牌和条件性访问策略。

-   如果计划使用 Azure 多重身份验证，我们建议使用[聚合注册进行自助密码重置（SSPR）和多重身份验证](../authentication/concept-registration-mfa-sspr-combined.md)，让用户注册其身份验证方法一次。

-   若要使用分阶段推出功能，你必须是租户的全局管理员。

-   若要在特定 Active Directory 林中启用*无缝 SSO* ，你需要是域管理员。

## <a name="supported-scenarios"></a>支持的方案

暂存部署支持以下方案。 此功能仅适用于：

- 使用 Azure AD Connect 设置为 Azure AD 的用户。 它不适用于仅限云的用户。

- 浏览器和*新式身份验证*客户端上的用户登录流量。 使用旧身份验证的应用程序或云服务将回退到联合身份验证流。 例如，在关闭新式身份验证的情况下使用 Exchange online，或使用不支持新式身份验证的 Outlook 2010。

## <a name="unsupported-scenarios"></a>不支持的方案

过渡部署不支持以下方案：

- 某些应用程序在身份验证过程中将 "domain_hint" 查询参数发送到 Azure AD。 这些流将继续进行，启用了过渡式推出的用户将继续使用联合身份验证进行身份验证。

<!-- -->

- 管理员可以使用安全组来推出云身份验证。 为了避免在使用本地 Active Directory 安全组时出现同步延迟，我们建议使用云安全组。 下列条件适用：

    - 每个功能最多可以使用10个组。 也就是说，可以将每个组用于*密码哈希同步*、*传递身份验证*和*无缝 SSO*。
    - 嵌套组*不受支持*。 此范围也适用于公共预览。
    - 临时部署*不支持*动态组。
    - 组内的联系人对象会阻止添加组。

- 你仍需要通过使用 Azure AD Connect 或 PowerShell，使最终从联合身份验证转换到云身份验证。 过渡部署不会将域从联合切换到托管域。

- 当你首次为分阶段推出添加安全组时，将限制为200用户，以避免 UX 超时。添加组后，可以根据需要向其直接添加更多用户。

## <a name="get-started-with-staged-rollout"></a>分步推出入门

若要使用分阶段推出来测试*密码哈希同步*登录，请按照下一节中的预工作说明进行操作。

有关要使用的 PowerShell cmdlet 的信息，请参阅[Azure AD 2.0 预览](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="pre-work-for-password-hash-sync"></a>密码哈希同步的预工作

1. 从 Azure AD Connect 中的 [可选功能](how-to-connect-install-custom.md#optional-features) 页启用 *密码哈希同步* 。 

   ![Azure Active Directory Connect 中的 "可选功能" 页的屏幕截图](media/how-to-connect-staged-rollout/sr1.png)

1. 确保已运行完整的*密码哈希同步*周期，以便所有用户的密码哈希同步到 Azure AD。 若要检查*密码哈希同步*的状态，可以使用 PowerShell 诊断，使用 " [Azure AD Connect 同步" 排查密码哈希同步问题](tshoot-connect-password-hash-synchronization.md)。

   ![AADConnect 故障排除日志的屏幕截图](./media/how-to-connect-staged-rollout/sr2.png)

如果要使用分步推出来测试*直通身份验证*登录，请按照下一节中的预工作说明进行启用。

## <a name="pre-work-for-pass-through-authentication"></a>用于传递身份验证的预工作

1. 标识运行 Windows Server 2012 R2 或更高版本的服务器，在该服务器中你希望*传递身份验证*代理运行。 

   *不要*选择 Azure AD Connect 服务器。 确保服务器已加入域，可以通过 Active Directory 对选定用户进行身份验证，并且可以与出站端口和 Url 上的 Azure AD 通信。 有关详细信息，请参阅[快速入门： Azure AD 无缝单一登录](how-to-connect-sso-quick-start.md)的 "步骤1：检查先决条件" 一节。

1. [下载 Azure AD Connect authentication 代理](https://aka.ms/getauthagent)，并将其安装在服务器上。 

1. 若要启用 [高可用性](how-to-connect-sso-quick-start.md)，请在其他服务器上安装其他身份验证代理。

1. 请确保已正确配置[智能锁定设置](../authentication/howto-password-smart-lockout.md)。 这样做有助于确保用户本地 Active Directory 帐户不会被错误的执行组件锁定。

建议启用*无缝 SSO* ，而不考虑你选择用于分阶段推出的登录方法（*密码哈希同步*或*传递身份验证*）。 若要启用*无缝 SSO*，请按照下一节中的预工作说明操作。

## <a name="pre-work-for-seamless-sso"></a>无缝 SSO 的预工作

使用 PowerShell 启用 Active Directory 林的 *无缝 SSO* 。 如果有多个 Active Directory 林，请为每个林单独启用它。仅为选择进行分阶段推出的用户触发 *无缝 SSO* 。 这不会影响现有的联合身份验证设置。

通过执行以下操作来启用*无缝 SSO* ：

1. 登录到 Azure AD Connect 服务器。

2. 请 Microsoft Azure Active Directory Connect 文件夹中转到 *% programfiles%\\* 。

3. 通过运行以下命令导入*无缝 SSO* PowerShell 模块： 

   `Import-Module .\AzureADSSO.psd1`

4. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令将打开一个窗格，可在其中输入租户的全局管理员凭据。

5. 调用 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令显示已启用此功能的 Active Directory 林的列表（请参阅 "域" 列表）。 默认情况下，它在租户级别设置为 false。

   ![Windows PowerShell 输出示例](./media/how-to-connect-staged-rollout/sr3.png)

6. 调用 `$creds = Get-Credential`。 在提示符下，输入目标 Active Directory 林的域管理员凭据。

7. 调用 `Enable-AzureADSSOForest -OnPremCredentials $creds`。 此命令将为*无缝 SSO*所需的 Active Directory 林的本地域控制器创建 AZUREADSSOACC 计算机帐户。

8. *无缝 SSO*要求 url 位于 intranet 区域中。 若要使用组策略部署这些 Url，请参阅[快速入门： Azure AD 无缝单一登录](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)。

9. 对于完整的演练，还可以下载用于*无缝 SSO*的[部署计划](https://aka.ms/SeamlessSSODPDownload)。

## <a name="enable-staged-rollout"></a>启用暂存推出

若要将特定功能（*传递身份验证*、*密码哈希同步*或*无缝 SSO*）推出到组中的一组用户，请按照后续部分中的说明进行操作。

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>在你的租户上启用特定功能的分步推出

可以推出下列选项之一：

- **选项 A** - *密码哈希同步* + *无缝 SSO*
- **选项 B** + *无缝 SSO* - *传递身份验证*
- **不支持** - *密码哈希同步* + *直通身份验证* + *无缝 SSO*

请执行以下操作：

1. 要访问预览版 UX，请登录到[Azure AD 门户](https://aka.ms/stagedrolloutux)。

2. 选择 "**为托管用户登录（预览版）启用过渡部署**" 链接。

   例如，如果想要启用*选项 A*，请将**密码哈希同步**和**无缝单一登录**控制滑**入到，** 如下面的图像所示。

   !["Azure AD Connect" 页](./media/how-to-connect-staged-rollout/sr4.png)

   !["启用分步推出功能（预览版）" 页](./media/how-to-connect-staged-rollout/sr5.png)

3. 将组添加到功能，以启用*直通身份验证*和*无缝 SSO*。 若要避免 UX 超时，请确保最初安全组不包含200个以上的成员。

   !["管理密码哈希同步的组（预览版）" 页](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >将自动为组中的成员启用过渡。 过渡部署不支持嵌套和动态组。

## <a name="auditing"></a>审核

对于用于分阶段推出的各种操作，我们已启用了审核事件：

- 启用用于*密码哈希同步*、*传递身份验证*或*无缝 SSO*的分步推出时的审核事件。

  >[!NOTE]
  >使用分阶段推出开启*无缝 SSO*时，将记录审核事件。

  !["创建功能的推出策略" 窗格-活动选项卡](./media/how-to-connect-staged-rollout/sr7.png)

  !["创建功能的推出策略" 窗格修改的属性选项卡](./media/how-to-connect-staged-rollout/sr8.png)

- 将组添加到*密码哈希同步*、*传递身份验证*或*无缝 SSO*时的审核事件。

  >[!NOTE]
  >如果将组添加到 "*密码哈希同步*" 以进行暂存部署，则会记录审核事件。

  !["向功能推出添加组" 窗格-"活动" 选项卡](./media/how-to-connect-staged-rollout/sr9.png)

  !["向功能推出添加组" 窗格-修改的属性选项卡](./media/how-to-connect-staged-rollout/sr10.png)

- 当已添加到组的用户启用了过渡时审核事件。

  !["向功能推出添加用户" 窗格-"活动" 选项卡](media/how-to-connect-staged-rollout/sr11.png)

  !["向功能推出添加用户" 窗格-"目标" 选项卡](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>验证

若要使用*密码哈希同步*或*传递身份验证*（用户名和密码登录）测试登录，请执行以下操作：

1. 在 extranet 上，转到专用浏览器会话中的 "[应用" 页](https://myapps.microsoft.com)，然后输入为 "暂存" 推出选择的用户帐户的 USERPRINCIPALNAME （UPN）。

   已作为过渡式推出目标的用户不会重定向到联合登录页。 但会要求他们登录 Azure AD 租户品牌的登录页。

1. 通过使用 UserPrincipalName 筛选来确保登录已成功出现在[Azure AD 登录活动报告](../reports-monitoring/concept-sign-ins.md)中。

用*无缝 SSO*测试登录：

1. 在 intranet 上，转到专用浏览器会话中的 "[应用" 页](https://myapps.microsoft.com)，然后输入为 "暂存" 推出选择的用户帐户的 USERPRINCIPALNAME （UPN）。

   如果用户已被定向到*无缝 SSO*的分阶段推出，将显示 "正在尝试登录 ..."消息，然后再以无提示方式登录。

1. 通过使用 UserPrincipalName 筛选来确保登录已成功出现在[Azure AD 登录活动报告](../reports-monitoring/concept-sign-ins.md)中。

   若要跟踪在所选暂存推出用户的 Active Directory 联合身份验证服务（AD FS）上仍然发生的用户登录，请按照[AD FS 故障排除：事件和日志记录](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)中的说明进行操作。 查看有关如何在第三方联合身份验证提供程序上检查此项的供应商文档。

## <a name="remove-a-user-from-staged-rollout"></a>从分阶段推出中删除用户

从组中删除用户将为该用户禁用暂存推出。 若要禁用分阶段推出功能，请将控件滑回**Off**。

## <a name="frequently-asked-questions"></a>常见问题

**问：我是否能在生产中使用此功能？**

答：是的，你可以在生产租户中使用此功能，但我们建议你首先在测试租户中试用此功能。

**问：此功能能否用于维护永久性的 "共存"，其中某些用户使用联合身份验证，其他用户使用云身份验证？**

答：不可以，此功能设计用于分阶段迁移到云身份验证，然后最终将其转换为云身份验证。 建议不要使用永久性混合状态，因为这种方法可能导致意外的身份验证流。

**问：我是否可以使用 PowerShell 执行分阶段推出？**

答：是的。 若要了解如何使用 PowerShell 执行分阶段推出，请参阅[Azure AD 预览](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)。

## <a name="next-steps"></a>后续步骤
- [Azure AD 2.0 预览版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
