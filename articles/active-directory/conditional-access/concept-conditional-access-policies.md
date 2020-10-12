---
title: 生成条件访问策略 - Azure Active Directory
description: 生成条件访问策略可使用的所有选项有哪些？它们的含义是什么？
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ab632010ec2bbbc19ac71cbeccab2ff6b3565f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88948379"
---
# <a name="building-a-conditional-access-policy"></a>生成条件访问策略

如[什么是条件访问](overview.md)一文中所述，条件访问策略是一个关于**分配**和**访问控制**的 if-then 语句。 条件访问策略可统合信号，做出决策，并实施组织策略。

组织如何创建这些策略？ 需要执行哪些操作？

![条件访问（信号 + 决策 + 实施 = 策略）](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>分配

分配部分用于控制条件访问策略的对象、内容和位置。

### <a name="users-and-groups"></a>用户和组

[用户和组](concept-conditional-access-users-groups.md)指定该策略将包含或排除的对象。 该分配可以包括所有用户、特定的用户组、目录角色或外部来宾用户。 

### <a name="cloud-apps-or-actions"></a>云应用或操作

[云应用或操作](concept-conditional-access-cloud-apps.md)可以包括或排除将受该策略约束的云应用程序或用户操作。

### <a name="conditions"></a>条件

一个策略可以包含多个[条件](concept-conditional-access-conditions.md)。

#### <a name="sign-in-risk"></a>登录风险

对于具有 [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)的组织而言，生成的风险检测可能会影响你的条件访问策略。

#### <a name="device-platforms"></a>设备平台

具有多个设备操作系统平台的组织可能希望在不同的平台上实施特定的策略。 

用于计算设备平台的信息来自未经验证的源，例如可以更改的用户代理字符串。

#### <a name="locations"></a>位置

位置数据由 IP 地理位置数据提供。 管理员可以选择定义位置，并选择将某些位置（例如其组织的网络位置）标记为受信任位置。

#### <a name="client-apps"></a>客户端应用

默认情况下，条件访问策略适用于支持新式身份验证的浏览器应用、移动应用和桌面客户端。 

此分配条件允许条件访问策略将不使用新式身份验证的特定客户端应用程序作为目标。 这些应用程序包括 Exchange ActiveSync 客户端、不使用新式身份验证的旧版 Office 应用程序，以及 IMAP、MAPI、POP 和 SMTP 等邮件协议。

#### <a name="device-state"></a>设备状态

此控制条件用于排除已加入混合 Azure AD 或在 Intune 中标记为合规的设备。 可以通过此排除来阻止非托管设备。 

## <a name="access-controls"></a>访问控制

条件访问策略的访问控制部分用于控制策略的实施方式。

### <a name="grant"></a>授予

[Grant](concept-conditional-access-grant.md) 为管理员提供了一种策略实施方法，可在其中阻止或授予访问权限。

#### <a name="block-access"></a>阻止访问

阻止访问只会阻止指定分配下的访问。 阻止控制的功能十分强大，应对其有一定了解后再使用。

#### <a name="grant-access"></a>授予访问权限

授予控制可以触发一项或多项控制的实施。 

- 要求进行多重身份验证（Azure 多重身份验证）
- 要求将设备标记为合规 (Intune)
- 要求使用加入混合 Azure AD 的设备
- 需要批准的客户端应用
- 需要应用保护策略

管理员可以使用以下选项选择是需要上述控制之一还是所有已选控制。 多项控制的默认值为全部需要。

- 需要所有已选控制（控制和控制）
- 需要某一已选控制（控制或控制）

### <a name="session"></a>会话

[会话控制](concept-conditional-access-session.md) 可限制体验 

- 使用应用所强制实施的限制
   - 目前仅适用于 Exchange Online 和 SharePoint Online。
      - 传递设备信息，以允许控制授予完全访问权限或受限访问权限的体验。
- 使用条件访问应用控制
   - 使用 Microsoft Cloud App Security 发出的信号来执行以下操作： 
      - 阻止下载、剪切、复制和打印敏感文档。
      - 监视危险的会话行为。
      - 需要标记敏感文件。
- 登录频率
   - 能够更改新式身份验证的默认登录频率。
- 持久性浏览器会话
   - 可让用户在关闭再重新打开其浏览器窗口后保持登录状态。

## <a name="simple-policies"></a>简单策略

条件访问策略必须至少包含以下内容才能实施：

- 策略的**名称**。
- **分配**
   - 要应用策略的**用户和/或组**。
   - 要应用策略的**云应用或操作**。
- 访问控制 
   - **授予**或**阻止**控制

![空白条件访问策略](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

本文 [常见的条件性访问策略](concept-conditional-access-policy-common.md) 包括一些我们认为对大多数组织有用的策略。

## <a name="next-steps"></a>后续步骤

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[规划基于云的 Azure 多重身份验证部署](../authentication/howto-mfa-getstarted.md)

[用 Intune 管理设备符合性](/intune/device-compliance-get-started)

[Microsoft Cloud App Security 和条件访问](/cloud-app-security/proxy-intro-aad)