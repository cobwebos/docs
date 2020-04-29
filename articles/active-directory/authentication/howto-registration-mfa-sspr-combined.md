---
title: 组合注册-Azure Active Directory 入门
description: 启用组合 Azure AD 多重身份验证和自助密码重置注册
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639671"
---
# <a name="enable-combined-security-information-registration-in-azure-active-directory"></a>在 Azure Active Directory 中启用组合的安全信息注册

在组合注册之前，用户单独注册了 Azure 多重身份验证和自助服务密码重置（SSPR）的身份验证方法。 人们搞糊涂，使用了类似的方法进行多重身份验证和 SSPR，但他们不得不注册这两种功能。 现在，通过组合注册，用户可以注册一次，并获得多重身份验证和 SSPR 的优势。

在启用新体验之前，请先查看[组合安全信息注册](concept-registration-mfa-sspr-combined.md)一文，确保了解此功能的功能和影响。

![组合的安全信息注册增强体验](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

## <a name="enable-combined-registration"></a>启用合并注册

完成以下步骤以启用组合注册：

1. 以用户管理员或全局管理员身份登录到 Azure 门户。
2. 中转到**Azure Active Directory** > "**用户设置** > " "**管理用户功能预览设置**"。
3. 在 "**用户可以使用预览功能注册和管理安全信息**" 下，选择为**选择**的用户组或为**所有**用户启用。

   ![为所有用户启用组合的安全信息预览体验](media/howto-registration-mfa-sspr-combined/enable-the-combined-security-info-preview.png)

> [!NOTE]
> 启用组合注册后，如果在多重身份验证和 SSPR 策略中启用了这些方法，则通过新体验注册或确认其电话号码或移动应用的用户可将其用于多重身份验证和 SSPR。 如果你随后禁用这一体验，则需要访问上一 SSPR 注册页面的`https://aka.ms/ssprsetup`用户在访问该页面之前需要执行多重身份验证。

如果已在 Internet Explorer 中将 "站点到区域分配" 列表配置为 "区域分配"，则以下站点必须位于同一区域中：

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="conditional-access-policies-for-combined-registration"></a>组合注册的条件性访问策略

使用“条件访问策略”中的“用户操作”，现在可对用户注册 Azure 多重身份验证和自助式密码重置的时间和方式提供保护。 此功能适用于启用了[组合注册功能](../authentication/concept-registration-mfa-sspr-combined.md)的组织。 在需要用户在 HR 载入期间从中心位置注册 Azure 多重身份验证和 SSPR （如受信任的网络位置）的组织中，可能会启用此功能。

有关在条件访问中创建受信任位置的详细信息，请参阅文档 [Azure Active Directory 条件访问中的位置条件是什么？](../conditional-access/location-condition.md#named-locations)

### <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>创建要求从受信任的位置注册的策略

以下策略适用于尝试使用组合注册体验进行注册的所有选定用户，并阻止访问，除非它们从标记为受信任网络的位置进行连接。

1. 在**Azure 门户**中，浏览到**Azure Active Directory** > **安全** > **条件性访问**
1. 选择 **+ 新建策略**
1. 输入此策略的名称，例如*受信任的网络上的合并安全信息注册*。
1. 在“分配”  下，选择“用户和组”  。 选择要将此策略应用到的用户和组，然后选择 "**完成**"。

   > [!WARNING]
   > 必须为用户启用合并注册。

1. 在 "**云应用或操作**" 下，选择 "**用户操作**"。 选中 "**注册安全信息**"，然后选择 "**完成**"。

    ![创建条件性访问策略以控制安全信息注册](media/howto-registration-mfa-sspr-combined/require-registration-from-trusted-location.png)

1. 在 "**条件** > **位置**" 下，配置下列选项：
   1. 配置**是**
   1. 包含**任何位置**
   1. 排除**所有受信任的位置**
1. 在 "*位置*" 窗口中选择 "**完成**"，然后在 "*条件*" 窗口中选择 "**完成**"。
1. 在 "**访问控制** > " "**授权**" 下，选择 "**阻止访问**"，然后**选择**
1. 将 "**启用策略**" 设置为 **"开"**
1. 若要完成策略，请选择 "**创建**"

## <a name="next-steps"></a>后续步骤

如果需要帮助，请参阅如何[排查合并安全信息注册问题](howto-registration-mfa-sspr-combined-troubleshoot.md)或了解[Azure Active Directory 条件访问中的位置条件是什么？](../conditional-access/location-condition.md)

若要启用 Azure AD 租户中的功能，请参阅[启用自助服务密码重置](tutorial-enable-sspr.md)和[启用 Azure 多重身份验证](tutorial-enable-azure-mfa.md)的教程。

了解如何[在租户中启用组合注册](howto-registration-mfa-sspr-combined.md)，或[强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

你还可以查看[Azure 多重身份验证和 SSPR 的可用方法](concept-authentication-methods.md)。
