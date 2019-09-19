---
title: 构建条件访问策略-Azure Active Directory
description: 可用于生成条件性访问策略的所有选项及其含义是什么？
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87f3e815f541ad4cfabc22d917ca9cecba47b50f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077602"
---
# <a name="building-a-conditional-access-policy"></a>生成条件性访问策略

如条件性[访问](overview.md)一文中所述，条件性访问策略是**赋值**和**访问控制**的 if-then 语句。 条件性访问策略将信号组合在一起，以做出决策，并强制实施组织策略。

组织如何创建这些策略？ 需要执行哪些操作？

![条件性访问（信号 + 决策 + 强制 = 策略）](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>作业

"分配" 部分控制条件访问策略的人员、内容和位置。

### <a name="users-and-groups"></a>用户和组

用户和组分配策略将包括或排除的用户。 此分配可以包括所有用户、特定的用户组、目录角色或外部来宾用户。 

### <a name="cloud-apps-or-actions"></a>云应用或操作

云应用或操作可以包括或排除将服从策略的云应用程序或用户操作。

### <a name="conditions"></a>条件

策略可以包含多个条件。

#### <a name="sign-in-risk"></a>登录风险

对于具有[Azure AD Identity Protection](../identity-protection/overview.md)的组织而言，生成的风险检测可能会影响你的条件访问策略。

#### <a name="device-platforms"></a>设备平台

具有多个设备操作系统平台的组织可能希望在不同的平台上强制实施特定策略。 

用于计算设备平台的信息来自未经验证的源，如可以更改的用户代理字符串。

#### <a name="locations"></a>位置

位置数据由 IP 地理位置数据提供。 管理员可以选择定义位置，并选择将某些位置标记为 "受信任"，如其组织的网络位置。

#### <a name="client-apps"></a>客户端应用

默认情况下，条件访问策略适用于支持新式身份验证的浏览器应用、移动应用和桌面客户端。 

此分配条件允许条件性访问策略针对不使用新式身份验证的特定客户端应用程序。 这些应用程序包括 Exchange ActiveSync 客户端、不使用新式身份验证的旧版 Office 应用程序，以及 IMAP、MAPI、POP 和 SMTP 等邮件协议。

#### <a name="device-state"></a>设备状态

此控件用于排除混合 Azure AD 的设备或在 Intune 中标记为符合的设备。 可以通过此排除来阻止非托管设备。 

## <a name="access-controls"></a>访问控制

条件性访问策略的 "访问控制" 部分控制如何强制实施策略。

### <a name="grant"></a>授予

#### <a name="block-access"></a>阻止访问

阻止访问只会阻止在指定分配下进行访问。 Block 控件的功能非常强大，应年长相关知识。

#### <a name="grant-access"></a>授予访问权限

Grant 控件可以触发一个或多个控件的强制执行。 

- 需要多重身份验证（Azure 多重身份验证）
- 要求设备标记为符合（Intune）
- 需要混合 Azure AD 联接的设备
- 需要核准的客户端应用
- 需要应用保护策略

管理员可以选择要求使用以下选项之一之前的控件或所有选定的控件。 多个控件的默认值为 "全部需要"。

- 要求所有选定控件（控件和控件）
- 需要一个选定控件（控件或控件）

### <a name="session"></a>会话

会话控制可限制体验 

- 使用应用强制实施的限制
   - 目前仅适用于 Exchange Online 和 SharePoint Online。
      - 传递设备信息，以允许控制授予完全访问权限或受限访问权限。
- 使用条件访问应用控制
   - 使用来自 Microsoft Cloud App Security 的信号执行如下操作： 
      - 阻止对敏感文档进行下载、剪切、复制和打印。
      - 监视风险会话行为。
      - 要求对敏感文件进行标记。
- 登录频率
   - 能够更改新式身份验证的默认登录频率。
- 持久性浏览器会话
   - 允许用户在关闭并重新打开其浏览器窗口后保持登录。

## <a name="simple-policies"></a>简单策略

条件性访问策略必须至少包含以下内容才能强制执行：

- 策略的**名称**。
- **分配**
   - 要向其应用策略的**用户和/或组**。
   - 要向其应用策略的**云应用或操作**。
- 访问控制
   - **Grant**或**Block**控件

![空白条件性访问策略](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

## <a name="next-steps"></a>后续步骤

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[常见的条件访问策略](concept-conditional-access-policy-common.md)

[规划基于云的 Azure 多重身份验证部署](../authentication/howto-mfa-getstarted.md)

[用 Intune 管理设备符合性](https://docs.microsoft.com/intune/device-compliance-get-started)

[Microsoft Cloud App Security 和条件访问](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
