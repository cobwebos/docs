---
title: 条件访问 - 组合安全信息 - Azure Active Directory
description: 创建用于安全信息注册的自定义条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457903"
---
# <a name="conditional-access-securing-security-info-registration"></a>条件性访问：保护安全信息注册

使用“条件访问策略”中的“用户操作”，现在可对用户注册 Azure 多重身份验证和自助式密码重置的时间和方式提供保护。 此预览功能适用于启用了[组合注册预览](../authentication/concept-registration-mfa-sspr-combined.md)的组织。 如果要使用受信任的网络位置等条件来限制对注册 Azure 多重身份验证和自助密码重置（SSPR）的访问权限，组织中可能会启用此功能。 有关可用条件的详细信息，请参阅 "[条件性访问：条件](concept-conditional-access-conditions.md)" 一文。

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>创建要求从受信任的位置注册的策略

以下策略适用于所有试图使用组合注册体验进行注册的选定用户，并将阻止访问，除非他们从标记为受信任网络的位置进行连接。

1. 在“Azure 门户”**** 中，浏览到“Azure Active Directory”**** > “安全性”**** > “条件访问”****。
1. 选择“新策略”****。
1. 在“名称”中，输入此策略的名称。 例如，受信任网络上的组合安全信息注册****。
1. 在 "**分配**" 下，选择 "**用户和组**"，然后选择要应用此策略的用户和组。

   > [!WARNING]
   > 必须为用户启用[合并注册](../authentication/howto-registration-mfa-sspr-combined.md)。

1. 在 "**云应用或操作**" 下，选择 "**用户操作**"，然后选中 "**注册安全信息**"。
1. 在 "**条件** > **位置**" 下。
   1. 配置：“是”****。
   1. 包含**任何位置**。
   1. 排除**所有受信任的位置**。
   1. 在 "位置" 边栏选项卡上选择 "**完成**"。
   1. 在 "条件" 边栏选项卡中选择 "**完成**"。
1. 在 "**条件** > " "**客户端应用（预览）**" 下，将 "**配置**" 设置为 **"是"**，**然后选择**
1. "**访问控制** > **"。**
   1. 选择“阻止访问”。****
   1. 然后单击“选择”****。
1. 将“启用策略”设置为“打开”。********
1. 再选择“保存”  。

在此策略中，在步骤6中，组织可以做出一些选择。 上述策略需要从受信任的网络位置进行注册。 组织可以选择使用任何可用的条件来替代**位置**。 请记住，此策略是阻止策略，因此将阻止包含的所有内容，并且不允许任何与 include 匹配的内容。 

某些情况可能会选择使用设备状态，而不是上面步骤6中的位置：

6. 在 "**条件** > " "**设备状态（预览）**" 下。
   1. 配置：“是”****。
   1. 包括**所有设备状态**。
   1. 排除已**加入设备混合 Azure AD**和/或**标记为合规的设备**
   1. 在 "位置" 边栏选项卡上选择 "**完成**"。
   1. 在 "条件" 边栏选项卡中选择 "**完成**"。

> [!WARNING]
> 如果你使用设备状态作为策略中的条件，则可能会影响目录中的来宾用户。 [仅限报表模式](concept-conditional-access-report-only.md)可帮助确定策略决定的影响。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件性访问仅报告模式来确定影响](howto-conditional-access-report-only.md)

[使用条件性访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
