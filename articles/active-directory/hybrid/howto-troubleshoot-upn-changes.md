---
title: 规划 Azure 用户主体名称（UPN）更改并对其进行故障排除
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
ms.openlocfilehash: 885d30305ba2b186052e17b9b455b2248bca541b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608511"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>在 Azure Active Directory 中规划用户主体名称更改并进行故障排除

用户主体名称（UPN）是一个属性，该属性是用户帐户的 internet 通信标准。 UPN 由 UPN 前缀（用户帐户名）和 UPN 后缀（DNS 域名）组成。 前缀使用 "@" 符号联接后缀。 例如 someone@example.com。 UPN 必须在目录林中的所有安全主体对象之间保持唯一。 

**本文假设你使用 UPN 作为用户标识符。它用于规划 UPN 更改，并从可能由 UPN 更改引起的问题进行恢复。**

> [!NOTE]
> 对于开发人员，我们建议使用用户 objectID 作为不可变标识符，而不是 UPN 或电子邮件地址，因为它们的值可能会更改。


## <a name="learn-about-upns-and-upn-changes"></a>了解 Upn 和 UPN 更改
当所需的值实际上是其 UPN 时，登录页通常会提示用户输入其电子邮件地址。 因此，应确保在用户的主电子邮件地址发生更改时更改用户的 UPN。

用户的主要电子邮件地址可能因多种原因而发生变化：

* 公司品牌重塑

* 移动到不同公司部门的员工 

* 合并和收购

* 员工姓名更改

### <a name="types-of-upn-changes"></a>UPN 更改的类型

可以通过更改前缀和/或后缀来更改 UPN。

* **更改前缀**。

   *  例如，如果某人的名称发生了更改，你可以更改其帐户名：  
BSimon@contoso.com到BJohnson@contoso.com

   * 你还可以更改前缀的企业标准：  
Bsimon@contoso.com到Britta.Simon@contoso.com

* **更改后缀**。 <br>

    例如，如果某个人更改了部门，则可以更改其域： 

   * Britta.Simon@contoso.com 至 Britta.Simon@contosolabs.com <br>
     或<br>
    * Britta.Simon@corp.contoso.com 至 Britta.Simon@labs.contoso.com 

建议在每次更新用户的主要电子邮件地址时更改用户的 UPN。

在从 Active Directory 到 Azure AD 的初始同步过程中，请确保用户的电子邮件与其 Upn 完全相同。

### <a name="upns-in-active-directory"></a>Active Directory 中的 Upn

在 Active Directory 中，默认的 UPN 后缀是在其中创建用户帐户的域的 DNS 名称。 在大多数情况下，这是在 internet 上注册为企业域的域名。 如果在 contoso.com 域中创建用户帐户，则默认 UPN 是

username@contoso.com

 不过，你可以通过使用 Active Directory 域和信任关系来[添加更多 UPN 后缀](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)。 

例如，你可能想要添加 labs.contoso.com，并让用户的 Upn 和电子邮件反映这一情况。 然后，它们将成为

username@labs.contoso.com.

>[!IMPORTANT]
> 如果在[Active Directory 中更改后缀](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)，则必须确保已[在 Azure AD 中添加并验证](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)了匹配的自定义域名。 

![经验证的域的屏幕截图](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>Azure Active Directory 中的 Upn

用户以其 userPrincipalName 属性中的值登录到 Azure AD。 

将 Azure AD 与本地 Active Directory 结合使用时，将使用 Azure AD Connect 服务来同步用户帐户。 默认情况下，Azure AD Connect 向导使用本地 Active Directory 中的 userPrincipalName 属性作为 Azure AD 中的 UPN。 可以将其更改为自定义安装中的其他属性。

更新单个用户的用户主体名称（UPN）或整个组织时，必须具有定义的进程。 

请参阅本文档中的已知问题和解决方法。

将用户帐户从 Active Directory 同步到 Azure AD 时，请确保 Active Directory 中的 Upn 映射到 Azure AD 中的已验证域。

![已验证域的屏幕截图](./media/howto-troubleshoot-upn-changes/verified-domains.png)

如果 userPrincipalName 属性的值不对应于 Azure AD 中的已验证域，则同步过程会将后缀替换为 onmicrosoft.com 值。


### <a name="roll-out-bulk-upn-changes"></a>推出批量 UPN 更改

按照有关批量 UPN 更改的[试验的最佳实践进行](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)操作。 如果发现无法快速解决的问题，还可以使用已测试的回退计划来恢复 Upn。 试点计算机运行后，可以开始针对包含各种组织角色的小型用户和特定的应用或设备集。

通过此第一个用户子集，你可以很好地了解用户在更改过程中应期待的内容。 将此信息包含在用户通信中。

创建一个已定义的过程，用于在正常操作过程中更改各个用户的 Upn。 建议有一个已测试的过程，其中包含有关已知问题和解决方法的文档。

以下各节详细介绍了在更改 Upn 时可能存在的已知问题和解决方法。

## <a name="apps-known-issues-and-workarounds"></a>应用已知问题和解决方法

[软件即服务（SaaS）](https://azure.microsoft.com/overview/what-is-saas/)和业务线（LoB）应用程序通常依赖于 upn 查找用户并存储用户配置文件信息，包括角色。 当用户首次登录到应用时，使用实时[预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)创建用户配置文件的应用程序可能会受到 UPN 更改的影响。

**已知问题**<br>
更改用户的 UPN 可能会破坏 Azure AD 用户与在应用程序上创建的用户配置文件之间的关系。 如果应用程序使用实时[预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)，则它可能会创建全新的用户配置文件。 这将要求应用程序管理员进行手动更改来修复此关系。

**解决方法**<br>
[Azure AD 自动用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)，你可以在受支持的云应用程序中自动创建、维护和删除用户标识。 在应用程序上配置自动用户预配会自动更新应用程序上的 Upn。 在渐进式推出过程中测试应用程序，以验证它们是否不受 UPN 更改的影响。
如果你是开发人员，请考虑[将 SCIM 支持添加到你的应用程序](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)，以启用 Azure Active Directory 的自动用户预配。 

## <a name="managed-devices-known-issues-and-workarounds"></a>托管设备的已知问题和解决方法

通过[使你的设备 Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview)，你可以通过单一登录（SSO）跨云和本地资源最大化用户的工作效率。

### <a name="azure-ad-joined-devices"></a>Azure AD 加入设备

[Azure AD 联接](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join)的设备直接连接到 Azure AD，并允许用户使用其组织的标识登录到设备。

**已知问题** <br>
用户可能会遇到依赖于身份验证 Azure AD 的应用程序的单一登录问题。

**解决方法** <br>
此部分中提到的问题已在 Windows 10 2020 更新（2004）上得到解决。

**解决方法** <br>
留出足够的时间让 UPN 更改同步到 Azure AD。 验证新 UPN 是否反映在 Azure AD 门户上之后，要求用户选择 "其他用户" 磁贴，以通过其新 UPN 进行登录。 还可以通过[PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)进行验证。 使用新的 UPN 登录后，对旧 UPN 的引用可能仍会显示在 "访问工作或学校" Windows 设置上。

![已验证域的屏幕截图](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>混合 Azure AD 加入设备

[混合 Azure AD 联接](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid)的设备加入到 Active Directory 和 Azure AD。 如果你的环境具有本地 Active Directory 空间，你可以实施混合 Azure AD 联接，并且还想要从 Azure AD 提供的功能中获益。

**已知问题** 

Windows 10 混合 Azure AD 联接的设备可能会遇到意外的重新启动和访问问题。

如果用户在新 UPN 已同步到 Azure AD 之前登录到 Windows，或者继续使用现有的 Windows 会话，则当条件性访问已配置为强制使用混合加入设备访问资源时，用户可能会遇到使用 Azure AD 进行身份验证的应用程序的单一登录问题。 

此外，将显示以下消息，并强制一分钟后重启。 

"你的电脑将在一分钟内自动重新启动。 Windows 遇到问题，需要重新启动。 应该立即关闭此消息，并保存工作。

**解决方法** <br>
此部分中提到的问题已在 Windows 10 2020 更新（2004）上得到解决。

**解决方法** 

设备必须脱离 Azure AD 并重新启动。 重新启动后，设备将再次自动 Azure AD 加入，用户必须通过选择 "其他用户" 磁贴，使用新的 UPN 登录。 若要从 Azure AD 中脱离设备，请在命令提示符下运行以下命令：

**dsregcmd.exe/leave**

如果使用 Windows Hello 企业版，用户将需要[重新注册](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision)。 在 UPN 更改后，Windows 7 和8.1 设备不受此问题的影响。


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator 已知问题和解决方法

你的组织可能需要使用[Microsoft Authenticator 应用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)来登录和访问组织的应用程序和数据。 尽管用户可能会在应用程序中出现用户名，但在用户完成注册过程之前，帐户未设置为作为验证方法。

[Microsoft Authenticator 应用](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)具有四个主要功能：

* 通过推送通知或验证码进行多重身份验证

* 作为 iOS 和 Android 设备上的身份验证代理，以便为使用[中转身份验证](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)的应用程序提供单一登录

* 设备注册（也称为 Workplace Join）用于 Azure AD，这是 Intune 应用保护和设备注册/管理等其他功能的一种要求。

* 需要进行 MFA 和设备注册的手机登录。

### <a name="multi-factor-authentication-with-android-devices"></a>Android 设备的多重身份验证

Microsoft Authenticator 应用提供了带外验证选项。 在登录时，[多重身份验证（MFA）](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)会将通知推送到用户的智能手机或平板电脑上的 Microsoft Authenticator 应用，而不是在登录时将自动电话呼叫或短信。 用户只需在应用中点击 "批准" （或输入 PIN 或生物识别，然后点击 "身份验证"）即可完成登录。

**已知问题** 

更改用户的 UPN 时，旧的 UPN 仍将显示在用户帐户上，并且可能无法接收通知。 [验证代码](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq)将继续工作。

**解决方法**

如果收到通知，请指示用户关闭通知，打开验证器应用，点击 "检查通知" 选项，并批准 MFA 提示。 此后，将更新在该帐户上显示的 UPN。 请注意，更新的 UPN 可能显示为新帐户，这是因为正在使用其他验证器功能。 有关详细信息，请参阅本文中的其他已知问题。

### <a name="brokered-authentication"></a>中转身份验证

在 Android 和 iOS 代理上，如 Microsoft Authenticator 启用：

* 单一登录（SSO）-用户无需登录到每个应用程序。

* 设备标识-当设备加入工作区时，代理会访问在设备上创建的设备证书。

* 应用程序标识验证-当应用程序调用代理时，它会传递其重定向 URL，并且 broker 将对其进行验证。

此外，它还允许应用程序参与更高级的功能，如[条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/)，并支持[Microsoft Intune 方案](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)。

**已知问题**<br>
由于应用程序传递的 login_hint 与代理上存储的 UPN 不匹配，用户在使用 broker 辅助登录的新应用程序上提供了更多的交互式身份验证提示。

**解决方法** <br> 用户需要从 Microsoft Authenticator 中手动删除帐户，然后从代理辅助应用程序开始新的登录。 初始身份验证后，将自动添加该帐户。

### <a name="device-registration"></a>设备注册

Microsoft Authenticator 应用负责向 Azure AD 注册设备。 设备注册允许设备进行 Azure AD 身份验证，并且在以下情况下是必需的：

* Intune 应用保护

* Intune 设备注册

* 电话登录

**已知问题**<br>
更改 UPN 后，具有新 UPN 的新帐户将显示在 Microsoft Authenticator 应用上，并且仍会列出具有旧 UPN 的帐户。 此外，旧 UPN 将显示在 "设备注册" 部分的 "应用设置" 中。 设备注册的正常功能或依赖方案不会有任何变化。

**解决方法** <br> 若要删除对 Microsoft Authenticator 应用上的旧 UPN 的所有引用，请指示用户手动从 Microsoft Authenticator 中删除旧帐户和新帐户，重新注册 MFA 并重新加入设备。

### <a name="phone-sign-in"></a>电话登录

使用手机登录，用户无需密码即可登录 Azure AD。 若要启用手机登录，用户需要注册使用验证器应用的 MFA，然后在验证器上直接启用手机登录。 在配置过程中，设备将注册到 Azure AD。

**已知问题** <br>
用户无法使用手机登录，因为他们没有收到任何通知。 如果用户点击 "检查通知"，则会出现错误。

**解决方法**<br>
用户需要选择启用了电话登录的帐户的下拉菜单，然后选择 "禁用电话登录"。 如果需要，可以重新启用电话登录。

## <a name="security-key-fido2-known-issues-and-workarounds"></a>安全密钥（FIDO2）已知问题和解决方法

**已知问题** <br>
如果在同一密钥上注册多个用户，则 "登录" 屏幕会显示一个帐户选择页，其中显示了旧的 UPN。 使用安全密钥的登录不受 UPN 更改的影响。  

**解决方法**<br>
若要删除对旧 Upn 的引用，用户必须[重置安全密钥并重新注册](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)。

## <a name="onedrive-known-issues-and-workarounds"></a>OneDrive 已知问题和解决方法

OneDrive 用户已知在 UPN 更改后遇到问题。 有关详细信息，请参阅[UPN 更改如何影响 ONEDRIVE URL 和 onedrive 功能](https://docs.microsoft.com/onedrive/upn-changes)。

## <a name="next-steps"></a>后续步骤

请参阅以下资源：
* [Azure AD Connect：设计概念](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Azure AD UserPrincipalName 填充](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Microsoft 标识平台 ID 令牌](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
