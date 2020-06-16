---
title: 条件访问 - 合并的安全信息 - Azure Active Directory
description: 创建用于安全信息注册的自定义条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: acf17f4cef43fe4221781deedb130ef587208550
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993739"
---
# <a name="conditional-access-securing-security-info-registration"></a>条件访问：保护安全信息注册

现在可以使用条件访问策略中的用户操作保护用户注册 Azure 多重身份验证和自助密码重置的时间和方式。 此预览功能适用于已启用[合并注册预览](../authentication/concept-registration-mfa-sspr-combined.md)的组织。 如果要使用受信任的网络位置等条件来限制对注册 Azure 多重身份验证和自助密码重置 (SSPR) 的访问权限，组织可能会启用此功能。 有关可用条件的详细信息，请参阅[条件访问：条件](concept-conditional-access-conditions.md)一文。

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>创建需要从受信任位置注册的策略

以下策略适用于尝试使用合并注册体验进行注册的所有选定用户，并阻止他们访问，除非他们从标记为受信任网络的位置进行连接。

1. 在“Azure 门户”中，浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 在“名称”中，输入此策略的名称。 例如，“受信任网络上的合并安全信息注册”。
1. 在“分配”下，选择“用户和组”，并选择要将此策略应用到的用户和组。

   > [!WARNING]
   > 必须为[合并注册](../authentication/howto-registration-mfa-sspr-combined.md)启用用户。

1. 在“云应用或操作”下，选择“用户操作”，选中“注册安全信息”。
1. 在“条件” > “位置”下。
   1. 配置“是”。
   1. 包括任何位置。
   1. 排除所有受信任的位置。
   1. 在“位置”边栏选项卡上选择“完成”。
   1. 在“条件”边栏选项卡中，单击“完成”。
1. 在“条件” > “客户端应用（预览版）”下，将“配置”设置为“是”，然后选择“完成”。
1. 在“访问控制” > “授予”下。
   1. 选择“阻止访问”。
   1. 然后单击“选择”。
1. 将“启用策略”设置为“打开”。
1. 再选择“保存”。

在此策略的步骤 6 中，组织可以做出一些选择。 上述策略需要从受信任的网络位置进行注册。 组织可以选择使用任何可用的条件来代替“位置”。 请记住，此策略是阻止策略，因此将阻止包含的所有内容，并允许与包含内容不匹配的任何内容。 

某些情况可能会选择使用设备状态，而非以上步骤 6 中的位置：

6. 在“条件” > “设备状态（预览版）”下。
   1. 配置“是”。
   1. 包括“所有设备状态”。
   1. 排除“已建立混合 Azure AD 联接的设备”和/或“标记为合规的设备”
   1. 在“位置”边栏选项卡上选择“完成”。
   1. 在“条件”边栏选项卡中，单击“完成”。

> [!WARNING]
> 如果你使用设备状态作为策略中的条件，则可能会影响目录中的来宾用户。 [仅限报告模式](concept-conditional-access-report-only.md)有助于确定策略决策的影响。
> 请注意，仅限报告模式不适用于具有“用户操作”作用域的 CA 策略。

## <a name="next-steps"></a>后续步骤

[常用条件访问策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-report-only.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
