---
title: 风险策略 - Azure 活动目录标识保护
description: 在 Azure 活动目录标识保护中启用和配置风险策略
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75706999"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>如何：配置和启用风险策略

正如我们在上一篇文章中了解到的[，身份保护策略](concept-identity-protection-policies.md)我们有两个风险策略，我们可以在我们的目录中启用。 

- 登录风险策略
- 用户风险策略

![安全概述页面，用于启用用户和登录风险策略](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

这两种策略都用于自动响应环境中的风险检测，并允许用户在检测到风险时自我修复。 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>先决条件 

如果您的组织希望允许用户在检测到风险时进行自我修复，则必须为自助服务密码重置和 Azure 多重身份验证注册用户。 我们建议[启用组合的安全信息注册体验](../authentication/howto-registration-mfa-sspr-combined.md)，以获得最佳体验。 允许用户自我修复可让他们更快地回到生产状态，而无需管理员干预。 管理员仍可以看到这些事件，并在事实发生后进行调查。 

## <a name="choosing-acceptable-risk-levels"></a>选择可接受的风险级别

组织必须决定他们愿意接受的风险级别，以平衡用户体验和安全状态。 

微软的建议是将用户风险策略阈值设置为 **"高"，** 将登录风险策略设置为 **"中"及"以上**"。

选择“高”阈值可减少触发策略的次数，最大程度地降低对用户的影响。**** 但是，它排除了策略中的**低**风险和**中等**风险检测，这可能不会阻止攻击者利用受攻击的标识。 选择**低**阈值会引入其他用户中断，但安全性增加。

## <a name="exclusions"></a>排除项

所有策略都允许排除用户，如[紧急访问或碎玻璃管理员帐户](../users-groups-roles/directory-emergency-access.md)。 组织可能会根据帐户的使用方式确定他们需要从特定策略中排除其他帐户。 应定期审查所有排除项，看它们是否仍然适用。

身份保护在某些风险检测中使用配置的受信任[网络位置](../conditional-access/location-condition.md)，以减少误报。

## <a name="enable-policies"></a>启用策略

要启用用户风险并登录风险策略，请完成以下步骤。

1. 导航到[Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure 活动目录** > **安全** > **标识保护** > **概述**。
1. 选择 **"配置用户风险策略**"。
   1. 在**分配**下
      1. **用户**- 如果限制推出，请选择**所有用户**或**选择个人和组**。
         1. 可以选择将用户从策略中排除。
      1. **条件** - **用户风险**微软的建议是将此选项设置为**高**。
   1. 在**控制下**
      1. **访问**- 微软的建议是**允许访问**和**要求密码更改**。
   1. **执行策略** - **On**
   1. **保存**- 此操作将返回到 **"概述"** 页。
1. 选择**配置登录风险策略**。
   1. 在**分配**下
      1. **用户**- 如果限制推出，请选择**所有用户**或**选择个人和组**。
         1. 可以选择将用户从策略中排除。
      1. **条件** - **登录风险**微软的建议是将此选项设置为**中和以上**。
   1. 在**控制下**
      1. **访问**- 微软的建议是**允许访问**和**要求多重身份验证**。
   1. **执行策略** - **On**
   1. **保存**

## <a name="next-steps"></a>后续步骤

- [启用 Azure 多重身份验证注册策略](howto-identity-protection-configure-mfa-policy.md)

- [什么是风险](concept-identity-protection-risks.md)

- [调查风险检测](howto-identity-protection-investigate-risk.md)

- [模拟风险检测](howto-identity-protection-simulate-risk.md)
