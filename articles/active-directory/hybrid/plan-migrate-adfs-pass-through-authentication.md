---
title: Azure AD Connect：从联合身份验证迁移到 Azure AD 的直通身份验证 | Microsoft Docs
description: 有关将混合标识环境从联合身份验证转移到直通身份验证的信息。
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5361c8940c8c7dba5338a3f5a0ed18910f7e45a0
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410323"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-ad"></a>从联合身份验证迁移到 Azure AD 的直通身份验证
以下文档提供有关从 AD FS 转移到直通身份验证的指导。

>[!NOTE]
>[此处](https://aka.ms/ADFSTOPTADPDownload)提供了本文档的可下载副本。

## <a name="prerequisites-for-pass-through-authentication"></a>直通身份验证的先决条件
在迁移之前，必须满足以下先决条件。
### <a name="update-azure-ad-connect"></a>更新 Azure AD Connect

若要成功执行迁移到直通身份验证的步骤，至少需要安装 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0。 此版本对执行登录转换的方式做了重大更改，将联合身份验证到云身份验证的迁移所需的总时间从潜在的几个小时减少为几分钟。

> [!IMPORTANT]
> 过时的文档、工具和博客指出，将联合域转换为托管域时，必须执行用户转换。 现在不再需要**转换用户**，Microsoft 正在努力更新文档和工具以反映这种情况。

若要将 Azure AD Connect 更新到最新版本，请遵循此[更新说明](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)。

### <a name="plan-authentication-agent-number-and-placement"></a>规划身份验证代理数目和位置

可以通过在 Azure AD Connect 服务器和本地 Windows 服务器上部署轻型代理来实现直通身份验证。 请将代理安装在尽量靠近 Active Directory 域控制器的位置，以减少延迟。

对于大多数客户而言，两个或三个身份验证代理就足以实现高可用性和容量需求，为一个租户注册的代理不能超过 12 个。 第一个代理始终安装在 AAD Connect 服务器本身上。 请参阅[有关网络流量估算和性能指导的信息](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations)，以了解代理限制和代理部署选项。

### <a name="plan-migration-method"></a>规划迁移方法

可通过两种方法从联合身份验证迁移到 PTA 和无缝 SSO。 所用的方法取决于 AD FS 的最初配置方式。

- **使用 Azure AD Connect**。 如果最初使用 Azure AD Connect 配置了 AD FS，则必须通过 Azure AD Connect 向导来执行直通身份验证。

如果 Azure AD Connect，在更改用户登录方法时，它会自动运行 Set-MsolDomainAuthentication cmdlet，因此，你无法控制它取消联合 Azure AD 租户中所有已验证的联合域。  
‎  
> [!NOTE]
> 目前，如果最初使用 AAD Connect 配置了 AD FS，在将用户登录方法更改为直通身份验证时，无法避免取消联合租户中的所有域。  
‎
- **配合使用 Azure AD Connect 和 PowerShell**。 仅当 AD FS 最初不是使用 Azure AD Connect 配置的时，才可以使用此方法。 仍需要通过 Azure AD Connect 向导更改用户登录方法，但重要差别在于，Azure AD Connect 不会自动运行 Set-MsolDomainAuthentication cmdlet，因为它不能识别 AD FS 场，因此，你对转换的域和转换顺序拥有完全控制权。

若要了解应使用哪种方法，请执行以下部分中的步骤。

#### <a name="verify-current-user-sign-in-settings"></a>验证当前用户登录设置

使用全局管理员帐户登录到 Azure AD 门户 [https://aad.portal.azure.com](https://aad.portal.azure.com/) 来验证当前用户登录设置。 

在“用户登录”部分，验证“联合身份验证”是否为“已启用”，“无缝单一登录”和“直通身份验证”是否为“已禁用”。 

![图片 5](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>验证联合身份验证的配置方式

   1. 转到 Azure AD Connect 服务器并启动 Azure AD Connect，然后选择“配置”。
   2. 在“其他任务”屏幕中，依次选择“查看当前配置”、“下一步”。</br>
   ![图片 31](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)</br>
   3. 在“查看解决方案”屏幕中，向下滚动到“Active Directory 联合身份验证服务(AD FS)”。</br>
   如果在此部分看到了 AD FS 配置，则可以肯定 AD FS 最初是通过 Azure AD Connect 配置的，因此，可以通过 Azure AD Connect 的“更改用户登录”选项来驱动从联合域到托管域的转换。“选项 1：使用 Azure AD Connect 配置直通身份验证”部分详细介绍了此过程。  
‎
   4. 如果当前设置中未列出“Active Directory 联合身份验证服务”，则需要通过 PowerShell 手动从联合域转换为托管域。“选项 2 - 使用 Azure AD Connect 和 PowerShell 从联合身份验证切换到 PTA”部分详细介绍了此过程。

### <a name="document-current-federation-settings"></a>阐述当前联合身份验证设置

可以运行 Get-MsolDomainFederationSettings cmdlet 来查找当前联合身份验证设置。

命令为：

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

例如：

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```


验证可能已根据联合身份验证设计和部署文档自定义的任何设置，具体而言，是 **PreferredAuthenticationProtocol**、**SupportsMfa** 和 **PromptLoginBehavior**。

下面提供了有关这些设置的作用的详细信息。

[Active Directory 联合身份验证服务 prompt=login 参数支持](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> 如果 SupportsMfa 值当前设置为“True”，则表示你正在使用本地 MFA 解决方案将第二因素质询注入到用户身份验证流。 此功能不再适用于 Azure AD 身份验证方案。必须利用基于云的 Azure MFA 服务来执行相同的功能。 在继续之前，请仔细评估 MFA 要求；在转换域之前，请确保了解 Azure MFA 的用法、许可影响，以及最终用户注册过程。

#### <a name="backup-federation-settings"></a>备份联合身份验证设置

尽管在此过程中不会对 AD FS 场中的其他信赖方进行任何更改，但最好是确保为 AD FS 场创建可还原的当前有效备份。 可以使用免费的 Microsoft [AD FS 快速还原工具](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)实现此目的。 使用此工具可将 AD FS 备份和还原到现有场或新场。

如果不选择使用 AD FS 快速还原工具，则最起码应该导出“Microsoft Office 365 标识平台”信赖方信任，以及可能已添加的任何关联的自定义声明规则。 可以通过以下 PowerShell 示例来实现此目的

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>部署注意事项和 AD FS 用法

### <a name="validate-your-current-ad-fs-usage"></a>验证当前的 AD FS 用法

从联合域转换为托管域之前，应仔细检查目前如何将 AD FS 用于 Azure AD/Office 365 和其他应用程序（信赖方信任）。 具体而言，应考虑下表中的信息：

| 如果| 那么 |
|-|-|
| 要为其他这些应用程序保留 AD FS。| 将同时使用 AD FS 和 Azure AD，因此需要考虑最终用户体验。 在某些情况下，用户可能需要进行身份验证两次，一次是针对 Azure AD（然后用户可以通过 SSO 登录到 Office 365 等其他应用程序），另一次是针对仍以信赖方信任方式绑定到 AD FS 的任何应用程序再次进行身份验证。 |
| AD FS 经过重度的自定义，并依赖于无法在 Azure AD 中复制的 onload.js 文件中的特定自定义设置（例如，你已更改登录体验，使用户只需以 SamAccountName 格式输入其用户名而不是 UPN；或者在登录体验中使用了众多的品牌设计）| 在继续之前，需要验证 Azure AD 是否可以满足当前自定义要求。 有关更多信息和指导，请参阅“AD FS 品牌”和“AD FS 自定义”部分。|
| 正在通过 AD FS 阻止旧式身份验证客户端。| 考虑将 AD FS 中当前存在的、用于阻止旧式身份验证客户端的控制机制，替换为[针对旧式身份验证的条件访问控制](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)和 [Exchange Online 客户端访问规则](http://aka.ms/EXOCAR)的组合。 |
| 要求用户在 AD FS 中进行身份验证时对本地 MFA 服务器解决方案执行 MFA。| 无法通过本地 MFA 解决方案将 MFA 质询注入到托管域的身份验证流，但是，在转换域后，可以使用 Azure MFA 服务实现此目的。 如果用户目前未使用 Azure MFA，则此过程涉及到一次性的最终用户注册步骤，你需要对此做好准备并与最终用户沟通。 |
| 目前在 AD FS 中使用访问控制策略（AuthZ 规则）来控制对 Office 365 的访问。| 考虑将这些策略替换为等效的 Azure AD [条件访问策略](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)和 [Exchange Online 客户端访问规则](http://aka.ms/EXOCAR)。|

### <a name="considerations-for-common-ad-fs-customizations"></a>有关常见 AD FS 自定义的注意事项

#### <a name="inside-corporate-network-claim"></a>在企业网络声明内部

如果用户身份验证在企业网络内部进行，则 InsideCorporateNetwork 声明由 AD FS 颁发。 然后，可将此声明传递给 Azure AD，并使用它来根据用户的网络位置绕过多重身份验证。 有关如何确定当前是否已在 AD FS 中启用此功能的信息，请参阅[联合用户的受信任 IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)。

将域转换为直通身份验证后，InsideCorporateNetwork 声明不再可用。 可以使用 [Azure AD 中的命名位置](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)来取代此功能。

配置命名位置后，必须更新配置为包含或排除网络位置“所有受信任位置”或“MFA 信任的 IP”的所有条件访问策略，以反映新建的命名位置。

有关条件访问中的位置条件的详细信息，请参阅 [Active Directory 条件访问位置](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)。

#### <a name="hybrid-azure-ad-joined-devices"></a>已加入混合 Azure AD 的设备

将设备加入 Azure AD 可以创建条件访问规则，以强制要求设备符合访问标准以及安全与合规性要求；这样还可让用户使用组织的工作或学校帐户（而不是个人帐户）登录到设备。 使用已加入混合 Azure AD 的设备可将已加入 AD 域的设备加入到 Azure AD。 你的联合环境可能已配置了此功能。

为了确保在将域转换为直通身份验证之后，混合加入仍适用于任何已加入域的新设备，Azure AD Connect 必须配置为将 Windows 10 客户端的 Active Directory 计算机帐户同步到 Azure AD。 对于 Windows 7 和 Windows 8 计算机帐户，混合加入将使用无缝 SSO 在 Azure AD 中注册计算机，你无需像在 Windows 10 设备上一样同步这些帐户。 但是，必须（通过一个 .msi）将更新的 workplacejoin.exe 文件部署到这些下层客户端，使它们可以使用无缝 SSO 自我注册。 [下载 .msi](https://www.microsoft.com/download/details.aspx?id=53554)。

有关此项要求的详细信息，请参阅[如何配置联接到混合 Azure Active Directory 的设备](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)。

#### <a name="branding"></a>品牌

你的组织可能已[自定义 ADFS 登录页](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)，使显示的内容与组织更相关。 如果是这样，请考虑[在 Azure AD 登录页中使用类似的自定义项](https://docs.microsoft.com/azure/active-directory/customize-branding)。

尽管可以使用类似的自定义项，但应该预见到会发生一定的视觉变化。 你可能想要在发给最终用户的信件中包含预期的更改。

> [!NOTE]
> 只有购买了 Azure AD 的 Premium 或 Basic 许可证或者拥有 Office 365 许可证，才能使用公司品牌功能。

## <a name="planning-for-smart-lockout"></a>规划智能锁定

Azure AD 智能锁定可以防范暴力破解密码攻击，并可以防止在使用直通身份验证并在 Active Directory 中设置了帐户锁定组策略时，本地 Active Directory 帐户遭到锁定。 

有关详细信息，请参阅[智能锁定功能以及如何编辑其配置](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout)。

## <a name="planning-deployment-and-support"></a>规划部署和支持

### <a name="plan-the-maintenance-window"></a>规划维护时段

尽管域转换过程本身的速度相对较快，但在完成域转换后的最长 4 小时内，Azure AD 仍可能向 AD FS 服务器发送一些身份验证请求。 在这 4 小时期限内，根据不同的服务端缓存，这些身份验证可能不被 Azure AD 接受，同时用户会收到错误，因为他们仍可针对 AD FS 成功完成身份验证，但是，Azure AD 不再接受用户的颁发令牌，因为该联合信任现已删除。

> [!NOTE]
> 这只会影响在清除服务端缓存之前，在此后期转换时段通过浏览器访问服务的用户。 旧式客户端（Exchange ActiveSync、Outlook 2010/2013）应不受到影响，因为 Exchange Online 会将其凭据缓存保留一段时间，用于以静默方式重新验证用户身份，而无需返回到 AD FS。 清除此缓存后，将使用这些客户端的设备上存储的凭据来以静默方式对客户端自身重新进行身份验证，因此，在完成域转换过程后，用户不应会收到任何密码提示。 相比之下，新式身份验证客户端（Office 2013/2016、IOS 和 Android 应用）使用有效的刷新令牌获取新的访问令牌以持续访问资源，而无需返回到 AD FS，因此，在完成域转换过程后有无密码提示并不重要，无需任何额外的配置，这些客户端就能持续正常运行。
> [!IMPORTANT]
> 在验证所有用户可以使用云身份验证成功完成身份验证之前，请不要关闭 AD FS 环境或删除 Office 365 信赖方信任。

### <a name="plan-for-rollback"></a>规划回滚

如果发现了不可快速解决的重大问题，可将解决方案回滚到联合身份验证。 必须规划好当部署无法按计划进行时要怎样做。 如果部署期间域或用户转换失败，或者需要回滚到联合身份验证，则必须了解如何应对任何服务中断和减小对用户的影响。

#### <a name="rolling-back"></a>回滚

请查阅联合身份验证设计和部署文档获取特定部署的详细信息。 该过程应涉及：

* 使用 Convert-MSOLDomainToFederated 将托管域转换为联合域 

* 根据需要配置其他声明规则。

### <a name="plan-change-communications"></a>规划变更沟通

规划部署和支持的一个重要组成部分是确保主动通知最终用户将要发生哪些变化，以及他们可能会遇到哪种情况，或者必须做些什么。 

部署直通身份验证和无缝 SSO 后，访问通过 Azure AD 进行身份验证的 Office 365 和其他关联的资源时，最终用户登录体验将会发生变化。 网络外部的用户现在会看到 Azure AD 登录页，而不是重定向到面向外部的 Web 应用程序代理服务器提供的基于窗体的页。

规划沟通策略时需要考虑到多个要素。 其中包括：

* 通过以下方式通知用户即将发布和已发布哪些功能
  * 电子邮件和其他内部沟通渠道
  * 海报等视觉对象
  * 行政现场发布会或其他沟通方式
* 确定由谁执行自定义，谁发送信件，以及何时发送。

## <a name="implementing-your-solution"></a>实施解决方案

规划好解决方案后，可将其实施。 实施包括以下组成部分：

   1. 准备无缝单一登录
   2. 更改直通身份验证登录方法并启用无缝 SSO

## <a name="step-1--prepare-for-seamless-sso"></a>步骤 1 - 准备无缝 SSO

要让设备使用无缝 SSO，需使用 Active Directory 中的组策略将一个 Azure AD URL 添加到用户的 Intranet 区域设置。

默认情况下，浏览器将自动从特定 URL 计算正确的区域（Internet 或 Intranet）。 例如，“http://contoso/”映射到 Intranet 区域，而“http://intranet.contoso.com/”映射到 Internet 区域（因为此 URL 包含句点）。 浏览器不会将 Kerberos 票证发送到云终结点（例如 Azure AD URL），除非将此 URL 显式添加到浏览器的 Intranet 区域。

请遵循[这些步骤](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)在设备上实施所需的更改。

> [!IMPORTANT]
> 进行此项更改不会修改用户登录到 Azure AD 的方式。 但是，必须在继续执行步骤 3 之前将此配置应用到所有设备。另请注意，在尚未收到此配置的设备上登录的用户只需输入用户名和密码即可登录到 Azure AD。

## <a name="step-2--change-sign-in-method-to-pta-and-enable-seamless-sso"></a>步骤 2 - 将登录方法更改为 PTA 并启用无缝 SSO

### <a name="option-a-configuring-pta-by-using-azure-ad-connect"></a>选项 A：使用 Azure AD Connect 配置 PTA

如果 AD FS 最初是使用 Azure AD Connect 配置的，请使用此方法。 如果 AD FS 最初不是使用 Azure AD Connect 配置的，则不能使用此方法。

> [!IMPORTANT]
> 请记住，执行以下步骤会将所有联合域转换为托管域。 请查看“规划迁移方法”部分了解详细信息。[](#_Plan_Migration_Method)

首先必须更改登录方法：

   1. 在 Azure AD Connect 服务器上打开向导。
   2. 依次选择“更改用户登录”、“下一步”。 
   3. 在“连接到 Azure AD”屏幕中，提供全局管理员的用户名和密码。
   4. 在“用户登录”屏幕中，将单选按钮从“使用 AD FS 进行联合身份验证”更改为“直通身份验证”，选择“启用单一登录”，然后选择“下一步”。
   5. 在“启用单一登录”屏幕中输入域管理员帐户的凭据，然后选择“下一步”。  

   > [!NOTE]
   > 需要使用域管理员凭据来启用无缝单一登录，因为该过程将执行以下操作，而这些操作需要这些提升的权限。 域管理员凭据不存储在 Azure AD Connect 或 Azure AD 中。 这些凭据仅用于启用该功能，成功完成后即会将其丢弃
   >
   > * 在本地 Active Directory (AD) 中创建名为 AZUREADSSOACC 的计算机帐户（表示 Azure AD）。
   > * 与 Azure AD 安全共享计算机帐户的 Kerberos 解密密钥。
   > * 此外，创建两个 Kerberos 服务主体名称 (SPN) 来表示 Azure AD 登录期间使用的两个 URL。
   > * 域管理员凭据不存储在 Azure AD Connect 或 Azure AD 中。 这些凭据仅用于启用该功能，成功完成后即会将其丢弃

   6. 在“已准备好进行配置”屏幕中，确保已选中“配置完成后启动同步过程”复选框。 然后选择“配置”。</br>
   ![图片](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)</br>
   7. 打开 **Azure AD 门户**，依次选择“Azure Active Directory”、“Azure AD Connect”。
   8. 验证“联合身份验证”是否为“已禁用”，“无缝单一登录”和“直通身份验证”是否为“已启用”。</br>
   ![图片](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)</br>

接下来，必须部署附加的身份验证方法。 打开 **Azure 门户**，浏览到“Azure Active Directory”、“Azure AD Connect”，然后单击“直通身份验证”。

在“直通身份验证”页中，单击“下载”按钮。 在“下载代理”屏幕中，单击“接受条款并下载”。

随后将开始下载附加的身份验证代理。 在已加入域的服务器上安装辅助身份验证代理。 

> [!NOTE]
> 在 Azure AD Connect 工具的“用户登录”部分进行配置更改过程中，始终会在 Azure AD Connect 服务器本身上安装第一个代理。 任何附加的身份验证代理应安装在单独的服务器上。 建议提供 2-3 个附加的身份验证代理。 

运行身份验证代理安装。 在安装过程中，需要提供**全局管理员**帐户的凭据。

![图片 1243067202](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

![图片 1243067210](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

安装身份验证代理后，可以返回到直通身份验证代理运行状况页，以检查附加代理的状态。


转到“测试和后续步骤”。

> [!IMPORTANT]
> 跳过“选项 B：使用 Azure AD Connect 和 PowerShell 从联合身份验证切换到 PTA”部分，因为该部分中的步骤不适用。  

### <a name="option-b---switch-from-federation-to-pta-using-azure-ad-connect-and-powershell"></a>选项 B - 使用 Azure AD Connect 和 PowerShell 从联合身份验证切换到 PTA

如果联合身份验证最初不是使用 Azure AD Connect 配置的，请使用此选项。 首先必须启用直通身份验证：

   1. 在 Azure AD Connect 服务器上打开向导。
   2. 依次选择“更改用户登录”、“下一步”。
   3. 在“连接到 Azure AD”屏幕中，提供全局管理员的用户名和密码。
   4. 在“用户登录”屏幕上，将单选按钮从“不要配置”更改为“直通身份验证”，选择“启用单一登录”，然后选择“下一步”。
   5. 在“启用单一登录”屏幕中输入域管理员帐户的凭据，然后选择“下一步”。

   > [!NOTE]
   > 需要使用域管理员凭据来启用无缝单一登录，因为该过程将执行以下操作，而这些操作需要这些提升的权限。 域管理员凭据不存储在 Azure AD Connect 或 Azure AD 中。 这些凭据仅用于启用该功能，成功完成后即会将其丢弃。
   >
   > * 在本地 Active Directory (AD) 中创建名为 AZUREADSSOACC 的计算机帐户（表示 Azure AD）。
   > * 与 Azure AD 安全共享计算机帐户的 Kerberos 解密密钥。
   > * 此外，创建两个 Kerberos 服务主体名称 (SPN) 来表示 Azure AD 登录期间使用的两个 URL。

   6. 在“已准备好进行配置”屏幕中，确保已选中“配置完成后启动同步过程”复选框。 然后选择“配置”。</br>
   ‎![图片](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)</br>
   选择“配置”时，将执行以下步骤：
   * 安装第一个直通身份验证代理
   * 启用直通功能
   * 启用无缝单一登录。  
   7. 验证“联合身份验证”是否仍为“已启用”，“无缝单一登录”和“直通身份验证”现在是否为“已启用”。
   ![图片 2](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
   8. 选择“直通身份验证”，验证状态是否为“活动”。</br>
   
   如果身份验证代理不处于活动状态，请先遵循[这些故障排除步骤](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication)，然后继续在下一步骤进行域转换过程。 在验证 PTA 代理是否已成功安装并且其在 Azure 门户中的状态显示为“活动”之前，如果转换域，可能存在身份验证中断的风险。  
   9. 接下来请部署附加的身份验证代理。 打开 **Azure 门户**，浏览到“Azure Active Directory”、“Azure AD Connect”，然后单击“直通身份验证”。
   10. 在“直通身份验证”页中，单击“下载”按钮。 在“下载代理”屏幕中，单击“接受条款并下载”。
   
   随后将开始下载附加的身份验证代理。 在已加入域的服务器上安装辅助身份验证代理。

  > [!NOTE]
  > 在 Azure AD Connect 工具的“用户登录”部分进行配置更改过程中，始终会在 Azure AD Connect 服务器本身上安装第一个代理。 任何附加的身份验证代理应安装在单独的服务器上。 建议提供 2-3 个附加的身份验证代理。
  
   11. 运行身份验证代理安装。 在安装过程中，需要提供**全局管理员**帐户的凭据。</br>
   ![图片 1243067215](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)</br>
   ![图片 1243067216](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)</br>
   12. 安装身份验证代理后，可以返回到直通身份验证代理运行状况页，以检查附加代理的状态。

此时，联合身份验证仍为已启用，并可以对域正常运行。 若要继续部署，需将每个域从“联合”转换为“托管”，使直通身份验证开始为该域的身份验证请求提供服务。

并非需要同时转换所有域，可以选择从生产租户中的某个测试域着手，或者从用户数量最少的域着手。

转换是使用 Azure AD PowerShell 模块执行的。

   1. 打开 **PowerShell**，使用**全局管理员**帐户登录到 Azure AD。
   2. 若要转换第一个域，请运行以下命令：
 
 ``` PowerShell
 Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
 ```
 
   3. 打开 **Azure AD 门户**，依次选择“Azure Active Directory”、“Azure AD Connect”。  
   4. 转换所有联合域后，验证“联合身份验证”是否为“已禁用”，“无缝单一登录”和“直通身份验证”是否为“已启用”。</br>
   ![图片](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)</br>

## <a name="testing-and-next-steps"></a>测试和后续步骤

### <a name="test-pass-through-authentication"></a>测试直通身份验证 

以前，当租户使用联合身份验证时，用户会从 Azure AD 登录页重定向到你的 AD FS 环境。 将租户配置为使用直通身份验证而不是联合身份验证之后，用户不会重定向到 AD FS，而是直接通过 Azure AD 登录页登录。

在 InPrivate 模式下打开 Internet Explorer 以避免自动执行无缝 SSO 登录，然后转到 Office 365 登录页 ([http://portal.office.com](http://portal.office.com/))。 键入用户的 **UPN**，然后单击“下一步”。 请务必键入已从本地 Active Directory 同步的，并且事先已联合的混合用户的 UPN。 用户将会看到一个屏幕，可在其中键入其用户名和密码。

![图片 18](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

![图片 19](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

键入密码后，应会重定向到 Office 365 门户。

![图片 23](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-single-sign-on"></a>测试无缝单一登录

   1. 登录到已连接到企业网络的且已加入域的计算机。 
   2. 打开 **Internet Explorer** 或 **Chrome** 并转到以下 URL 之一：   
      ‎  
      ‎[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com)（请将 Contoso 替换为自己的域）。

      片刻之后，用户将重定向到 Azure AD 登录页并看到消息“正在尝试将你登录”，系统不会提示输入用户名或密码。</br>
   ![图片 24](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)</br>
   3. 然后，用户将重定向并成功登录到访问面板：

> [!NOTE]
> 无缝单一登录可在支持域提示（例如 myapps.microsoft.com/contoso.com）的 Office 365 服务中正常工作。 Office 365 门户 (portal.office.com) 目前不支持域提示，因此，预期用户需要键入其 UPN。 输入 UPN 之后，无缝单一登录可以代表用户检索 Kerberos 票证，然后在不键入密码的情况下将用户登录。
> [!TIP]
> 为了改善单一登录体验，请考虑部署 [Windows 10 上的 Azure AD 混合加入](https://docs.microsoft.com/azure/active-directory/device-management-introduction)。

### <a name="removal-of-the-relying-party-trust"></a>删除信赖方信任

验证所有用户和客户端已通过 Azure AD 成功完成身份验证后，可以安全删除 Office 365 信赖方信任。

如果 AD FS 未用于其他目的（已配置其他信赖方信任），则现在可以安全解除 AD FS。

### <a name="rollback"></a>回退

如果发现了不可快速解决的重大问题，可将解决方案回滚到联合身份验证。

请查阅联合身份验证设计和部署文档获取特定部署的详细信息。 该过程应涉及：

* 使用 Convert-MSOLDomainToFederated 将托管域转换为联合域
* 根据需要配置其他声明规则。

### <a name="enable-synchronization-of-userprincipalname-updates"></a>启用 userPrincipalName 更新同步

在过去，除非以下两个条件都成立，否则会阻止在本地使用同步服务对 UserPrincipalName 属性进行更新：

* 用户受管理（非联合）。
* 没有为用户分配许可证。

有关如何验证或启用此功能的说明，请参阅以下文章：

[同步 userPrincipalName 更新](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)。

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>滚动更新无缝 SSO 的 Kerberos 解密密钥

必须经常更新在本地 AD 林中创建的 AZUREADSSOACC 计算机帐户（代表 Azure AD）的 Kerberos 解密密钥。 我们强烈建议每隔 30 天至少滚动更新 Kerberos 解密密钥一次，以便与 Active Directory 域成员提交密码更改的频率保持一致。 由于没有任何关联的设备附加到 AZUREADSSOACC 计算机帐户对象，因此需要手动执行滚动更新。

请在运行 Azure AD Connect 的本地服务器上执行这些步骤，以启动 Kerberos 解密密钥的滚动更新。

[如何滚动更新 AZUREADSSOACC 计算机帐户的 Kerberos 解密密钥](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)？

## <a name="monitoring-and-logging"></a>监视和日志记录

应该监视运行身份验证代理的服务器，以保持解决方案的可用性。 除了常规服务器性能计数器以外，身份验证代理还会公开可用于了解身份验证统计信息和错误的性能对象。

身份验证代理将操作记录到 Windows 事件日志中的“Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin”下面。

可根据需要启用故障排除日志。

详细了解[监视和日志记录](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication)。

## <a name="next-steps"></a>后续步骤

- [Azure AD Connect 设计概念](plan-connect-design-concepts.md)
- [选择适当的身份验证](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [支持的拓扑](plan-connect-design-concepts.md)
