---
title: 开始合并注册 - Azure 活动目录
description: 启用组合 Azure AD 多重身份验证和自助服务密码重置注册
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b063253ee49ab58c2685f359b4bb8a4079532
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81639671"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>在 Azure 活动目录中启用组合安全信息注册

在合并注册之前，用户分别注册了 Azure 多重身份验证和自助服务密码重置 （SSPR） 的身份验证方法。 人们感到困惑的是，类似的方法被用于多重身份验证和SSPR，但他们必须注册这两个功能。 现在，通过合并注册，用户可以注册一次，并获得多重身份验证和 SSPR 的优势。

在启用新体验之前，请查看文章["组合安全信息注册](concept-registration-mfa-sspr-combined.md)"，以确保您了解此功能的功能和效果。

![组合安全信息注册增强体验](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>启用合并注册

完成以下步骤以启用合并注册：

1. 以用户管理员或全局管理员的身份登录到 Azure 门户。
2. 转到**Azure 活动目录** > **用户设置** > **管理用户功能预览设置**。
3. **在"用户"下可以使用预览功能注册和管理安全信息**，选择为**选定的**用户组或**所有用户**启用。

   ![为所有用户启用组合安全信息预览体验](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> 启用合并注册后，通过新体验注册或确认其电话号码或移动应用的用户可以使用它们进行多重身份验证和 SSPR，如果这些方法在多重身份验证和 SSPR 策略中启用。 如果随后禁用此体验，则转到以前的 SSPR 注册页`https://aka.ms/ssprsetup`的用户将需要执行多重身份验证，然后才能访问该页面。

如果在 Internet 资源管理器中配置了站点到区域分配列表，则以下站点必须位于同一区域中：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>合并注册的条件访问策略

使用“条件访问策略”中的“用户操作”，现在可对用户注册 Azure 多重身份验证和自助式密码重置的时间和方式提供保护。 此功能可供启用[合并注册功能](../authentication/concept-registration-mfa-sspr-combined.md)的组织使用。 在希望用户从中心位置（如 HR 入职期间受信任的网络位置）注册 Azure 多重身份验证和 SSPR 的组织中，可以启用此功能。

有关在条件访问中创建受信任位置的详细信息，请参阅文档 [Azure Active Directory 条件访问中的位置条件是什么？](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>创建要求从受信任的位置注册的策略

以下策略适用于尝试使用组合注册体验进行注册的所有选定用户，并阻止访问，除非他们从标记为受信任网络的位置进行连接。

1. 在**Azure 门户**中，浏览到**Azure 活动目录** > **安全** > **条件访问**
1. 选择 **= 新策略**
1. 输入此策略的名称，例如 *"受信任网络上的安全信息合并注册*"。
1. 在“分配”**** 下，选择“用户和组”****。 选择要应用此策略的用户和组，然后选择 **"完成**"。

   > [!WARNING]
   > 必须启用用户进行合并注册。

1. 在 **"云应用"或"操作"** 下，选择 **"用户操作**"。 检查**注册安全信息**，然后选择 **"完成**"。

    ![创建条件访问策略以控制安全信息注册](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. 在 **"条件** > **位置**"下，配置以下选项：
   1. 配置**是**
   1. 包括**任何位置**
   1. 排除**所有受信任位置**
1. 在 *"位置*"窗口中选择 **"完成**"，然后在"*条件*"窗口中选择 **"完成**"。
1. 在 **"访问控制** > **授予"下**，选择 **"阻止访问**"，然后**选择**
1. 将**启用策略**设置为 **"打开"**
1. 要最终确定策略，请选择 **"创建"**

## <a name="next-steps"></a>后续步骤

如果您需要帮助，请参阅如何[排除组合安全信息注册故障](howto-registration-mfa-sspr-combined-troubleshoot.md)，或了解[Azure 活动目录条件访问中的位置条件是什么？](../conditional-access/location-condition.md)

要启用 Azure AD 租户中的功能，请参阅[启用自助服务密码重置](tutorial-enable-sspr.md)和[启用 Azure 多重身份验证 的](tutorial-enable-azure-mfa.md)教程。

了解如何[在租户中启用合并注册](howto-registration-mfa-sspr-combined.md)或[强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

您还可以查看 Azure[多重身份验证和 SSPR 的可用方法](concept-authentication-methods.md)。
