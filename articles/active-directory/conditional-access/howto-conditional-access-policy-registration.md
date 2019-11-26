---
title: 条件性访问-组合的安全信息-Azure Active Directory
description: 创建自定义条件访问策略以要求受信任位置进行安全信息注册
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5864424f003ce9254a6452d8374d78c54516f2bc
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322735"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>条件性访问：要求受信任的位置进行 MFA 注册

使用条件性访问策略中的用户操作，可以保护用户注册 Azure 多重身份验证和自助密码重置的时间和方式。 此预览功能适用于启用了[组合注册预览](../authentication/concept-registration-mfa-sspr-combined.md)的组织。 在需要用户在 HR 载入期间从中心位置注册 Azure 多重身份验证和 SSPR （如受信任的网络位置）的组织中，可能会启用此功能。 有关在条件性访问中创建受信任位置的详细信息，请参阅文章[Azure Active Directory 条件访问中的位置条件是什么？](../conditional-access/location-condition.md#named-locations)

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>创建需要从受信任位置注册的策略

以下策略适用于所有选定的用户，这些用户尝试使用组合注册体验进行注册，并阻止访问，除非它们从标记为受信任网络的位置进行连接。

1. 在**Azure 门户**中，浏览到**Azure Active Directory** > **条件性访问**。
1. 选择“新策略”。
1. 在 "名称" 中，输入此策略的名称。 例如，**受信任的网络上的合并安全信息注册**。
1. 在 "**分配**" 下，单击 "**用户和组**"，然后选择你想要将此策略应用到的用户和组。

   > [!WARNING]
   > 必须为用户启用[组合注册预览](../authentication/howto-registration-mfa-sspr-combined.md)。

1. 在 "**云应用或操作**" 下，选择 "**用户操作**"，然后选中 "**注册安全信息（预览版）** "。
1. 在 "**条件** > **位置**" 下。
   1. 配置 **"是"** 。
   1. 包含**任何位置**。
   1. 排除**所有受信任的位置**。
   1. 单击 "位置" 边栏选项卡上的 "**完成**"。
   1. 单击 "条件" 边栏选项卡上的 "**完成**"。
1.  > **Grant**下的 "**访问控制**"。
   1. 单击 "**阻止访问**"。
   1. 然后单击“选择”。
1. 将“启用策略”设置为“打开”。
1. 然后单击“保存”。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
