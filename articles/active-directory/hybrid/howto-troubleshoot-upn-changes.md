---
title: 计划和故障排除 Azure 用户原则名称 （UPN） 更改
description: 了解 UPN 更改的已知问题和缓解措施
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11be1d971922095d4a1ace1c81c763134b4e58c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743331"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>在 Azure 活动目录中规划和排除用户主体名称更改

用户主体名称 （UPN） 是用户帐户的互联网通信标准的属性。 UPN 由 UPN 前缀（用户帐户名）和 UPN 后缀（DNS 域名）组成。 前缀使用"+"符号连接后缀。 例如，someone@example.com 。 UPN 必须在目录林中的所有安全主体对象之间保持唯一。 

> [!NOTE]
> 对于开发人员，我们建议您使用用户对象 ID 作为不可变标识符，而不是 UPN。 如果您的应用程序当前正在使用 UPN，我们建议您设置 UPN 以匹配用户的主要电子邮件地址，以改善其体验。<br> **在混合环境中，用户的 UPN 在本地目录和 Azure 活动目录中必须相同，这一点很重要**。

**本文假定您使用 UPN 作为用户标识符。它涉及 UPN 更改的规划，以及从 UPN 更改可能导致的问题中恢复的问题。**

## <a name="learn-about-upns-and-upn-changes"></a>了解 UPN 和 UPN 更改
登录页面通常会提示用户输入其电子邮件地址时，所需的值实际上是他们的 UPN。 因此，您应该确保在用户的主要电子邮件地址更改时更改其 UPN。

用户的主要电子邮件地址可能会由于多种原因而更改：

* 公司重塑品牌

* 员工转移到不同的公司部门 

* 合并和收购

* 员工姓名更改

### <a name="types-of-upn-changes"></a>UPN 更改的类型

您可以通过更改前缀、后缀或两者来更改 UPN。

* **更改前缀**。

   *  例如，如果某人的姓名已更改，您可以更改其帐户名称：  
BSimon@contoso.com到BJohnson@contoso.com

   * 您还可以更改前缀的公司标准：  
Bsimon@contoso.com到Britta.Simon@contoso.com

* **更改后缀**。 <br>

    例如，如果一个人更改了部门，则可以更改他们的域： 

   * Britta.Simon@contoso.com 到 Britta.Simon@contosolabs.com <br>
     或<br>
    * Britta.Simon@corp.contoso.com 到 Britta.Simon@labs.contoso.com 

每次更新用户的主电子邮件地址时，更改用户的 UPN。 无论电子邮件更改的原因如何，必须始终更新 UPN 以匹配。

在从活动目录到 Azure AD 的初始同步期间，请确保用户的电子邮件与其 UPN 相同。

### <a name="upns-in-active-directory"></a>活动目录中的 UPN

在活动目录中，默认的 UPN 后缀是创建用户帐户的域的 DNS 名称。 在大多数情况下，这是您在互联网上注册为企业域的域名。 如果在contoso.com域中创建用户帐户，则默认的 UPN 是

username@contoso.com

 但是，您可以使用活动目录域和信任[添加更多 UPN 后缀](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)。 

例如，您可能希望添加labs.contoso.com，并让用户的 UPN 和电子邮件反映这一点。 然后，他们将成为

username@labs.contoso.com.

>[!IMPORTANT]
> 如果活动目录中的 UPN 和 Azure 活动目录不匹配，则会出现问题。 如果要更改[Active Directory 中的后缀](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)，必须确保[已在 Azure AD 上添加和验证](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)了匹配的自定义域名。 

![已验证域的屏幕截图](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure 活动目录中的 UPN

用户使用其用户主体名称属性中的值登录到 Azure AD。 

将 Azure AD 与本地活动目录结合使用时，将使用 Azure AD 连接服务同步用户帐户。 默认情况下，Azure AD 连接向导使用本地活动目录中的用户主体名称属性作为 Azure AD 中的 UPN。 您可以在自定义安装中将其更改为其他属性。

更新单个用户或整个组织的用户主体名称 （UPN） 时，必须拥有定义的流程。 

请参阅本文档中的已知问题和解决方法。

将用户帐户从活动目录同步到 Azure AD 时，请确保活动目录中的 UPN 映射到 Azure AD 中的已验证域。

![已验证域的屏幕截图](./media/howto-troubleshoot-upn-changes/verified-domains.png)

如果用户主体名称属性的值与 Azure AD 中的已验证域不对应，则同步过程将后缀替换为默认的 .onmicrosoft.com 值。


### <a name="roll-out-bulk-upn-changes"></a>推出批量 UPN 更改

遵循批量 UPN 更改[的试点最佳实践](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)。 如果发现无法快速解决的问题，还可以制定用于恢复 UPN 的经过测试的回滚计划。 运行试验后，您可以开始定位具有各种组织角色的小用户集及其特定应用或设备集。

通过这第一个用户子集，您将清楚地了解用户在更改中应该期待什么。 在用户通信中包括此信息。

创建用于更改单个用户上的 UPN 的已定义过程，作为正常操作的一部分。 我们建议采用一个经过测试的过程，其中包括有关已知问题和解决方法的文档。

以下各节详细介绍了更改 UPN 时的潜在已知问题和解决方法。

## <a name="apps-known-issues-and-workarounds"></a>应用已知问题和解决方法

[软件即服务 （SaaS）](https://azure.microsoft.com/overview/what-is-saas/)和业务线 （LoB） 应用程序通常依靠 UPN 来查找用户并存储用户配置文件信息，包括角色。 首次使用["及时"预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)在用户首次登录应用时创建用户配置文件的应用程序可能会受到 UPN 更改的影响。

**已知问题**<br>
更改用户的 UPN 可能会中断 Azure AD 用户与在应用程序上创建的用户配置文件之间的关系。 如果应用程序使用["及时"预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)，则可能会创建全新的用户配置文件。 这将要求应用程序管理员进行手动更改以修复此关系。

**解决 方案**<br>
[Azure AD 自动用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)允许您在支持的云应用程序中自动创建、维护和删除用户标识。 在应用程序上配置自动用户预配会自动更新应用程序中的 UPN。 将应用程序作为渐进式部署的一部分进行测试，以验证它们是否不受 UPN 更改的影响。
如果您是开发人员，请考虑[向应用程序添加 SCIM 支持](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)，以启用 Azure 活动目录的自动用户预配。 

## <a name="managed-devices-known-issues-and-workarounds"></a>托管设备已知问题和解决方法

通过将[设备带到 Azure AD，](https://docs.microsoft.com/azure/active-directory/devices/overview)通过云和本地资源的单一登录 （SSO） 来最大限度地提高用户的工作效率。

### <a name="azure-ad-joined-devices"></a>Azure AD 加入设备

[Azure AD 联接](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join)的设备将直接加入 Azure AD，并允许用户使用其组织的身份登录到设备。

**已知问题** <br>
对于依赖于 Azure AD 进行身份验证的应用程序，用户可能会遇到单一登录问题。

**解决 方案** <br>
留出足够的时间进行 UPN 更改以同步到 Azure AD。 验证新的 UPN 是否反映在 Azure AD 门户上后，请要求用户选择"其他用户"磁贴以使用其新 UPN 登录。 您也可以通过[PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)进行验证。 使用新 UPN 登录后，对旧 UPN 的引用可能仍出现在"访问工作或学校"Windows 设置中。

![已验证域的屏幕截图](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>混合 Azure AD 加入设备

[混合 Azure AD 联接](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid)的设备将连接到活动目录和 Azure AD。 如果环境具有本地活动目录占用空间，并且还希望从 Azure AD 提供的功能中获益，则可以实现混合 Azure AD 联接。

**已知问题** 

Windows 10 混合 Azure AD 联接的设备可能会遇到意外的重新启动和访问问题。

如果用户在新 UPN 已同步到 Azure AD 之前登录到 Windows，或者继续使用现有的 Windows 会话，则如果已配置为强制使用混合联接设备访问资源，则使用 Azure AD 进行身份验证的应用程序可能会遇到单一登录问题。 

此外，将显示以下消息，强制在一分钟后重新启动。 

"您的电脑将在一分钟内自动重新启动。 Windows 出现问题，需要重新启动。 您应立即关闭此消息并保存您的工作"。

**解决 方案** 

设备必须从 Azure AD 取消连接并重新启动。 重新启动后，设备将自动再次加入 Azure AD，用户必须通过选择"其他用户"磁贴使用新的 UPN 登录。 要从 Azure AD 取消联接设备，在命令提示符下运行以下命令：

**dsregcmd /离开**

如果用户正在使用 Windows Hello 业务，则需要[重新注册](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision)它。 在 UPN 更改后，Windows 7 和 8.1 设备不受此问题的影响。

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>微软身份验证器已知问题和解决方法

您的组织可能需要使用[Microsoft 身份验证器应用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)来登录和访问组织应用程序和数据。 尽管应用中可能会出现用户名，但在用户完成注册过程之前，帐户不会设置为充当验证方法。

[微软身份验证器应用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)有四个主要功能：

* 通过推送通知或验证码进行多重身份验证

* 在 iOS 和 Android 设备上充当身份验证代理，为使用[代理身份验证](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)的应用程序提供单一登录

* Azure AD 的设备注册（也称为工作区联接），这是其他功能（如 Intune 应用保护和设备注册/管理）的要求，

* 电话登录，这需要 MFA 和设备注册。

### <a name="multi-factor-authentication-with-android-devices"></a>使用 Android 设备的多重身份验证

Microsoft 身份验证器应用提供了带外验证选项。 [多因素身份验证 （MFA）](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)在登录期间不向用户发送自动电话呼叫或 SMS，而是将通知推送到用户的智能手机或平板电脑上的 Microsoft 身份验证器应用。 用户只需在应用中点击"批准"（或输入 PIN 或生物识别，然后点击"身份验证"即可完成其登录。

**已知问题** 

更改用户的 UPN 时，旧 UPN 仍显示在用户帐户上，可能不会收到通知。 [验证码](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq)继续工作。

**解决 方案**

如果收到通知，请指示用户关闭通知，打开身份验证器应用，点按"检查通知"选项并批准 MFA 提示。 在此之后，帐户上显示的 UPN 将更新。 请注意，更新的 UPN 可能会显示为新帐户，这是因为正在使用其他身份验证器功能。 有关详细信息，请参阅本文中的其他已知问题。

### <a name="brokered-authentication"></a>代理身份验证

在 Android 和 iOS 代理（如 Microsoft 身份验证器）上启用：

* 单次登录 （SSO） - 您的用户无需登录到每个应用程序。

* 设备标识 - 代理访问在加入工作区时在设备上创建的设备证书。

* 应用程序标识验证 - 当应用程序调用代理时，它将传递其重定向 URL，并且代理验证它。

此外，它允许应用程序参与更高级的功能，如[条件访问](https://docs.microsoft.com/azure/active-directory/conditional-access/)，并支持 Microsoft [Intune 方案](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)。

**已知问题**<br>
由于应用程序传递的login_hint与存储在代理上的 UPN 不匹配，用户在使用代理辅助登录的新应用程序上会收到更多的交互式身份验证提示。

**解决 方案** <br> 用户需要手动从 Microsoft 身份验证器中删除帐户，并从代理辅助应用程序启动新的登录。 该帐户将在初始身份验证后自动添加。

### <a name="device-registration"></a>设备注册

Microsoft 身份验证器应用负责将设备注册到 Azure AD。 设备注册允许设备对 Azure AD 进行身份验证，并且是以下方案的要求：

* Intune 应用保护

* 调谐设备注册

* 电话登录

**已知问题**<br>
更改 UPN 时，Microsoft 身份验证器应用上将显示新 UPN 的新帐户，而旧 UPN 的帐户仍列出。 此外，旧的 UPN 显示在应用设置上的"设备注册"部分。 设备注册的正常功能或受抚养方案没有变化。

**解决 方案** <br> 要删除 Microsoft 身份验证器应用上对旧 UPN 的所有引用，请指示用户手动从 Microsoft 身份验证器中删除新旧帐户，重新注册 MFA 并重新加入设备。

### <a name="phone-sign-in"></a>电话登录

电话登录允许用户在没有密码的情况下登录到 Azure AD。 要启用电话登录，用户需要使用身份验证器应用注册 MFA，然后直接在身份验证器上启用电话登录。 作为配置的一部分，设备注册 Azure AD。

**已知问题** <br>
用户无法使用电话登录，因为他们没有收到任何通知。 如果用户点击"检查通知"，则会收到错误。

**解决 方案**<br>
用户需要选择启用电话登录的帐户上的下拉菜单，然后选择"禁用电话登录"。 如果需要，可以再次启用电话登录。

## <a name="security-key-fido2-known-issues-and-workarounds"></a>安全密钥 （FIDO2） 已知问题和解决方法

**已知问题** <br>
当多个用户在同一键上注册时，登录屏幕将显示显示旧 UPN 的帐户选择页。 使用安全密钥登录不受 UPN 更改的影响。  

**解决 方案**<br>
要删除对旧 UPN 的引用，用户必须[重置安全密钥并重新注册](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)。

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive 已知问题和解决方法

众所周知，OneDrive 用户在 UPN 更改后会遇到问题。 有关详细信息，请参阅[UPN 更改如何影响 OneDrive URL 和 OneDrive 功能](https://docs.microsoft.com/onedrive/upn-changes)。

## <a name="next-steps"></a>后续步骤

请参阅以下资源：
* [Azure AD Connect：设计概念](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Azure AD UserPrincipalName 填充](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Microsoft 标识平台 ID 令牌](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
