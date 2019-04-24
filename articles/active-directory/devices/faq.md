---
title: Azure Active Directory 设备管理常见问题解答 | Microsoft Docs
description: Azure Active Directory 设备管理常见问题解答。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: af10596fb1ddf4a4f9eba2b8265eb77221a19f4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60353119"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 设备管理常见问题解答

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>问：我最近注册了设备。 但为什么在 Azure 门户中我的用户信息下看不到该设备？ 或者为什么设备所有者标记为已加入混合 Azure Active Directory (Azure AD) 的不适用的设备？

**答:** 已加入混合 Azure AD 的 Windows 10 设备不显示在“USER 设备”下。
使用 Azure 门户中的“所有设备”视图。 还可以使用 PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet。

“USER 设备”下面只会列出以下设备：

- 所有未加入混合 Azure AD 的个人设备。 
- 所有非 Windows 10 或 Windows Server 2016 设备。
- 所有非 Windows 设备。 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>问：如何知道客户端的设备注册状态是什么？

**答:** 在 Azure 门户中，转到“所有设备”。 使用设备 ID 搜索设备。 检查“联接类型”列下的值。 有时，设备可能已重置或已重置映像。 因此，还必须检查设备上的设备注册状态：

- 对于 Windows 10 和 Windows Server 2016 或更高版本的设备，请运行 `dsregcmd.exe /status`。
- 对于低级别操作系统版本，请运行 `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`。

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>问：我在 Azure 门户中看到了 USER 信息下面的设备记录。 我在设备上看到状态为已注册。 正在设置正确使用条件性访问？

**答:** deviceID 所显示的设备加入状态必须与 Azure AD 上的状态相符，并且必须符合条件性访问的任何评估条件。 有关详细信息，请参阅[通过条件访问要求使用受管理设备进行云应用访问](../conditional-access/require-managed-devices.md)。

---

### <a name="q-i-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered"></a>问：我通过 Azure 门户或使用 Windows PowerShell 删除了设备。 但在设备上的本地状态指示仍注册。

**答:** 此操作是有意为之。 设备无权访问云中的资源。 

若要重新注册，必须在设备上执行手动操作。 

若要从已加入本地 Active Directory 域的 Windows 10 和 Windows Server 2016 中清除联接状态，请执行以下步骤：

1.  以管理员身份打开命令提示符。

2.  输入 `dsregcmd.exe /debug /leave` 。

3.  注销并再次登录，以触发可以将设备注册到 Azure AD 的计划任务。 

对于加入本地 Active Directory 域的低级别 Windows 操作系统版本，请执行以下步骤：

1.  以管理员身份打开命令提示符。
2.  输入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` 。
3.  输入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` 。

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>问：原因请参阅在 Azure 门户中的重复的设备条目？

**答:**

-   对于 Windows 10 和 Windows Server 2016，如果反复尝试分离再重新加入同一个设备，则可能会出现重复条目。 

-   使用“添加工作或学校帐户”的每个 Windows 用户将创建具有相同设备名称的新设备记录。

-   对于已加入本地 Azure Directory 域的低级别 Windows 操作系统版本，自动注册将为登录设备的每个域用户创建具有相同设备名称的新设备记录。 

-   在擦除后重新安装并使用相同名称重新加入 Azure AD 计算机会显示为具有相同设备名称的另一条记录。

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>问：Azure AD 中的 Windows 10 设备注册不支持在 FIPS 模式下的 Tpm？

**答:** 不可以，目前 Windows 10 上的所有设备状态的混合 Azure AD 联接、 Azure AD 加入和已注册 Azure AD 的设备注册不支持 Tpm 在 FIPS 模式下。 若要成功加入或注册到 Azure AD，FIPS 模式下需要为这些设备上 Tpm 已关闭

---

**问：用户为什么仍然可以通过我在 Azure 门户中禁用的设备访问资源？**

**答:** 最长可能需要花费一小时才能应用撤销。

>[!NOTE] 
>对于已注册的设备，建议擦除该设备，确保用户无法访问这些资源。 有关详细信息，请参阅[什么是设备注册？](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)。 

---

## <a name="azure-ad-join-faq"></a>Azure AD 加入常见问题解答

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>问：我如何断开 Azure AD 加入的设备本地设备上？

**答:** 
- 对于加入混合 Azure AD 的设备，请确保关闭自动注册。 然后计划任务不会再次注册设备。 接下来，以管理员身份打开命令提示符并输入 `dsregcmd.exe /debug /leave`。 或者将此命令作为脚本在多个设备上运行，以批量取消加入。

- 对于已加入纯 Azure AD 的设备，请确保拥有脱机本地管理员帐户或创建一个该账户。 无法使用 Azure AD 用户凭据登录。 接下来，转到“设置” > “帐户” > “访问工作单位或学校”。 选择帐户，然后选择“断开连接”。 按照提示操作，并在出现提示时提供本地管理员凭据。 重新启动设备以完成取消加入过程。

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>问：可以我的用户登录到要删除或禁用在 Azure AD 中的 Azure AD 加入设备？

**答:** 可以。 Windows 具有缓存用户名和密码功能，该功能允许先前登录的用户即使没有网络连接也能快速访问桌面。 

设备在 Azure AD 中已删除或禁用时，Windows 设备不会知道此情况。 因此，先前登录的用户继续使用缓存的用户名和密码访问桌面。 但是，由于设备已删除或禁用，用户无法访问由基于设备的条件访问保护的任何资源。 

先前没有登录的用户无法访问设备。 他们没有启用的缓存用户名和密码。 

---

### <a name="q-can-disabled-or-deleted-users-sign-in-to-azure-ad-joined-devices"></a>问：可以禁用或已删除用户登录到 Azure AD 加入设备？

**答:** 可以，但时间有限。 在 Azure AD 中已删除或禁用用户时，Windows 设备不会立即知道此情况。 因此，先前登录的用户可以使用缓存的用户名和密码访问桌面。 

通常，设备在不到 4 小时的时间内即可了解用户状态。 然后 Windows 会阻止这些用户访问桌面。 由于在 Azure AD 中已删除或禁用用户，因此他们的所有令牌都会撤销。 因此他们无法访问任何资源。 

先前未登录的已删除或禁用用户无法访问设备。 他们没有启用的缓存用户名和密码。 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>问：为什么我的用户有问题已加入 Azure AD 的设备上更改其 UPN 后？

**答:** 当前，已联接 Azure AD 的设备不完全支持 UPN 更改。 因此，在更改其 UPN 后，他们的 Azure AD 身份验证会失败。 这样一来，用户的设备上会存在 SSO 和条件访问问题。 此时，用户需要使用其新 UPN 通过“其他用户”磁贴来登录 Windows，以解决此问题。 我们目前正致力于解决此问题。 但是，使用 Windows Hello 企业版登录的用户不会遇到这个问题。 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>问：我的用户无法从加入 Azure AD 的设备中搜索打印机。 如何启用打印从这些设备？

**答:** 若要为加入 Azure AD 的设备部署打印机，请参阅[Deploy Windows Server Hybrid Cloud Print with Pre-Authentication](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)（使用预身份验证部署 Windows Server 混合云打印）。 需要安装本地 Windows Server 才能部署混合云打印。 当前，无法使用基于云的打印服务。 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>问：如何连接到远程 Azure AD 加入的设备？

**答:** 请参阅[连接到已加入远程 Azure Active Directory 的电脑](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)。

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>问：为什么我的用户看*你无法从此处获取那里*？

**答:** 是否配置了某些条件访问规则以要求特定的设备状态? 如果设备不满足条件，就会阻止用户，并且他们会看到该消息。 评估条件性访问策略规则。 确保设备满足条件，避免出现该消息。

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>问：为什么我的用户的一些不在 Azure AD 加入设备中获取 Azure 多重身份验证提示

**答:** 用户可能是通过使用多重身份验证加入或注册 Azure AD 设备。 然后，设备本身成为该用户信任的第二个因素。 每当同一用户登录到设备并访问应用程序，Azure AD 都会将该设备视为第二个因素。 它使用户能够无缝访问应用程序，而无需额外的多重身份验证提示。 

此行为：

- 适用于已加入和注册 Azure AD 的设备，但不适用于加入混合 Azure AD 的设备。

- 不适用于登录到该设备的任何其他用户。 因此，访问该设备的所有其他用户都需要进行多重身份验证。 然后他们可以访问需要多重身份验证的应用程序。

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>问：为什么收到*用户名或密码不正确*我只需加入到 Azure AD 的设备的消息？

**答:** 出现这种情况的常见原因如下：

- 用户凭据不再有效。

- 计算机无法与 Azure Active Directory 通信。 请检查是否存在任何网络连接问题。

- 联合登录要求联合服务器支持已启用的和可访问的 WS-Trust 终结点。 

- 已启用传递身份验证。 因此，登录时需要更改临时密码。

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>问：为什么看*糟糕...出现错误 ！* 对话框到 Azure AD 尝试时加入我的电脑？

**答:** 使用 Intune 设置 Azure Active Directory 注册时会发生此错误。 确保尝试进行 Azure AD 加入的用户已获得正确的 Intune 许可证。 有关详细信息，请参阅[设置 Windows 设备的注册](https://docs.microsoft.com/intune/windows-enroll)。  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>问：为什么却我尝试向 Azure AD 加入的 PC 故障，虽然我没有收到任何错误的信息？

**答:** 可能是由于使用本地内置管理员帐户登录到设备。 请在使用 Azure Active Directory 加入之前创建不同的本地帐户以完成设置。 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>问： 什么是 MS 组织 P2P 访问证书提供了有关我们的 Windows 10 设备？

**答:** MS-Organization-P2P-Access 证书由 Azure AD 颁发给加入 Azure AD 的设备和加入混合 Azure AD 的设备。 这些证书用于在同一租户中的设备之间为远程桌面场景启用信任。 一个证书颁发给设备，另一个颁发给用户。 设备证书在 `Local Computer\Personal\Certificates` 中提供，且有效期为一天。 如果设备在 Azure AD 中仍然处于活动状态，则续订此证书（通过颁发新证书）。 用户证书在 `Current User\Personal\Certificates` 中提供，且此证书有效期为一天，但是用户尝试与另一个加入 Azure AD 的设备进行远程桌面会话时，会按需颁发。 到期不续期。 这两个证书都是使用 `Local Computer\AAD Token Issuer\Certificates` 中的 MS-Organization-P2P-Access 证书颁发的。 此证书由 Azure AD 在设备注册期间颁发。 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why 会看到多个过期的证书颁发的 MS 组织 P2P 访问我们的 Windows 10 设备上？ 如何删除它们？

**答:** 在 Windows 10 版本 1709 及更低版本上，由于密码问题，过期的 MS-Organization-P2P-Access 证书会继续存在于计算机存储中。 如果使用的是无法处理大量过期证书的 VPN 客户端（如 Cisco AnyConnect），则用户可能会面临网络连接问题。 此问题在 Windows 10 1803 版本中已修复，自动删除任何此类过期的 MS-Organization-P2P-Access 证书。 可以通过将设备更新到 Windows 10 1803 来解决此问题。 如果无法更新，可以删除这些证书，而不产生任何不利影响。  

---


## <a name="hybrid-azure-ad-join-faq"></a>混合 Azure AD 加入常见问题解答

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>问：在哪里找到故障排除的信息来诊断混合 Azure AD 联接失败？

**答:** 有关排除故障的信息，请参阅以下文章：

- [排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题](troubleshoot-hybrid-join-windows-current.md)

- [排查已加入混合 Azure Active Directory 的下层设备问题](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>问：为什么看到重复的 Azure AD 注册的记录我的 Windows 10 混合 Azure AD 的已加入 Azure AD 的设备列表中的设备？

**答:** 用户在已加入域的设备上将其帐户添加到应用时，可能会提示他们“将帐户添加到 Windows？” 如果他们在提示时输入“确定”，设备会注册 Azure AD。 信任类型标记为已注册 Azure AD。 在组织中启用混合 Azure AD 加入后，设备还将加入混合 Azure AD。 然后显示同一设备的两种设备状态。 

混合 Azure AD 加入优先于 Azure AD 已注册状态。 因此，对于任何身份验证和条件访问评估，视作设备已加入混合 Azure AD。 可以安全地从 Azure AD 门户中删除已注册 Azure AD 设备记录。 了解[避免或清理 Windows 10 计算机上的此双状态](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know)。 


---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>问：为什么我的用户有问题在 Windows 10 混合 Azure AD 加入设备上更改其 UPN 后？

**答:** 当前，已联接混合 Azure AD 的设备不完全支持 UPN 更改。 虽然用户可以登录到设备并访问其本地应用程序，但在 UPN 更改后，他们的 Azure AD 身份验证仍会失败。 这样一来，用户的设备上会存在 SSO 和条件访问问题。 此时，需要从 Azure AD 中分离设备（使用提升的权限运行“dsregcmd /leave”），然后重新联接（自动执行）来解决问题。 我们目前正致力于解决此问题。 但是，使用 Windows Hello 企业版登录的用户不会遇到这个问题。 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>问：Windows 10 混合 Azure AD 加入设备是否需要连接到域控制器以获取对云资源的访问权限？

**答:** 通常不会，更改用户的密码时除外。 在 Windows 10 混合 Azure AD 联接已完成，并且用户至少登录了一次之后，设备无需连接到域控制器即可访问云资源。 Windows 10 可以通过 Internet 连接从任何位置单一登录到 Azure AD 应用程序（除非更改了密码时）。 使用 Windows hello 企业版登录继续获取单个实体的用户登录到 Azure AD 应用程序甚至更改密码后，即使它们没有连接到其域控制器。 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>问：如果用户更改其密码，并尝试登录到其 Windows 10 混合 Azure AD 会发生什么情况已加入公司网络外部的设备？

**答:**（例如，通过使用 Azure AD SSPR） 更改公司网络外部的密码时，如果用户使用新密码登录将失败。 对于混合 Azure AD 加入设备的本地 Active Directory 是主要授权机构。 如果设备不具有与域控制器建立直通，不能验证新密码。 因此，用户需要与域控制器 （无论是通过 VPN 或公司网络中） 建立连接，它们可以在登录到设备的新密码之前。 否则，他们可以仅注册使用其旧密码由于在 Windows 中的缓存的登录功能。 然而，旧密码由 Azure AD 在令牌请求期间失效，因此，防止上单一登录和失败的任何基于设备的条件性访问策略。 如果您使用 Windows hello 企业版，则不会出现此问题。 

---


## <a name="azure-ad-register-faq"></a>Azure AD 注册常见问题解答

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>问：是否可以注册 Android 或 iOS 的 BYOD 设备？

**答:** 可以，但只有使用 Azure 设备注册服务的混合客户才能注册。 Active Directory 联合身份验证服务 (AD FS) 中的本地设备注册服务不支持此功能。

### <a name="q-how-can-i-register-a-macos-device"></a>问：如何注册 macOS 设备？

**答:** 执行以下步骤：

1.  [创建符合性策略](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [定义适用于 macOS 设备的条件访问策略](../active-directory-conditional-access-azure-portal.md) 

**备注：**

- 条件访问策略中包含的用户必须有 [macOS 支持的 Office 版本](../conditional-access/technical-reference.md#client-apps-condition)，才能访问资源。 

- 在首次尝试访问期间，用户使用公司门户时会看到注册设备的提示。

