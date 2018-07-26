---
title: Azure Active Directory 设备管理常见问题解答 | Microsoft Docs
description: Azure Active Directory 设备管理常见问题解答。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 10d6b81915432d9f41c0d4751f98cbf380ff2d13
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136564"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 设备管理常见问题解答

**问：我可以注册 Android 或 iOS BYOD 设备吗？**

**答：** 可以，但只有使用 Azure 设备注册服务的混合客户才能注册。 不支持使用 AD FS 中的本地设备注册服务进行注册。

**问：如何注册 macOS 设备？**

**答：** 注册 macOS 设备的具体步骤：

1.  [创建符合性策略](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [定义适用于 macOS 设备的条件访问策略](active-directory-conditional-access-azure-portal.md) 

**备注：**

- 条件访问策略中的用户必须有 [macOS 支持的 Office 版本](active-directory-conditional-access-technical-reference.md#client-apps-condition)，才能访问资源。 

- 首次尝试访问期间，用户会看到有关使用公司门户注册设备的提示。

---

**问：我最近注册了设备，但为什么在 Azure 门户中我的用户信息下看不到该设备？**

**答：** 已加入混合 Azure AD 的 Windows 10 设备不显示在 USER 设备下。
需要使用 Azure 门户中的“所有设备”视图。 还可以使用 PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet。

USER 设备下面只会列出以下设备：

- 所有未加入混合 Azure AD 的个人设备。 
- 所有非 Windows 10/Windows Server 2016 设备。
- 所有非 Windows 设备 

--- 

**问：如何了解客户端的设备注册状态？**

**答：** 可以使用 Azure 门户，转到“所有设备”并使用设备 ID 搜索设备。 检查“联接类型”列下的值。

如果要从注册的设备检查本地设备注册状态：

- 对于 Windows 10 和 Windows Server 2016 或更高版本的设备，请运行 dsregcmd.exe /status。
- 对于下层 OS 版本，请运行“%programFiles%\Microsoft Workplace Join\autoworkplace.exe”

---

**问：我已通过 Azure 门户或使用 Windows PowerShell 删除，但设备上的本地状态仍然显示它已注册？**

**答：** 这是设计使然。 该设备无权访问云中的资源。 

若要重新注册，必须在设备上执行一个手动操作。 

若要从已加入本地 AD 域的 Windows 10 和 Windows Server 2016 中清除联接状态，请执行以下操作：

1.  以管理员身份打开命令提示符。

2.  键入 `dsregcmd.exe /debug /leave`

3.  注销并再次登录，以触发可以将设备注册到 Azure AD 的计划任务。 

对于已加入本地 AD 域的下层 Windows OS 版本，请执行以下操作：

1.  以管理员身份打开命令提示符。
2.  键入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`。
3.  键入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`。

---
**问：如何在设备上本地取消与 Azure AD 联接设备的联接？**

**答：** 
- 对于已加入混合 Azure AD 的设备，请确保关闭自动注册，以便计划任务不会再次注册该设备。 接下来，以管理员身份打开命令提示符并键入 `dsregcmd.exe /debug /leave`。 或者，可以将该命令作为脚本跨多个设备运行，以批量取消加入。

- 对于已加入纯 Azure AD 的设备，请确保你有脱机本地管理员帐户或创建一个，因为你将无法使用任何 Azure AD 用户凭据登录。 接下来，转到“设置” > “帐户” > “访问工作单位或学校”。 选择帐户，然后单击“断开连接”。 按照提示操作，并在出现提示时提供本地管理员凭据。 重新启动设备以完成取消加入过程。

---

**问：我的用户无法从 Azure AD 联接设备中搜索打印机。如何从 Azure AD 联接设备启用打印？**

**答：** 有关为 Azure AD 联接设备部署打印机的信息，请参阅[混合云打印](https://docs.microsoft.com/en-us/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)。 需要安装本地 Windows Server 才能部署混合云打印。 当前，无法使用基于云的打印服务。 

---

**问：如何连接到已加入 Azure AD 的远程设备？**
**答：** 有关详细信息，请参阅文章 https://docs.microsoft.com/en-us/windows/client-management/connect-to-remote-aadj-pc。

---

**问：Azure 门户中为何出现重复的设备条目？**

**答：**

-   对于 Windows 10 和 Windows Server 2016，如果反复尝试分离再重新加入同一个设备，则可能会出现重复条目。 

-   如果使用了“添加工作或学校帐户”，则使用“添加工作或学校帐户”的每个 Windows 用户将创建具有相同设备名称的新设备记录。

-   对于已加入本地 AD 域的下层 Windows OS 版本，使用自动注册将为登录设备的每个域用户创建具有相同设备名称的新设备记录。 

-   在擦除后重新安装并使用相同名称重新加入 Azure AD 计算机会显示为具有相同设备名称的另一条记录。

---

**问：用户为什么仍然可以通过我在 Azure 门户中禁用的设备访问资源？**

**答：** 最长可能需要花费一小时才能应用吊销。

>[!Note] 
>对于注册的设备，建议擦除该设备，确保用户无法访问这些资源。 有关详细信息，请参阅 [Enroll devices for management in Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)（注册设备以便在 Intune 中进行管理）。 


---

**问：用户为什么会看到“无法从这个位置访问那个位置”？**

**答：** 如果已配置特定的条件访问规则来要求设备保持特定的状态，但设备并不满足该条件，则用户会被阻止并会看到此消息。 请评估条件性访问策略规则，确保设备能够满足条件，避免出现此消息。

---

**问：为什么我的一些用户无法在 Azure AD 联接设备上获得 MFA 提示？**

**答：** 如果用户通过使用多重身份验证的 Azure AD 联接或注册设备，该设备本身将成为该特定用户的受信任第二个因数。 随后，每次同一用户登录到该设备并访问应用程序时，Azure AD 都会将该设备视为第二个因素，使该用户可无缝地访问他们的应用程序，而无需其他 MFA 提示。 此行为对登录到该设备的任何其他用户都不适用，因此访问该设备的所有其他用户仍将在访问要求 MFA 的应用程序之前收到相关的 MFA 提示。

---

**问：我在 Azure 门户中的“用户信息”下看到了设备记录，设备状态为已在设备上注册。我的设置是否正确，可以使用条件访问？**

**答：** deviceID 所反映的设备加入状态必须与 Azure AD 上的状态相符，并且必须符合条件性访问的任何评估条件。 有关详细信息，请参阅 [Azure Active Directory 设备注册入门](active-directory-device-registration.md)。

---

**问：刚刚加入 Azure AD 的设备中为何会显示“用户名或密码不正确”消息？**

**答：** 出现这种情况的常见原因包括：

- 用户凭据不再有效。

- 计算机无法与 Azure Active Directory 通信。 请检查是否存在任何网络连接问题。

- 不满足 Azure AD Join 的先决条件。 请确保遵循[通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-overview.md)中的步骤。  

- 联合登录要求联合服务器支持 WS-Trust 活动终结点。 

- 你已启用“直通身份验证”，并且用户具有需要在登录时更改的临时密码。

---

**问：尝试在电脑上加入 Azure AD 时，为何会出现“哎呀...发生了错误!”对话框？**

**答：** 这是使用 Intune 设置 Azure Active Directory 注册的结果。 请确保尝试进行 Azure AD 加入的用户已分配了正确的 Intune 许可证。 有关详细信息，请参阅 [Set up Windows device management](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment)（设置 Windows 设备管理）。  

---

**问：我没有收到任何错误信息，但尝试加入电脑为何失败？**

**答：** 一个可能的原因是用户使用本地内置管理员帐户登录到设备。 请在使用 Azure Active Directory Join 之前创建一个不同的本地帐户以完成设置。 

---

**问：在哪里可以找到自动注册设备的安装说明？**

**答：** 有关详细说明，请参阅[如何配置已加入域的 Windows 设备的 Azure Active Directory 自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)

---

**问：在哪里可以找到有关自动设备注册的故障排除信息？**

**答：** 有关故障排除信息，请参阅：

- [排查已加入 Azure AD 域的计算机的自动注册问题 - Windows 10 和 Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- 请参阅[排查已加入 Azure AD 域的计算机的自动注册问题 - Windows 下层客户端](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 

---

