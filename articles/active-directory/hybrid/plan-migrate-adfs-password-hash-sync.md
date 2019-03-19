---
title: Azure AD Connect：从联合身份验证迁移到 Azure AD 的密码哈希同步 | Microsoft Docs
description: 本文提供有关将混合标识环境从联合身份验证转移到密码哈希同步的信息。
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d522b0740b144c39da81a9838f9d6e259fe62d22
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57532773"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>从联合身份验证迁移到 Azure Active Directory 的密码哈希同步

本文介绍如何将组织域从 Active Directory 联合身份验证服务 (AD FS) 迁移到密码哈希同步。

可以[下载此文](https://aka.ms/ADFSTOPHSDPDownload)。

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>迁移到密码哈希同步的先决条件

从 AD FS 迁移到密码哈希同步需要满足以下先决条件。

### <a name="update-azure-ad-connect"></a>更新 Azure AD Connect

若要成功执行迁移到密码哈希同步的步骤，至少需要安装 [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0。 此版本对执行登录转换的方式做了重大更改，将联合身份验证到云身份验证的迁移所需的总时间从潜在的几个小时减少为几分钟。


> [!IMPORTANT]
> 过时的文档、工具和博客中可能指出，将域从联合标识转换为托管标识时，必须执行用户转换。 现在不再需要转换用户。 Microsoft 正在努力更新文档和工具以反映这项变化。

若要更新 Azure AD Connect，请完成 [Azure AD Connect：升级到最新版本](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)中的步骤。

### <a name="password-hash-synchronization-required-permissions"></a>密码哈希同步所需的权限

可以使用快速设置或自定义安装配置 Azure AD Connect。 如果使用了“自定义安装”选项，则可能并不拥有密码哈希同步[所需的权限](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions)。

Azure AD Connect Active Directory 域服务 (AD DS) 服务帐户需要以下权限才能同步密码哈希。

* 复制目录更改
* 复制所有目录更改

现在非常适合验证林中的所有域是否拥有这些权限。

### <a name="plan-the-migration-method"></a>规划迁移方法

可通过两种方法从联合标识管理迁移到密码哈希同步和无缝单一登录 (SSO)。 所用的方法取决于 AD FS 实例的最初配置方式。

* **Azure AD Connect**。 如果最初使用 Azure AD Connect 配置了 AD FS，则必须使用 Azure AD Connect 向导更改为密码哈希同步。

   ‎当你更改用户登录方法时，Azure AD Connect 会自动运行 **Set-MsolDomainAuthentication** cmdlet。 Azure AD Connect 会自动取消联合 Azure AD 租户中所有已验证的联合域。

   > [!NOTE]
   > 目前，如果最初使用 Azure AD Connect 配置了 AD FS，在将用户登录方法更改为密码哈希同步时，无法避免取消联合租户中的所有域。 ‎
* **配合使用 Azure AD Connect 和 PowerShell**。 仅当 AD FS 最初不是使用 Azure AD Connect 配置的时，才可以使用此方法。 对于此选项，仍需要通过 Azure AD Connect 向导更改用户登录方法。 但此选项的重要差别在于，向导不会自动运行 **Set-MsolDomainAuthentication** cmdlet。 使用此选项可以全面控制转换的域和转换顺序。

若要了解应使用哪种方法，请完成以下部分中的步骤。

#### <a name="verify-current-user-sign-in-settings"></a>验证当前用户登录设置

验证当前用户登录设置：

1. 使用全局管理员帐户登录到 [Azure AD 门户](https://aad.portal.azure.com/)。
2. 在“用户登录”部分验证以下设置：
   * “联合身份验证”设置为“已启用”。
   * “无缝单一登录”设置为“已禁用”。
   * “直通身份验证”设置为“已禁用”。

   ![Azure AD Connect“用户登录”部分中的设置屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>验证 Azure AD Connect 配置

1. 在 Azure AD Connect 服务器上打开 Azure AD Connect。 选择“配置”。
2. 在“其他任务”页上，依次选择“查看当前配置”、“下一步”。<br />

   ![在“其他任务”页上选择的“查看当前配置”选项的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. 在“查看解决方案”页上，记下“密码哈希同步”的状态。<br /> 

   * 如果“密码哈希同步”设置为“已禁用”，请遵循本文中的步骤将它启用。
   * 如果“密码哈希同步”设置为“已启用”，则可以放心跳过本文的“步骤 1：启用密码哈希同步”部分。
4. 在“查看解决方案”页上，滚动到“Active Directory 联合身份验证服务(AD FS)”。<br />

   * 如果此部分显示了 AD FS 配置，则可以肯定 AD FS 最初是使用 Azure AD Connect 配置的。 可以使用 Azure AD Connect 的“更改用户登录”选项将域从联合标识转换为托管标识。 “选项 A：使用 Azure AD Connect 从联合身份验证切换到密码哈希同步”部分详细介绍了该过程。
   * 如果当前设置中未列出 AD FS，则必须使用 PowerShell 手动将域从联合标识转换为托管标识。 有关该过程的详细信息，请参阅“选项 B：使用 Azure AD Connect 和 PowerShell 从联合身份验证切换到密码哈希同步”部分。

### <a name="document-current-federation-settings"></a>阐述当前联合身份验证设置

若要查找当前的联合身份验证设置，请运行 **Get-MsolDomainFederationSettings** cmdlet：

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

示例：

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

验证可能已根据联合身份验证设计和部署文档自定义的任何设置。 具体而言，请查找 **PreferredAuthenticationProtocol**、**SupportsMfa** 和 **PromptLoginBehavior** 中的自定义项。

有关详细信息，请参阅以下文章：

* [AD FS prompt=login 参数支持](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> 如果 **SupportsMfa** 设置为 **True**，则表示你正在使用本地多重身份验证解决方案将第二因素质询注入到用户身份验证流。 此设置不再适用于 Azure AD 身份验证方案。 
>
> 应该使用基于云的 Azure 多重身份验证服务来执行相同的功能。 在继续之前，请仔细评估多重身份验证要求。 在转换域之前，请确保了解 Azure 多重身份验证的用法、许可影响，以及用户注册过程。

#### <a name="back-up-federation-settings"></a>备份联合身份验证设置

尽管在执行本文所述的过程期间不会对 AD FS 场中的其他信赖方进行任何更改，但我们建议为 AD FS 场创建可用于还原的当前有效备份。 可以使用免费的 Microsoft [AD FS 快速还原工具](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)创建当前有效的备份。 使用此工具可以备份 AD FS、还原现有场，或创建新场。

如果不选择使用 AD FS 快速还原工具，则最起码应该导出“Microsoft Office 365 标识平台”信赖方信任，以及可能已添加的任何关联的自定义声明规则。 可使用以下 PowerShell 示例导出信赖方信任和关联的声明规则：

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>部署注意事项和 AD FS 用法

本部分介绍部署注意事项和 AD FS 的详细用法。

### <a name="current-ad-fs-use"></a>当前的 AD FS 用法

从联合标识转换为托管标识之前，请仔细检查目前如何将 AD FS 用于 Azure AD、Office 365 和其他应用程序（信赖方信任）。 具体而言，请考虑下表中所述的场景：

| 如果 | 那么 |
|-|-|
| 打算对其他这些应用程序（非 Azure AD 和 Office 365）保留使用 AD FS。 | 转换域后，将同时使用 AD FS 和 Azure AD。 考虑用户体验。 在某些情况下，用户可能需要进行身份验证两次，一次是针对 Azure AD（然后用户可以通过 SSO 访问 Office 365 等其他应用程序），另一次是针对仍以信赖方信任方式绑定到 AD FS 的任何应用程序再次进行身份验证。 |
| AD FS 实例经过重度的自定义，并依赖于 onload.js 文件中的特定自定义设置（例如，你已更改登录体验，使用户只需以 **SamAccountName** 格式输入其用户名而不是用户主体名称 (UPN)；或者组织在登录体验中使用了众多的品牌设计）。 不能在 Azure AD 中复制 onload.js 文件。 | 在继续之前，必须验证 Azure AD 是否可以满足当前自定义要求。 如需更多信息和指导，请参阅有关 AD FS 品牌和 AD FS 自定义的部分。|
| 使用 AD FS 阻止旧版身份验证客户端。| 考虑将用于阻止旧版身份验证客户端的 AD FS 控制机制替换为[条件访问控制](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)和 [Exchange Online 客户端访问规则](https://aka.ms/EXOCAR)的组合。 |
| 要求用户在 AD FS 中进行身份验证时对本地多重身份验证服务器解决方案执行多重身份验证。| 在托管标识域中，无法通过本地多重身份验证解决方案将多重身份验证质询注入到身份验证流。 但是，在转换域后，可以使用 Azure 多重身份验证服务进行多重身份验证。<br /><br /> 如果用户当前未使用 Azure 多重身份验证，则需要执行一次性的用户注册步骤。 必须准备好将规划的注册过程传达给用户。 |
| 目前在 AD FS 中使用访问控制策略（AuthZ 规则）来控制对 Office 365 的访问。| 考虑将这些策略替换为等效的 Azure AD [条件访问策略](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)和 [Exchange Online 客户端访问规则](https://aka.ms/EXOCAR)。|

### <a name="common-ad-fs-customizations"></a>常见的 AD FS 自定义项

本部分介绍常见的 AD FS 自定义项。

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork 声明

如果用户身份验证在企业网络内部进行，则 AD FS 会颁发 **InsideCorporateNetwork** 声明。 然后，可将此声明传递给 Azure AD。 此声明用于根据用户的网络位置绕过多重身份验证。 若要了解如何确定此功能当前是否在 AD FS 中启用，请参阅[联合用户的受信任 IP](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)。

将域转换为密码哈希同步后，**InsideCorporateNetwork** 声明不再可用。 可以使用 [Azure AD 中的命名位置](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations)来取代此功能。

配置命名位置后，必须更新配置为包含或排除网络“所有受信任位置”或“MFA 信任的 IP”值的所有条件访问策略，以反映新的命名位置。

有关条件访问中的“位置”条件的详细信息，请参阅 [Active Directory 条件访问位置](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)。

#### <a name="hybrid-azure-ad-joined-devices"></a>已加入混合 Azure AD 的设备

将设备加入 Azure AD 时，可以创建条件访问规则，以强制要求设备符合访问标准以及安全与合规性要求。 此外，用户可以使用组织的工作或学校帐户（而不是个人帐户）登录到设备。 使用已加入混合 Azure AD 的设备可将已加入 Active Directory 域的设备加入到 Azure AD。 你的联合环境可能已设置为使用此功能。

为了确保在将域转换为密码哈希同步之后，混合加入仍适用于任何已加入域的设备，对于 Windows 10 客户端，必须使用 Azure AD Connect 将 Active Directory 计算机帐户同步到 Azure AD。 

对于 Windows 8 和 Windows 7 计算机帐户，混合加入将使用无缝 SSO 在 Azure AD 中注册计算机。 无需像在 Windows 10 设备上一样同步 Windows 8 和 Windows 7 计算机帐户。 但是，必须（通过一个 .msi 文件）将更新的 workplacejoin.exe 文件部署到 Windows 8 和 Windows 7 客户端，使它们能够使用无缝 SSO 自我注册。 [下载 .msi 文件](https://www.microsoft.com/download/details.aspx?id=53554)。

有关详细信息，请参阅[配置已加入混合 Azure AD 的设备](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)。

#### <a name="branding"></a>品牌

如果你的组织已[自定义 AD FS 登录页](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization)以使显示的内容与组织更相关，请考虑[在 Azure AD 登录页中使用类似的自定义项](https://docs.microsoft.com/azure/active-directory/customize-branding)。

尽管可以使用类似的自定义项，但转换后，登录页上预期会发生一定的视觉变化。 可以在发送给用户的信件中提供有关预期变化的信息。

> [!NOTE]
> 只有购买了 Azure Active Directory 的 Premium 或 Basic 许可证或者拥有 Office 365 许可证，才能使用组织品牌功能。

## <a name="plan-deployment-and-support"></a>规划部署和支持

完成本部分所述的任务有助于规划部署和支持。

### <a name="plan-the-maintenance-window"></a>规划维护时段

尽管域转换过程的速度相对较快，但在完成域转换后的最长四小时内，Azure AD 仍可能向 AD FS 服务器发送一些身份验证请求。 在此四小时期限内，根据不同的服务端缓存，Azure AD 可能不接受这些身份验证。 用户可能会收到错误。 用户仍可针对 AD FS 成功完成身份验证，但是，Azure AD 不再接受用户的颁发令牌，因为该联合信任现已删除。

这只会影响在清除服务端缓存之前，在此后期转换时段通过浏览器访问服务的用户。 旧式客户端（Exchange ActiveSync、Outlook 2010/2013）预期不受影响，因为 Exchange Online 会将其凭据缓存保留设置的一段时间。 该缓存用于以静默方式重新验证用户身份。 用户无需返回到 AD FS。 清除此缓存后，将使用这些客户端的设备上存储的凭据来以静默方式对客户端本身重新进行身份验证。 在完成域转换过程后，用户预期不会收到任何密码提示。 

新式身份验证客户端（Office 2016 和 Office 2013、iOS 及 Android 应用）使用有效的刷新令牌获取新的访问令牌以持续访问资源，而无需返回到 AD FS。 在完成域转换过程后，有无密码提示对这些客户端而言并不重要。 无需进行额外的配置，这些客户端就能持续正常运行。

> [!IMPORTANT]
> 在验证所有用户可以使用云身份验证成功完成身份验证之前，请不要关闭 AD FS 环境或删除 Office 365 信赖方信任。

### <a name="plan-for-rollback"></a>规划回滚

如果遇到了不可快速解决的重大问题，可将解决方案回滚到联合身份验证。 必须规划好当部署无法按预期实施时要怎样做。 如果部署期间域或用户转换失败，或者需要回滚到联合身份验证，则必须了解如何应对任何服务中断和减小对用户的影响。

#### <a name="to-roll-back"></a>回滚

若要规划回滚，请查看联合身份验证设计和部署文档，以获取具体的部署详细信息。 该过程应包括以下任务：

* 使用 **Convert-MSOLDomainToFederated** cmdlet 将托管域转换为联合域。
* 根据需要配置其他声明规则。

### <a name="plan-communications"></a>规划沟通

规划部署和支持的一个重要组成部分是确保主动通知最终用户将要发生哪些变化。 用户应该提前知道他们可能会遇到哪种情况，以及需要做些什么。 

部署密码哈希同步和无缝 SSO 后，访问通过 Azure AD 进行身份验证的 Office 365 和其他资源时，用户登录体验将会发生变化。 网络外部的用户只会看到 Azure AD 登录页。 这些用户不会重定向到面向外部的 Web 应用程序代理服务器提供的基于窗体的页。

在沟通策略中包括以下要素：

* 通过以下方式通知用户即将发布和已发布哪些功能：
   * 电子邮件和其他内部沟通渠道。
   * 海报等视觉对象。
   * 行政现场发布会或其他沟通方式。
* 确定由谁自定义信件、谁发送信件，以及何时发送。

## <a name="implement-your-solution"></a>实施解决方案

规划好解决方案后， 可将其实施。 实施涉及以下组成部分：

* 启用密码哈希同步。
* 准备无缝 SSO。
* 将登录方法更改为密码哈希同步并启用无缝 SSO。

### <a name="step-1-enable-password-hash-synchronization"></a>步骤 1：启用密码哈希同步

实施此解决方案的第一步是使用 Azure AD Connect 向导启用密码哈希同步。 密码哈希同步是一个可选功能，可以使用联合身份验证在环境中启用。 这不会对身份验证流造成任何影响。 在这种情况下，Azure AD Connect 将开始同步密码哈希，而不影响用户使用联合身份验证登录。

出于此原因，我们建议在更改域登录方法之前，以准备任务的形式完成此步骤。 然后，便有充足的时间来验证密码哈希同步是否正常工作。

启用密码哈希同步：

1. 在 Azure AD Connect 服务器上打开 Azure AD Connect 向导，然后选择“配置”。
2. 依次选择“自定义同步选项”、“下一步”。
3. 在“连接到 Azure AD”页上，输入全局管理员帐户的用户名和密码。
4. 在“连接目录”页上选择“下一步”。
5. 在“域和 OU 筛选”页上选择“下一步”。
6. 在“可选功能”页上，依次选择“密码同步”、“下一步”。
 
   ![在“可选功能”页上选择的“密码同步”选项的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. 在接下来的页上选择“下一步”。 在最后一个页上选择“配置”。
8. 下次同步时，Azure AD Connect 将开始同步密码哈希。

启用密码哈希同步后，Azure AD Connect 同步范围内所有用户的密码哈希将重新进行哈希处理，并写入 Azure AD。 根据用户数量，此操作可能需要花费几分钟到几小时。

在规划时，应该预计在 1 小时内大约可以处理 20,000 个用户。

若要验证密码哈希同步是否正常工作，请完成 Azure AD Connect 向导中的“故障排除”任务。

1. 使用“以管理员身份运行”选项，在 Azure AD Connect 服务器上打开一个新的 Windows PowerShell 会话。
2. 运行 `Set-ExecutionPolicy RemoteSigned` 或 `Set-ExecutionPolicy Unrestricted`。
3. 启动 Azure AD Connect 向导。
4. 转到“其他任务”页，并依次选择“故障排除”、“下一步”。
5. 在“故障排除”页上，选择“启动”以在 PowerShell 中启动故障排除菜单。
6. 在主菜单中，选择“排查密码哈希同步问题”。
7. 在子菜单中，选择“密码哈希同步根本不工作”。

若要排查问题，请参阅[使用 Azure AD Connect 同步排查密码哈希同步问题](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)。

### <a name="step-2-prepare-for-seamless-sso"></a>步骤 2：准备无缝 SSO

要让设备使用无缝 SSO，必须使用 Active Directory 中的组策略将一个 Azure AD URL 添加到用户的 Intranet 区域设置。

默认情况下，浏览器将自动从 URL 计算正确的区域（Internet 或 Intranet）。 例如，**http:\/\/contoso/** 映射到 Intranet 区域，而 **http:\/\/intranet.contoso.com** 映射到 Internet 区域（因为此 URL 包含句点）。 仅当显式将 URL 添加到浏览器的 Intranet 区域时，浏览器才会将 Kerberos 票证发送到云终结点（例如 Azure AD URL）。

请完成[这些步骤](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)在设备上实施所需的更改。

> [!IMPORTANT]
> 进行此项更改不会修改用户登录到 Azure AD 的方式。 但是，必须在继续之前将此配置应用到所有设备。 在尚未收到此配置的设备上登录的用户只需输入用户名和密码即可登录到 Azure AD。

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>步骤 3：将登录方法更改为密码哈希同步并启用无缝 SSO

可以使用两个选项将登录方法更改为密码哈希同步并启用无缝 SSO。

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>选项 A：使用 Azure AD Connect 从联合身份验证切换到密码哈希同步

如果最初使用 Azure AD Connect 配置了 AD FS 环境，请使用此方法。 如果最初不是使用 Azure AD Connect 配置了 AD FS 环境，则不能使用此方法。

首先更改登录方法：

1. 在 Azure AD Connect 服务器上打开 Azure AD Connect 向导。
2. 依次选择“更改用户登录”、“下一步”。 

   ![“其他任务”页上的“更改用户登录”选项的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. 在“连接到 Azure AD”页上，输入全局管理员帐户的用户名和密码。
4. 在“用户登录”页上，选择“密码哈希同步”按钮。 并确保选中“不要转换用户帐户”框。 该选项已过时。 依次选择“启用单一登录”、“下一步”。

   ![“启用单一登录”页的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > 从 Azure AD Connect 版本 1.1.880.0 开始，默认会选中“无缝单一登录”复选框。

   > [!IMPORTANT]
   > 可以放心忽略有关在从联合身份验证转换为云身份验证时，必须执行用户转换和完全密码哈希同步的步骤的警告。 请注意，不再需要执行这些步骤。 如果仍看到这些警告，请确保运行最新版本的 Azure AD Connect，并使用本指南的最新版本。 有关详细信息，请参阅[更新 Azure AD Connect](#update-azure-ad-connect) 部分。

5. 在“启用单一登录”页上输入域管理员帐户的凭据，然后选择“下一步”。

   ![“启用单一登录”页的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > 需要使用域管理员帐户凭据来启用无缝 SSO。 该过程将完成以下操作，而这些操作需要这些提升的权限。 域管理员帐户凭据不存储在 Azure AD Connect 或 Azure AD 中。 域管理员帐户凭据仅用于启用该功能。 成功完成该过程后会丢弃这些凭据。
   >
   > 1. 在本地 Active Directory 实例中创建名为 AZUREADSSOACC 的计算机帐户（表示 Azure AD）。
   > 2. 计算机帐户的 Kerberos 解密密钥与 Azure AD 安全共享。
   > 3. 创建两个 Kerberos 服务主体名称 (SPN) 来表示 Azure AD 登录期间使用的两个 URL。

6. 在“已准备好进行配置”页上，确保已选中“配置完成后启动同步过程”复选框。 然后选择“配置”。

      ![“准备好配置”页的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > 此时，所有联合域将更改为托管身份验证。 密码哈希同步是新的身份验证方法。

7. 在 Azure AD 门户中，选择“Azure Active Directory” > “Azure AD Connect”。
8. 验证以下设置：
   * “联合身份验证”设置为“已禁用”。
   * “无缝单一登录”设置为“已启用”。
   * “密码同步”设置为“已启用”。<br /> 

   ![显示“用户登录”部分中的设置的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

跳到[测试和后续步骤](#testing-and-next-steps)。

   > [!IMPORTANT]
   > 跳过“选项 B：使用 Azure AD Connect 和 PowerShell 从联合身份验证切换到密码哈希同步”部分。 如果已选择使用“选项 A”将登录方法更改为密码哈希同步并启用无缝 SSO，则“选项 B”部分中的步骤不适用。

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>选项 B：使用 Azure AD Connect 和 PowerShell 从联合身份验证切换到密码哈希同步

如果联合域最初不是使用 Azure AD Connect 配置的，请使用此选项。 在此过程中，将启用无缝 SSO，并将联合域切换到托管域。

1. 在 Azure AD Connect 服务器上打开 Azure AD Connect 向导。
2. 依次选择“更改用户登录”、“下一步”。
3. 在“连接到 Azure AD”页上，输入全局管理员帐户的用户名和密码。
4. 在“用户登录”页上，选择“密码哈希同步”按钮。 依次选择“启用单一登录”、“下一步”。

   在启用密码哈希同步之前：![显示“用户登录”页上的“不要配置”选项的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   在启用密码哈希同步之后：![显示用户在登录页上的新选项的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > 从 Azure AD Connect 版本 1.1.880.0 开始，默认会选中“无缝单一登录”复选框。

5. 在“启用单一登录”页上输入域管理员帐户的凭据，然后选择“下一步”。

   > [!NOTE]
   > 需要使用域管理员帐户凭据来启用无缝 SSO。 该过程将完成以下操作，而这些操作需要这些提升的权限。 域管理员帐户凭据不存储在 Azure AD Connect 或 Azure AD 中。 域管理员帐户凭据仅用于启用该功能。 成功完成该过程后会丢弃这些凭据。
   >
   > 1. 在本地 Active Directory 实例中创建名为 AZUREADSSOACC 的计算机帐户（表示 Azure AD）。
   > 2. 计算机帐户的 Kerberos 解密密钥与 Azure AD 安全共享。
   > 3. 创建两个 Kerberos 服务主体名称 (SPN) 来表示 Azure AD 登录期间使用的两个 URL。

6. 在“已准备好进行配置”页上，确保已选中“配置完成后启动同步过程”复选框。 然后选择“配置”。

   ![显示“已准备好进行配置”页上的“配置”按钮的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   选择“配置”按钮时，将会根据上一步骤中的指定配置无缝 SSO。 不会修改密码哈希同步配置，因为之前已将其启用。

   > [!IMPORTANT]
   > 此时不会对用户登录方法进行任何更改。

7. 在 Azure AD 门户中，验证以下设置：
   * “联合身份验证”设置为“已启用”。
   * “无缝单一登录”设置为“已启用”。
   * “密码同步”设置为“已启用”。

   ![显示“用户登录”部分中的设置的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>将联合域转换为托管域

此时，联合身份验证仍为已启用，并可以对域正常运行。 若要继续部署，需将每个域从联合域转换为托管域，以强制通过密码哈希同步进行用户身份验证。

> [!IMPORTANT]
> 无需同时转换所有域。 可以选择从生产租户中的某个测试域着手，或者从用户数量最少的域着手。

使用 Azure AD PowerShell 模块完成转换：

1. 在 PowerShell 中，使用全局管理员帐户登录到 Azure AD。
2. 若要转换第一个域，请运行以下命令：

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. 在 Azure AD 门户中，选择“Azure Active Directory” > “Azure AD Connect”。
4. 运行以下命令，验证是否已将该域转换为托管域：

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>测试和后续步骤

完成以下任务以验证密码哈希同步并完成转换过程。

### <a name="test-authentication-by-using-password-hash-synchronization"></a>使用密码哈希同步测试身份验证 

以前，当租户使用联合标识时，用户会从 Azure AD 登录页重定向到你的 AD FS 环境。 现在，将租户配置为使用密码哈希同步而不是联合身份验证之后，用户不会重定向到 AD FS， 而是直接通过 Azure AD 登录页登录。

测试密码哈希同步：

1. 在 InPrivate 模式下打开 Internet Explorer，以避免无缝 SSO 自动将你登录。
2. 转到 Office 365 登录页 ([https://portal.office.com](https://portal.office.com/))。
3. 输入用户 UPN，然后选择“下一步”。 请务必输入已从本地 Active Directory 实例同步的，并且事先已使用联合身份验证的混合用户的 UPN。 此时会显示一个页面，可在其中输入用户名和密码：

   ![显示用于输入用户名的登录页的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![显示用于输入密码的登录页的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. 输入密码并选择“登录”后，将重定向到 Office 365 门户。

   ![显示 Office 365 门户的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>测试无缝 SSO

1. 登录到已连接到企业网络的且已加入域的计算机。
2. 打开 Internet Explorer 或 Chrome 并转到以下 URL 之一（请将“contoso”替换为你的域）：

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   片刻之后，用户将重定向到 Azure AD 登录页，其中显示了消息“正在尝试将你登录”。 系统不会提示用户输入用户名或密码。<br />

   ![显示 Azure AD 登录页和消息的屏幕截图](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. 用户将重定向并成功登录到访问面板：

   > [!NOTE]
   > 无缝 SSO 可在支持域提示（例如 myapps.microsoft.com/contoso.com）的 Office 365 服务中正常工作。 Office 365 门户 (portal.office.com) 目前不支持域提示。 用户需要输入 UPN。 输入 UPN 之后，无缝 SSO 将代表用户检索 Kerberos 票证。 用户无需输入密码即可登录。

   > [!TIP]
   > 为了改善 SSO 体验，请考虑部署 [Windows 10 上的 Azure AD 混合加入](https://docs.microsoft.com/azure/active-directory/device-management-introduction)。

### <a name="remove-the-relying-party-trust"></a>删除信赖方信任

验证所有用户和客户端已通过 Azure AD 成功完成身份验证后，可以安全删除 Office 365 信赖方信任。

如果未将 AD FS 用于其他目的（即，其他信赖方信任），则现在可以安全解除 AD FS。

### <a name="rollback"></a>回退

如果发现了不可快速解决的重大问题，可以选择将解决方案回滚到联合身份验证。

请查阅联合身份验证设计和部署文档，以获取具体的部署详细信息。 该过程应涉及以下任务：

* 使用 **Convert-MSOLDomainToFederated** cmdlet 将托管域转换为联合身份验证。
* 根据需要配置其他声明规则。

### <a name="sync-userprincipalname-updates"></a>同步 userPrincipalName 更新

在过去，除非以下两个条件都成立，否则会阻止在本地环境中使用同步服务对 **UserPrincipalName** 属性进行更新：

* 用户在托管的（非联合）标识域中。
* 没有为用户分配许可证。

若要了解如何验证或启用此功能，请参阅[同步 userPrincipalName 更新](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)。

### <a name="troubleshooting"></a>故障排除

支持团队应了解如何排查在从联合身份验证更改为托管身份验证期间或之后出现的任何身份验证问题。 使用以下故障排除文档可帮助支持团队熟悉常见的故障排除步骤，以及可帮助找出和解决问题的相应操作。

[排查 Azure Active Directory 密码哈希同步问题](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[排查 Azure Active Directory 无缝单一登录问题](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>滚动更新无缝 SSO 的 Kerberos 解密密钥

必须经常滚动更新 AZUREADSSOACC 计算机帐户（代表 Azure AD）的 Kerberos 解密密钥。 AZUREADSSOACC 计算机帐户在本地 Active Directory 林中创建。 我们强烈建议每隔 30 天至少滚动更新 Kerberos 解密密钥一次，以便与 Active Directory 域成员提交密码更改的频率保持一致。 没有任何关联的设备附加到 AZUREADSSOACC 计算机帐户对象，因此必须手动执行滚动更新。

请在运行 Azure AD Connect 的本地服务器上启动无缝 SSO Kerberos 解密密钥的滚动更新。

有关详细信息，请参阅[如何滚动更新 AZUREADSSOACC 计算机帐户的 Kerberos 解密密钥？](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)。

## <a name="next-steps"></a>后续步骤

* 了解 [Azure AD Connect 设计概念](plan-connect-design-concepts.md)。
* 选择[适当的身份验证](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)。
* 了解[支持的拓扑](plan-connect-design-concepts.md)。
