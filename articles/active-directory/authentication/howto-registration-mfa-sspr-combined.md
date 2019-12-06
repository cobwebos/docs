---
title: 组合注册-Azure Active Directory 入门
description: 启用组合 Azure AD 多重身份验证和自助密码重置注册（预览版）
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2bd3f61ffc07881ed8e502788b11fc0f435735b
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847348"
---
# <a name="enable-combined-security-information-registration-preview"></a>启用组合安全信息注册（预览）

在启用新体验之前，请查看[组合安全信息注册（预览版）](concept-registration-mfa-sspr-combined.md)一文，确保了解此功能的功能和影响。

![组合的安全信息注册增强体验](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| Azure 多重身份验证和 Azure Active Directory （Azure AD）自助服务密码重置的组合安全信息注册是 Azure AD 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

> [!NOTE]
> 为注册和管理安全信息而启用先前预览的组织应完成以下步骤，以启用增强的预览体验。 对于未在2019年10月8日发出交换机的组织，Microsoft 将切换先前预览的用户，以便向增强体验注册和管理安全信息。 
> 
> 如果你尚未启用任何版本的预览版，你的组织不会受到影响。

## <a name="enable-combined-registration"></a>启用合并注册

完成以下步骤以启用组合注册：

1. 以用户管理员或全局管理员身份登录到 Azure 门户。
2. 请参阅**Azure Active Directory** > **用户设置** > **管理访问面板预览功能的设置**。
3. 在 "**用户可以使用预览功能注册和管理安全信息-刷新**" 下，选择为**选定**的一组用户或为**所有**用户启用。

   ![为所有用户启用组合的安全信息预览体验](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> 从2019年3月开始，电话呼叫选项不能用于多重身份验证和 SSPR Azure AD 的用户。 SMS 消息不受此更改的影响。 电话呼叫选项将仍然可供付费 Azure AD 租户中的用户使用。

> [!NOTE]
> 启用组合注册后，如果在多重身份验证和 SSPR 中启用了这些方法，则通过新体验注册或确认其电话号码或移动应用的用户可将其用于多重身份验证和 SSPR。政策. 如果你随后禁用这一体验，则在 `https://aka.ms/ssprsetup` 访问上一个 SSPR 注册页面的用户将需要执行多重身份验证，然后才能访问该页面。

如果已在 Internet Explorer 中将 "站点到区域分配" 列表配置为 "区域分配"，则以下站点必须位于同一区域中：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>组合注册的条件性访问策略

使用条件性访问策略中的用户操作，可以保护用户注册 Azure 多重身份验证和自助密码重置的时间和方式。 此预览功能适用于启用了[组合注册预览](../authentication/concept-registration-mfa-sspr-combined.md)的组织。 在需要用户在 HR 载入期间从中心位置注册 Azure 多重身份验证和 SSPR （如受信任的网络位置）的组织中，可能会启用此功能。 有关在条件性访问中创建受信任位置的详细信息，请参阅文章[Azure Active Directory 条件访问中的位置条件是什么？](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>创建需要从受信任位置注册的策略

以下策略适用于所有选定的用户，这些用户尝试使用组合注册体验进行注册，并阻止访问，除非它们从标记为受信任网络的位置进行连接。

![创建用于控制安全信息注册的 CA 策略](media/howto-registration-mfa-sspr-combined/conditional-access-register-security-info.png)

1. 在**Azure 门户**中，浏览到**Azure Active Directory** > **条件访问**
1. 选择“新建策略”
1. 在 "名称" 中，输入此策略的名称。 例如，**受信任的网络上的合并安全信息注册**
1. 在 "**分配**" 下，单击 "**用户和组**"，然后选择你想要将此策略应用到的用户和组

   > [!WARNING]
   > 必须为用户启用[组合注册预览](../authentication/howto-registration-mfa-sspr-combined.md)。

1. 在 "**云应用或操作**" 下，选择 "**用户操作**"，然后选中 "**注册安全信息（预览版）** "
1. **条件** > **位置**
   1. 配置**是**
   1. 包含**任何位置**
   1. 排除**所有受信任的位置**
   1. 单击 "位置" 边栏选项卡上的 "**完成**"
   1. 单击 "条件" 边栏选项卡上的 "**完成**"
1.  > **Grant**下的**访问控制**
   1. 单击 "**阻止访问**"
   1. 然后单击 "**选择**"
1. 将 "**启用策略**" 设置为 **"开"**
1. 然后单击 "**创建**"

## <a name="next-steps"></a>后续步骤

[强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[多重身份验证和 SSPR 的可用方法](concept-authentication-methods.md)

[配置自助服务密码重置](howto-sspr-deployment.md)

[配置 Azure 多重身份验证](howto-mfa-getstarted.md)

[组合安全信息注册疑难解答](howto-registration-mfa-sspr-combined-troubleshoot.md)

[Azure Active Directory 条件访问中的位置条件是什么？](../conditional-access/location-condition.md)
