---
title: Azure AD Connect： Cloud authentication-暂存推出 |Microsoft Docs
description: 介绍如何使用分阶段推出从联合身份验证迁移到云身份验证。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2596091324acde5c4fdc3f7c467849f90266fec9
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847229"
---
# <a name="cloud-authentication-staged-rollout-public-preview"></a>云身份验证：分阶段推出（公共预览版）

此功能允许你使用分阶段方法从联合身份验证迁移到云身份验证。

离开联合身份验证会产生影响。 例如，如果有以下任意一种：
    
-  本地 MFA 服务器 
-  使用智能卡进行身份验证 
-  其他仅限联合功能

在切换到云身份验证之前，应考虑这些功能。  在尝试此功能之前，我们建议您查看本指南，以了解如何选择正确的身份验证方法。 有关更多详细信息，请参阅[此表](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)。

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>先决条件

-   你有一个包含联合域的 Azure AD 租户。

-   你已决定移动到 "密码哈希同步 + 无缝 SSO **（选项 A）"** 或 "直通身份验证 + 无缝 Sso **（选项 B）"。** 尽管无缝 SSO 是可选的，但我们建议启用无缝 SSO，为使用已加入域的计算机的用户提供无提示登录体验。

-   已为要迁移到云身份验证的用户配置了所有合适的租户品牌和条件访问策略。

-   如果计划使用 Azure 多重身份验证，我们建议使用[聚合注册进行自助服务密码重置（SSPR）和 AZURE MFA](../authentication/concept-registration-mfa-sspr-combined.md) ，使用户能够注册其身份验证方法一次。

-   若要使用此功能，你需要是租户的全局管理员。

-   若要在特定的 AD 林中启用无缝 SSO，你需要是域管理员。

## <a name="supported-scenarios"></a>支持的方案

临时推出支持这些方案：

- 此功能仅适用于使用 Azure AD Connect 预配到 Azure AD 的用户，不适用于仅限云的用户。

- 此功能仅适用于浏览器和新式身份验证客户端上的用户登录流量。 使用旧身份验证的应用程序或云服务将回退到联合身份验证流。 （示例：禁用了新式身份验证的 Exchange online 或 Outlook 2010，该功能不支持新式身份验证。）

## <a name="unsupported-scenarios"></a>不受支持的方案

过渡部署不支持这些方案：

- 某些应用程序在身份验证过程中向 Azure AD 发送 "域\_提示" 查询参数。 这些流将继续，启用暂存后的用户将继续使用联合身份验证进行身份验证。

<!-- -->

- 管理员可以使用安全组推出云身份验证。 （建议使用云安全组，以避免在使用本地 AD 安全组时的同步滞后时间。）

    - **每个功能最多**可以使用10个组;即每个密码哈希同步/传递身份验证/无缝 SSO。

    - 嵌套组**不受支持**。 这也是公共预览的范围。

    - 临时部署**不支持**动态组。

    - 组内的联系人对象会阻止添加组窗体。

- 从联合身份验证到云身份验证的最终转换仍需使用 Azure AD Connect 或 PowerShell 来执行。 此功能不会将域从联合切换为托管。

- 当你首次添加用于分阶段推出的安全组时，该安全组限制为200用户，避免 UX 超时。将组添加到 UX 后，可以根据需要将更多用户直接添加到组。

## <a name="get-started-with-staged-rollout"></a>分步推出入门

如果要使用分阶段推出来测试密码哈希同步（PHS）登录，请完成以下预备工作，启用密码哈希同步分阶段推出。

有关使用的 PowerShell cmdlet 的详细信息，请参阅[AzureAD 2.0 preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)

## <a name="pre-work-for-password-hash-sync"></a>密码哈希同步的预工作

1. 从 Azure AD Connect 中的 " [可选功能](how-to-connect-install-custom.md#optional-features) " 页启用密码哈希同步。 

   ![Azure Active Directory Connect 中的 "可选功能" 页的屏幕截图](media/how-to-connect-staged-rollout/sr1.png)

1. 确保完整的密码哈希同步周期已运行，以便所有用户的密码哈希同步到 Azure AD。 可以使用[此处](tshoot-connect-password-hash-synchronization.md)的 PowerShell 诊断来检查密码哈希同步的状态。

   ![AADConnect 故障排除日志的屏幕截图](./media/how-to-connect-staged-rollout/sr2.png)

如果你想要使用分阶段推出来测试通过身份验证（PTA）登录，请完成以下预备工作，以启用 PTA 进行分阶段推出。

## <a name="pre-work-for-pass-through-authentication"></a>用于传递身份验证的预工作

1. 标识运行 Windows Server 2012 R2 或更高版本的服务器，你希望通过身份验证代理在此服务器上运行（**请勿选择 Azure AD Connect 服务器**）。 确保服务器已加入域，可以通过 Active Directory 对选定用户进行身份验证，并且可以与 Azure AD 出站端口/Url 进行通信（请参阅详细的[先决条件](how-to-connect-sso-quick-start.md)）。

1. [下载](https://aka.ms/getauthagent) & 在服务器上安装 Microsoft Azure AD 连接身份验证代理。 

1. 若要启用 [高可用性](how-to-connect-sso-quick-start.md)，请在其他服务器上安装其他身份验证代理。

1. 确保已正确配置[智能锁定设置](../authentication/howto-password-smart-lockout.md)。 这是为了确保用户本地 Active Directory 帐户不会被错误的执行组件锁定。

建议启用无缝 SSO，而不考虑你选择用于分阶段推出的登录方法（PHS 或 PTA）。 请完成下面的预备工作，为分阶段推出启用无缝 SSO。

## <a name="pre-work-for-seamless-sso"></a>无缝 SSO 的预工作

使用 PowerShell 在 AD 林上启用无缝 SSO。 如果有多个 AD 林，请为每个林单独启用相同的。 仅对选择进行分阶段推出的用户触发无缝 SSO，而不会影响现有的联合身份验证设置。

**步骤摘要**

1. 首先，登录到 Azure AD Connect 服务器。

2. 导航到% programfiles%\\Microsoft Azure Active Directory Connect 文件夹。

3. 使用以下命令导入无缝 SSO PowerShell 模块： `Import-Module .\AzureADSSO.psd1`。

4. 以管理员身份运行 PowerShell。 在 PowerShell 中，调用 `New-AzureADSSOAuthenticationContext`。 此命令应为你显示一个对话框，你可以在其中输入租户的全局管理员凭据。

5. 调用 `Get-AzureADSSOStatus | ConvertFrom-Json`。 此命令提供已启用此功能的 AD 林列表（查看 \"域\" 列表）。 默认情况下，它在租户级别设置为 false。

   > **示例：** 
   > Windows PowerShell 输出 ![示例](./media/how-to-connect-staged-rollout/sr3.png)

6. 调用 `$creds = Get-Credential`。 出现提示时，输入目标 AD 林的域管理员凭据。

7. 调用 `Enable-AzureADSSOForest -OnPremCredentials $creds`。 此命令将为无缝 SSO 所需的特定 Active Directory 林的本地域控制器创建 AZUREADSSOACC 计算机帐户。

8. 无缝 SSO 要求 Url 位于 intranet 区域中。 请参阅[无缝单一登录快速入门](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)，使用组策略部署这些 URL。

9.  你还可以下载用于无缝 SSO 的[部署计划](https://aka.ms/SeamlessSSODPDownload)来完成完整的演练。

## <a name="enable-staged-rollout"></a>启用暂存推出

使用以下步骤将特定功能（传递身份验证/密码哈希同步/无缝 SSO）推出到组中的一组选择的用户：

### <a name="enable-the-staged-rollout-of-a-specific-feature-on-your-tenant"></a>在你的租户上启用特定功能的分步推出

你可以推出这些选项之一

-   密码哈希同步 + 无缝 SSO **（选项 A）**

-   传递身份验证 + 无缝 SSO **（选项 B）**

-   密码哈希同步 + 传递身份验证 + 无缝 SSO **-\>** ***不受支持***

完成以下步骤：

1. 使用以下 URL 登录到 Azure AD 门户，以访问预览 UX。

   > <https://aka.ms/stagedrolloutux>

2. 单击 "为托管用户登录启用过渡部署（预览版）"

   *例如：* （**选项 B**）如果想要启用密码哈希同步和无缝 SSO，请将密码哈希同步和无缝单一登录功能滑到 **"启用"** ，如下所示。

   ![](./media/how-to-connect-staged-rollout/sr4.png)

   ![](./media/how-to-connect-staged-rollout/sr5.png)

3. 将相应的组添加到功能，以启用直通身份验证和无缝单一登录。 请确保最初安全组不超过200个成员，以避免 UX 超时。

   ![](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >将自动为组中的成员启用过渡。 过渡部署不支持嵌套和动态组。

## <a name="auditing"></a>审核

我们已为用于分阶段推出的不同操作启用了审核事件。

- 启用用于密码哈希同步/传递身份验证/无缝 SSO 的分步推出时的审核事件。

  >[!NOTE]
  >**使用 StagedRollout**打开 SeamlessSSO 时记录的审核事件。

  ![](./media/how-to-connect-staged-rollout/sr7.png)

  ![](./media/how-to-connect-staged-rollout/sr8.png)

- 将组添加到密码哈希同步/传递身份验证/无缝 SSO 时的审核事件。

  >[!NOTE]
  >将组添加到密码哈希同步进行暂存部署时记录的审核事件

  ![](./media/how-to-connect-staged-rollout/sr9.png)

  ![](./media/how-to-connect-staged-rollout/sr10.png)

- 为暂存推出启用已添加到组的用户时的审核事件

  ![](media/how-to-connect-staged-rollout/sr11.png)

  ![](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>验证

使用密码哈希同步或传递身份验证（用户名/密码登录）测试登录：

1. 从 extranet 浏览到专用浏览器会话 <https://myapps.microsoft.com>，并输入所选的用于分阶段推出的用户帐户的 UserPrincipalName （UPN）。

1. 如果用户已针对暂存推出，则不会将用户重定向到联合登录页面，而是会要求登录到 Azure AD 租户品牌登录页。

1. 通过使用 UserPrincipalName 筛选来确保登录已成功出现在[Azure AD 登录活动报告](../reports-monitoring/concept-sign-ins.md)中。

用无缝 SSO 测试登录：

1. 从 intranet 浏览到专用浏览器会话 <https://myapps.microsoft.com>/，并输入所选的用于暂存的用户帐户的 UserPrincipalName （UPN）。

1. 如果用户已针对无缝 SSO 的分阶段推出，用户将看到 "正在尝试登录 ..."消息，然后以无提示方式登录。

1. 通过使用 UserPrincipalName 筛选来确保登录已成功出现在[Azure AD 登录活动报告](../reports-monitoring/concept-sign-ins.md)中。

若要检查在联合身份验证提供程序上仍然发生的用户登录：

下面介绍了如何使用[这些说明](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)跟踪所选过渡推出用户在 AD FS 上的用户登录。 查看供应商文档，了解如何在第三方联合身份验证提供商上进行检查。

## <a name="roll-back"></a>回滚

### <a name="remove-a-user-from-staged-rollout"></a>从分阶段推出中删除用户

1.  从组中删除用户将为用户禁用暂存式推出。

2.  若要禁用暂存推出功能，请将此功能滑回 **"关闭"** 状态以关闭暂存推出。


## <a name="frequently-asked-questions"></a>常见问题

-   **问：客户是否可以在生产中使用此功能？**

-   答：是的，此功能可用于你的生产租户，但我们建议你首先在测试租户中尝试此功能。

-   **问：此功能能否用于维护永久性的 "共存"，其中某些用户使用联合身份验证和其他云身份验证？**

-   答：不可以，此功能设计用于分阶段迁移到云身份验证，然后最终将其转换为云身份验证。 不建议使用永久性混合状态，因为这可能会导致意外的身份验证流。

-   **问：我们可以使用 PowerShell 来执行分阶段推出吗？**

-   答：是的，请在[此处](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)找到使用 PowerShell 执行分阶段推出的文档。

## <a name="next-steps"></a>后续步骤
- [AzureAD 2.0 预览版](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
