---
title: "Azure Active Directory 设备管理常见问题解答 | Microsoft Docs"
description: "Azure Active Directory 设备管理常见问题解答。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3b37033e0c96e01c73bef15af1b85116f404c354
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 设备管理常见问题解答



**问：如何注册 macOS 设备？**

**答：**注册 macOS 设备的具体步骤：

1.  [创建符合性策略](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [定义适用于 macOS 设备的条件访问策略](active-directory-conditional-access-azure-portal.md) 

**备注：**

- 条件访问策略中的用户必须有 [macOS 支持的 Office 版本](active-directory-conditional-access-technical-reference.md#client-apps-condition)，才能访问资源。 

- 首次尝试访问期间，用户会看到有关使用公司门户注册设备的提示。

---

**问：我最近注册了设备，但为什么在 Azure 门户中我的用户信息下看不到该设备？**

**答：**使用自动设备注册加入域的 Windows 10 设备不会显示在用户信息下面。
需要使用 PowerShell 查看所有设备。 

用户信息下面只会列出以下设备：

- 所有未加入企业的个人设备 
- 所有非 Windows 10/Windows Server 2016 设备 
- 所有非 Windows 设备 

---

**问：在 Azure 门户中为何看不到所有已在 Azure Active Directory 中注册的设备？** 

**答：**目前，无法在 Azure 门户中所有已注册的设备。 可以使用 Azure PowerShell 查找所有设备。 有关详细信息，请参阅 [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet。

--- 

**问：如何了解客户端的设备注册状态？**

**答：**设备注册状态取决于：

- 设备是什么
- 设备的注册方式 
- 与设备相关的任何详细信息。 
 

---

**问：在 Azure 门户中或使用 Windows PowerShell 删除的设备为何仍列为已注册？**

**答：**这是设计使然。 该设备无权访问云中的资源。 如果想要删除设备并重新注册，必须在设备上执行一个手动操作。 

对于已加入本地 AD 域的 Windows 10 和 Windows Server 2016 设备：

1.  以管理员身份打开命令提示符。

2.  键入 `dsregcmd.exe /debug /leave`

3.  注销并再次登录，以触发注册设备的计划任务。 

对于已加入本地 AD 域的其他 Windows 平台：

1.  以管理员身份打开命令提示符。
2.  键入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`。
3.  键入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`。

---

**问：Azure 门户中为何出现重复的设备条目？**

**答：**

-   对于 Windows 10 和 Windows Server 2016，如果反复尝试分离再重新加入同一个设备，则可能会出现重复条目。 

-   如果使用了“添加工作或学校帐户”，则使用“添加工作或学校帐户”的每个 Windows 用户将创建具有相同设备名称的新设备记录。

-   使用自动注册加入本地 AD 域的其他 Windows 平台将为登录设备的每个域用户创建具有相同设备名称的新设备记录。 

-   已擦除、重新安装并使用相同名称重新加入域的 AADJ 计算机会显示为具有相同设备名称的另一条记录。

---

**问：用户为什么仍然可以通过我在 Azure 门户中禁用的设备访问资源？**

**答：**最长可能需要花费一小时才能应用吊销。

>[!Note] 
>对于遗失的设备，我们建议擦除该设备，确保用户无法访问该设备。 有关详细信息，请参阅 [Enroll devices for management in Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)（注册设备以便在 Intune 中进行管理）。 


---

**问：用户为什么会看到“无法从这个位置访问那个位置”？**

**答：**如果已配置特定的条件访问规则来要求设备保持特定的状态，但设备并不满足该条件，则用户会被阻止并会看到此消息。 请评估规则，确保设备能够满足条件，避免出现此消息。

---


**问：我在 Azure 门户中的用户信息下看到了设备记录，设备状态为已在客户端注册。我的设置是否正确，可以使用条件访问？**

**答：**Azure 门户中的设备记录 (deviceID) 和状态必须与客户端匹配，并满足条件访问的所有评估条件。 有关详细信息，请参阅 [Azure Active Directory 设备注册入门](active-directory-device-registration.md)。

---

**问：刚刚加入 Azure AD 的设备中为何会显示“用户名或密码不正确”消息？**

**答：**出现这种情况的常见原因包括：

- 用户凭据不再有效。

- 计算机无法与 Azure Active Directory 通信。 请检查是否存在任何网络连接问题。

- 不满足 Azure AD Join 的先决条件。 请确保遵循[通过 Azure Active Directory Join 将云功能扩展到 Windows 10 设备](active-directory-azureadjoin-overview.md)中的步骤。  

- 联合登录要求联合服务器支持 WS-Trust 活动终结点。 

---

**问：尝试加入我的电脑时，为何会出现“哎呀...发生了错误!”？**

**答：**这是使用 Intune 设置 Azure Active Directory 注册的结果。 有关详细信息，请参阅 [Set up Windows device management](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment)（设置 Windows 设备管理）。  

---

**问：我没有收到任何错误信息，但尝试加入电脑为何失败？**

**答：**一个可能的原因是用户使用内置管理员帐户登录到设备。 请在使用 Azure Active Directory Join 之前创建一个不同的本地帐户以完成设置。 

---

**问：在哪里可以找到自动注册设备的安装说明？**

**答：**有关详细说明，请参阅[如何配置已加入域的 Windows 设备的 Azure Active Directory 自动注册](active-directory-conditional-access-automatic-device-registration-setup.md)

---

**问：在哪里可以找到有关自动设备注册的故障排除信息？**

**答：**有关故障排除信息，请参阅：

- [排查已加入 Azure AD 域的计算机的自动注册问题 - Windows 10 和 Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- 请参阅[排查已加入 Azure AD 域的计算机的自动注册问题 - Windows 下层客户端](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 
---

