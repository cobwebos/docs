---
title: Azure Active Directory 设备管理常见问题解答 | Microsoft Docs
description: Azure Active Directory 设备管理常见问题解答。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a27c9ae1b75b9517bd3af92486df96434c5b34fb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207391"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 设备管理常见问题解答

## <a name="general-faq"></a>General FAQ

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Q: I registered the device recently. 但为什么在 Azure 门户中我的用户信息下看不到该设备？ Or why is the device owner marked as N/A for hybrid Azure Active Directory (Azure AD) joined devices?

**A:** Windows 10 devices that are hybrid Azure AD joined don't show up under **USER devices**.
使用 Azure 门户中的“所有设备”视图。 还可以使用 PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet。

“USER 设备”下面只会列出以下设备：

- 所有未加入混合 Azure AD 的个人设备。 
- 所有非 Windows 10 或 Windows Server 2016 设备。
- 所有非 Windows 设备。 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Q: How do I know what the device registration state of the client is?

**A:** In the Azure portal, go to **All devices**. 使用设备 ID 搜索设备。 检查“联接类型”列下的值。 有时，设备可能已重置或已重置映像。 因此，还必须检查设备上的设备注册状态：

- 对于 Windows 10 和 Windows Server 2016 或更高版本的设备，请运行 `dsregcmd.exe /status`。
- 对于低级别操作系统版本，请运行 `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`。

**A:** For troubleshooting information, see these articles:
- [Troubleshooting devices using dsregcmd command](troubleshoot-device-dsregcmd.md)
- [排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题](troubleshoot-hybrid-join-windows-current.md)
- [排查已加入混合 Azure Active Directory 的下层设备问题](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Q: I see the device record under the USER info in the Azure portal. 我在设备上看到状态为已注册。 Am I set up correctly to use Conditional Access?

**A:** The device join state, shown by **deviceID**, must match the state on Azure AD and meet any evaluation criteria for Conditional Access. For more information, see [Require managed devices for cloud app access with Conditional Access](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>Q: Why do my users see an error message saying "Your organization has deleted the device" or "Your organization has disabled the device" on their Windows 10 devices ?

**A:** On Windows 10 devices joined or registered with Azure AD, users are issued a [Primary refresh token (PRT)](concept-primary-refresh-token.md) which enables single sign on. The validity of the PRT is based on the validaity of the device itself. Users see this message if the device is either deleted or disabled in Azure AD without initiating the action from the device itself. A device can be deleted or disabled in Azure AD one of the following scenarios: 

- User disables the device from the My Apps portal. 
- An administrator (or user) deletes or disables the device in the Azure portal or by using PowerShell
- Hybrid Azure AD joined only: An administrator removes the devices OU out of sync scope resulting in the devices being deleted from Azure AD
- Upgrading Azure AD connect to the version 1.4.xx.x. [Understanding Azure AD Connect 1.4.xx.x and device disappearance](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-device-disappearance).


See below on how these actions can be rectified.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>Q: I disabled or deleted my device in the Azure portal or by using Windows PowerShell. But the local state on the device says it's still registered. 我应该怎么做？

**A:** This operation is by design. In this case, the device doesn't have access to resources in the cloud. Administrators can perform this action for stale, lost or stolen devices to prevent unauthorized access. If this action was performed unintentionally, you'll need to re-enable or re-register the device as described below

- If the device was disabled in Azure AD, an administrator with sufficient privileges can enable it from the Azure AD portal  
  > [!NOTE]
  > If you are syncing devices using Azure AD Connect, hybrid Azure AD joined devices will be automatically re-enabled during the next  sync cycle. So, if you need to disable a hybrid Azure AD joined device, you need to disable it from your on-premises AD

 - If the device is deleted in Azure AD, you need to re-register the device. To re-register, you must take a manual action on the device. See below for instructions for re-registration based on the device state. 

      To re-register hybrid Azure AD joined Windows 10 and Windows Server 2016/2019 devices, take the following steps:

      1. 以管理员身份打开命令提示符。
      1. 输入 `dsregcmd.exe /debug /leave` 。
      1. 注销并再次登录，以触发可以将设备注册到 Azure AD 的计划任务。 

      For down-level Windows OS versions that are hybrid Azure AD joined, take the following steps:

      1. 以管理员身份打开命令提示符。
      1. 输入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` 。
      1. 输入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` 。

      For Azure AD joined devices Windows 10 devices, take the following steps:

      1. 以管理员身份打开命令提示符
      1. Enter `dsregcmd /forcerecovery` (Note: You need to be an administrator to perform this action).
      1. Click "Sign in" in the dialog that opens up and continue with the sign in process.
      1. Sign out and sign in back to the device to complete the recovery.

      For Azure AD registered Windows 10 devices, take the following steps:

      1. Go to **Settings** > **Accounts** > **Access Work or School**. 
      1. Select the account and select **Disconnect**.
      1. Click on "+ Connect" and register the device again by going through the sign in process.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Q: Why do I see duplicate device entries in the Azure portal?

**答:**

- 对于 Windows 10 和 Windows Server 2016，如果反复尝试分离再重新加入同一个设备，则可能会出现重复条目。 
- 使用“添加工作或学校帐户”的每个 Windows 用户将创建具有相同设备名称的新设备记录。
- 对于已加入本地 Azure Directory 域的低级别 Windows 操作系统版本，自动注册将为登录设备的每个域用户创建具有相同设备名称的新设备记录。 
- 在擦除后重新安装并使用相同名称重新加入 Azure AD 计算机会显示为具有相同设备名称的另一条记录。

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Q: Does Windows 10 device registration in Azure AD support TPMs in FIPS mode?

**A:** Windows 10 device registration only supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Azure AD join or Hybrid Azure AD join. Note that Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. 

---

**Q: Why can a user still access resources from a device I disabled in the Azure portal?**

**A:** It takes up to an hour for a revoke to be applied from the time the Azure AD device is marked as disabled.

>[!NOTE] 
>对于已注册的设备，建议擦除该设备，确保用户无法访问这些资源。 有关详细信息，请参阅[什么是设备注册？](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)。 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>Q: Why are there devices marked as "Pending" under the REGISTERED column in the Azure portal?

**A**:  Pending indicates that the device is not registered. This state indicates that a device has been synchronized using Azure AD connect from an on-premises AD and is ready for device registration. These devices have the JOIN TYPE set to "Hybrid Azure AD joined". Learn more on [how to plan your hybrid Azure Active Directory join implementation](hybrid-azuread-join-plan.md).

>[!NOTE]
>A device can also change from having a registered state to "Pending"
>* If a device is deleted from Azure AD first and re-synchronized from an on-premises AD.
>* If a device is removed from a sync scope on Azure AD Connect and added back.
>
>In both cases, you must re-register the device manually on each of these devices. To review whether the device was previously registered, you can [troubleshoot devices using the dsregcmd command](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Azure AD 加入常见问题解答

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin an Azure AD joined device locally on the device?

**A:** For pure Azure AD joined devices, make sure you have an offline local administrator account or create one. 无法使用 Azure AD 用户凭据登录。 接下来，转到“设置” > “帐户” > “访问工作单位或学校”。 选择帐户，然后选择“断开连接”。 按照提示操作，并在出现提示时提供本地管理员凭据。 重新启动设备以完成取消加入过程。

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Q: Can my users' sign in to Azure AD joined devices that are deleted or disabled in Azure AD?

**答：** 是。 Windows 具有缓存用户名和密码功能，该功能允许先前登录的用户即使没有网络连接也能快速访问桌面。 

设备在 Azure AD 中已删除或禁用时，Windows 设备不会知道此情况。 因此，先前登录的用户继续使用缓存的用户名和密码访问桌面。 But as the device is deleted or disabled, users can't access any resources protected by device-based Conditional Access. 

先前没有登录的用户无法访问设备。 他们没有启用的缓存用户名和密码。 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Q: Can a disabled or deleted user sign in to an Azure AD joined devices

**A:** Yes, but only for a limited time. 在 Azure AD 中已删除或禁用用户时，Windows 设备不会立即知道此情况。 因此，先前登录的用户可以使用缓存的用户名和密码访问桌面。 

通常，设备在不到 4 小时的时间内即可了解用户状态。 然后 Windows 会阻止这些用户访问桌面。 由于在 Azure AD 中已删除或禁用用户，因此他们的所有令牌都会撤销。 因此他们无法访问任何资源。 

先前未登录的已删除或禁用用户无法访问设备。 他们没有启用的缓存用户名和密码。 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Azure AD joined devices after changing their UPN?

**A:** Currently, UPN changes are not fully supported on Azure AD joined devices. 因此，在更改其 UPN 后，他们的 Azure AD 身份验证会失败。 这样一来，用户的设备上会存在 SSO 和条件访问问题。 此时，用户需要使用其新 UPN 通过“其他用户”磁贴来登录 Windows，以解决此问题。 我们目前正致力于解决此问题。 但是，使用 Windows Hello 企业版登录的用户不会遇到这个问题。 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Q: My users can't search printers from Azure AD joined devices. How can I enable printing from those devices?

**A:** To deploy printers for Azure AD joined devices, see [Deploy Windows Server Hybrid Cloud Print with Pre-Authentication](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). 需要安装本地 Windows Server 才能部署混合云打印。 当前，无法使用基于云的打印服务。 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Q: How do I connect to a remote Azure AD joined device?

**A:** See [Connect to remote Azure Active Directory-joined PC](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Q: Why do my users see *You can’t get there from here*?

**A:** Did you configure certain Conditional Access rules to require a specific device state? 如果设备不满足条件，就会阻止用户，并且他们会看到该消息。 Evaluate the Conditional Access policy rules. 确保设备满足条件，避免出现该消息。

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Q: Why don't some of my users get Azure Multi-Factor Authentication prompts on Azure AD joined devices?

**A:** A user might join or register a device with Azure AD by using Multi-Factor Authentication. 然后，设备本身成为该用户信任的第二个因素。 每当同一用户登录到设备并访问应用程序，Azure AD 都会将该设备视为第二个因素。 它使用户能够无缝访问应用程序，而无需额外的多重身份验证提示。 

此行为：

- 适用于已加入和注册 Azure AD 的设备，但不适用于加入混合 Azure AD 的设备。
- 不适用于登录到该设备的任何其他用户。 因此，访问该设备的所有其他用户都需要进行多重身份验证。 然后他们可以访问需要多重身份验证的应用程序。

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Q: Why do I get a *username or password is incorrect* message for a device I just joined to Azure AD?

**A:** Common reasons for this scenario are as follows:

- 用户凭据不再有效。
- 计算机无法与 Azure Active Directory 通信。 请检查是否存在任何网络连接问题。
- 联合登录要求联合服务器支持已启用的和可访问的 WS-Trust 终结点。 
- 已启用传递身份验证。 因此，登录时需要更改临时密码。

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Q: Why do I see the *Oops… an error occurred!* dialog when I try to Azure AD join my PC?

**A:** This error happens when you set up Azure Active Directory enrollment with Intune. 确保尝试进行 Azure AD 加入的用户已获得正确的 Intune 许可证。 有关详细信息，请参阅[设置 Windows 设备的注册](https://docs.microsoft.com/intune/windows-enroll)。  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Q: Why did my attempt to Azure AD join a PC fail, although I didn't get any error information?

**A:** A likely cause is that you signed in to the device by using the local built-in administrator account. 请在使用 Azure Active Directory 加入之前创建不同的本地帐户以完成设置。 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What are the MS-Organization-P2P-Access certificates present on our Windows 10 devices?

**A:** The MS-Organization-P2P-Access certificates are issued by Azure AD to both, Azure AD joined and hybrid Azure AD joined devices. 这些证书用于在同一租户中的设备之间为远程桌面场景启用信任。 一个证书颁发给设备，另一个颁发给用户。 设备证书在 `Local Computer\Personal\Certificates` 中提供，且有效期为一天。 如果设备在 Azure AD 中仍然处于活动状态，则续订此证书（通过颁发新证书）。 用户证书在 `Current User\Personal\Certificates` 中提供，且此证书有效期为一天，但是用户尝试与另一个加入 Azure AD 的设备进行远程桌面会话时，会按需颁发。 到期不续期。 这两个证书都是使用 `Local Computer\AAD Token Issuer\Certificates` 中的 MS-Organization-P2P-Access 证书颁发的。 此证书由 Azure AD 在设备注册期间颁发。 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why do I see multiple expired certificates issued by MS-Organization-P2P-Access on our Windows 10 devices? How can I delete them?

**A:** There was an issue identified on Windows 10 version 1709 and lower where expired MS-Organization-P2P-Access certificates continued to exist on the computer store because of cryptographic issues. Your users could face issues with network connectivity, if you are using any VPN clients (for example, Cisco AnyConnect) that cannot handle the large number of expired certificates. 此问题在 Windows 10 1803 版本中已修复，自动删除任何此类过期的 MS-Organization-P2P-Access 证书。 可以通过将设备更新到 Windows 10 1803 来解决此问题。 如果无法更新，可以删除这些证书，而不产生任何不利影响。  

---

## <a name="hybrid-azure-ad-join-faq"></a>混合 Azure AD 加入常见问题解答

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin a Hybrid Azure AD joined device locally on the device?

**A:** For hybrid Azure AD joined devices, make sure to turn off automatic registration. 然后计划任务不会再次注册设备。 接下来，以管理员身份打开命令提示符并输入 `dsregcmd.exe /debug /leave`。 或者将此命令作为脚本在多个设备上运行，以批量取消加入。

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Q: Where can I find troubleshooting information to diagnose hybrid Azure AD join failures?

**A:** For troubleshooting information, see these articles:

- [排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题](troubleshoot-hybrid-join-windows-current.md)
- [排查已加入混合 Azure Active Directory 的下层设备问题](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Q: Why do I see a duplicate Azure AD registered record for my Windows 10 hybrid Azure AD joined device in the Azure AD devices list?

**A:** When your users add their accounts to apps on a domain-joined device, they might be prompted with **Add account to Windows?** 如果他们在提示时输入“确定”，设备会注册 Azure AD。 信任类型标记为已注册 Azure AD。 在组织中启用混合 Azure AD 加入后，设备还将加入混合 Azure AD。 然后显示同一设备的两种设备状态。 

混合 Azure AD 加入优先于 Azure AD 已注册状态。 So your device is considered hybrid Azure AD joined for any authentication and Conditional Access evaluation. 可以安全地从 Azure AD 门户中删除已注册 Azure AD 设备记录。 了解[避免或清理 Windows 10 计算机上的此双状态](hybrid-azuread-join-plan.md#review-things-you-should-know)。 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Windows 10 hybrid Azure AD joined devices after changing their UPN?

**A:** Currently UPN changes are not fully supported with hybrid Azure AD joined devices. 虽然用户可以登录到设备并访问其本地应用程序，但在 UPN 更改后，他们的 Azure AD 身份验证仍会失败。 这样一来，用户的设备上会存在 SSO 和条件访问问题。 At this time, you need to unjoin the device from Azure AD (run "dsregcmd /leave" with elevated privileges) and rejoin (happens automatically) to resolve the issue. 我们目前正致力于解决此问题。 但是，使用 Windows Hello 企业版登录的用户不会遇到这个问题。 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Q: Do Windows 10 hybrid Azure AD joined devices require line of sight to the domain controller to get access to cloud resources?

**A:** No, except when the user's password is changed. After Windows 10 hybrid Azure AD join is complete, and the user has signed in at least once, the device doesn't require line of sight to the domain controller to access cloud resources. Windows 10 can get single sign-on to Azure AD applications from anywhere with an internet connection, except when a password is changed. Users who sign in with Windows Hello for Business continue to get single sign-on to Azure AD applications even after a password change, even if they don't have line of sight to their domain controller. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Q: What happens if a user changes their password and tries to login to their Windows 10 hybrid Azure AD joined device outside the corporate network?

**A:** If a password is changed outside the corporate network (for example, by using Azure AD SSPR), then the user sign in with the new password will fail. For hybrid Azure AD joined devices, on-premises Active Directory is the primary authority. When a device does not have line of sight to the domain controller, it is unable to validate the new password. So, user needs to establish connection with the domain controller (either via VPN or being in the corporate network) before they're able to sign in to the device with their new password. Otherwise, they can only sign in with their old password because of cached sign in capability in Windows. However, the old password is invalidated by Azure AD during token requests and hence, prevents single sign-on and fails any device-based Conditional Access policies. This issue doesn't occur if you use Windows Hello for Business. 

---

## <a name="azure-ad-register-faq"></a>Azure AD 注册常见问题解答

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>Q: How do I remove an Azure AD registered state for a device locally?

**答:** 
- For Windows 10 Azure AD registered devices, Go to **Settings** > **Accounts** > **Access Work or School**. 选择帐户，然后选择“断开连接”。 Device registration is per user profile on Windows 10.
- For iOS and Android, you can use the Microsoft Authenticator application **Settings** > **Device Registration** and select **Unregister device**.
- For macOS, you can use the Microsoft Intune Company Portal application to un-enroll the device from management and remove any registration. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>Q: How can I block users from adding additional work accounts (Azure AD registered) on my corporate Windows 10 devices?

**A:** Enable the following registry to block your users from adding additional work accounts to your corporate domain joined, Azure AD joined or hybrid Azure AD joined Windows 10 devices. This policy can also be used to block domain joined machines from inadvertently getting Azure AD registered with the same user account. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Q: Can I register Android or iOS BYOD devices?

**A:** Yes, but only with the Azure device registration service and for hybrid customers. Active Directory 联合身份验证服务 (AD FS) 中的本地设备注册服务不支持此功能。

---
### <a name="q-how-can-i-register-a-macos-device"></a>Q: How can I register a macOS device?

**A:** Take the following steps:

1.  [创建符合性策略](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Define a Conditional Access policy for macOS devices](../active-directory-conditional-access-azure-portal.md) 

**备注：**

- The users included in your Conditional Access policy need a [supported version of Office for macOS](../conditional-access/technical-reference.md#client-apps-condition) to access resources. 
- 在首次尝试访问期间，用户使用公司门户时会看到注册设备的提示。

---
## <a name="next-steps"></a>后续步骤

- 详细了解[已注册到 Azure AD 的设备](concept-azure-ad-register.md)
- 详细了解[已加入 Azure AD 的设备](concept-azure-ad-join.md)
- 详细了解[已加入混合 Azure AD 的设备](concept-azure-ad-join-hybrid.md)
