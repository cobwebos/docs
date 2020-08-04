---
title: Azure Active Directory 中基于风险的用户登录保护
description: 在本教程中，你将了解如何启用 Azure 标识保护，以便在用户帐户上检测到风险登录行为时保护用户。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18b2fb520ecab8b233be3c93ef614a2bce01a75e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87034990"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-multi-factor-authentication-or-password-changes"></a>教程：对用户登录使用风险检测以触发 Azure 多重身份验证或密码更改

若要保护用户，可以在 Azure Active Directory (Azure AD) 中配置基于风险的策略，以便自动响应风险行为。 Azure AD 标识保护策略可以自动阻止登录尝试或要求执行其他操作，例如，要求更改密码或提示进行 Azure 多重身份验证。 这些策略可与现有的 Azure AD 条件访问策略配合使用，充当组织的额外保护层。 用户可能永远不会触发其中某项策略内的风险行为，但如果有人试图危害组织的安全，组织将受到保护。

> [!IMPORTANT]
> 本教程向管理员展示如何启用基于风险的 Azure 多重身份验证。
>
> 如果 IT 团队尚未启用使用 Azure 多重身份验证的功能，或者你在登录过程中遇到问题，请联系支持人员获得更多帮助。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 了解 Azure AD 标识保护的可用策略
> * 启用 Azure 多重身份验证注册
> * 启用基于风险的密码更改
> * 启用基于风险的多重身份验证
> * 针对用户登录尝试测试基于风险的策略

## <a name="prerequisites"></a>先决条件

需有以下资源和特权才能完成本教程：

* 一个至少启用了 Azure AD Premium P2 试用版许可证的有效 Azure AD 租户。
    * 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个拥有“全局管理员”特权的帐户。
* 为自助式密码重置和 Azure 多重身份验证配置的 Azure AD
    * 如果需要，请[完成该教程以启用 Azure AD SSPR](tutorial-enable-sspr.md)。
    * 如果需要，请[完成该教程以启用 Azure 多重身份验证](tutorial-enable-azure-mfa.md)。

## <a name="overview-of-azure-ad-identity-protection"></a>Azure AD 标识保护概述

Microsoft 每天都会收集和分析作为用户登录尝试的一部分的数万亿个匿名信号。 这些信号有助于构建良好用户登录行为的模式，并可帮助识别潜在的风险登录尝试。 Azure AD 标识保护可以查看用户登录尝试并在出现可疑行为时采取进一步的措施：

以下某些操作可能会触发 Azure AD 标识保护风险检测：

* 用户使用已泄漏的凭据。
* 从匿名 IP 地址登录。
* 以不可能的方式到达并非常去的位置。
* 从受感染的设备登录。
* 从具有可疑活动的 IP 地址登录。
* 从不熟悉的位置登录。

Azure AD 标识保护中提供了以下三个策略来保护用户并响应可疑活动。 可以选择启用或禁用强制执行策略，选择要将策略应用到的用户或组，并决定是否要在登录时阻止访问或提示执行其他操作。

* 用户风险策略
    * 识别并响应可能已泄露凭据的用户帐户。 可以提示用户创建新密码。
* 登录风险策略
    * 识别并响应可疑的登录尝试。 可以提示用户使用 Azure 多重身份验证提供其他形式的验证。
* MFA 注册策略
    * 确保用户已注册到 Azure 多重身份验证中。 如果登录风险策略提示进行 MFA，那么用户必须已注册到 Azure 多重身份验证中。

当启用策略用户或登录风险策略时，还可以选择风险级别的阈值 -“较低及以上”、“中等及以上”或“高”。 利用这种灵活性，可以决定在对任何可疑登录事件实施控制时所需采取的严格程度。

有关 Azure AD 标识保护的详细信息，请参阅[什么是 Azure AD 标识保护？](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>启用 MFA 注册策略

Azure AD 标识保护包含一个默认策略，该策略可帮助用户注册到 Azure 多重身份验证中。 如果要使用更多策略来保护登录事件，则需要用户已注册到 MFA 中。 当启用此策略时，它不需要用户在每次发生登录事件时执行 MFA。 此策略仅检查用户的注册状态，并要求他们在需要时进行预注册。

对于要为其启用更多 Azure AD 标识保护策略的用户，建议你为其启用 MFA 注册策略。 若要启用此策略，请完成以下步骤：

1. 使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，选择“安全性”，然后在“保护”菜单标题下选择“标识保护”。
1. 从左侧菜单中选择“MFA 注册策略”。
1. 默认情况下，该策略将应用于所有用户。 如果需要，请选择“分配”，然后选择要将此策略应用到的用户或组。
1. 在“控制”下，选择“访问”。 请确保选中“需要 Azure MFA 注册”选项，然后选择“选择”。
1. 将“强制执行策略”设置为“开”，然后选择“保存”。

    ![有关如何在 Azure 门户中要求用户注册到 MFA 的屏幕截图](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>启用用户风险策略以要求密码更改

Microsoft 会与研究人员、执法机构、Microsoft 安全团队以及其他受信任的源合作，以查找用户名和密码对。 当其中的某一对与你环境中的某个帐户匹配时，可能会要求进行基于风险的密码更改。 此策略和操作要求用户在登录前先更新其密码，以确保以前公开的任何凭据不再有效。

若要启用此策略，请完成以下步骤：

1. 从左侧菜单中选择“用户风险策略”。
1. 默认情况下，该策略将应用于所有用户。 如果需要，请选择“分配”，然后选择要将此策略应用到的用户或组。
1. 在“条件”下，选择“选择条件”>“选择风险级别”，然后选择“中等及以上”。
1. 单击“选择”，然后单击“完成” 。
1. 在“访问”下，选择“访问”。 请确保选中“允许访问”和“需要密码更改”选项，然后选择“选择”。
1. 将“强制执行策略”设置为“开”，然后选择“保存”。

    ![有关如何在 Azure 门户中启用用户风险策略的屏幕截图](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>启用登录风险策略以要求执行 MFA

大多数用户都有可以跟踪的正常行为。 当用户行为异于往常时，允许用户成功登录可能会有风险。 你可能需要阻止该用户，或者要求他们执行多重身份验证。 如果用户成功完成 MFA 质询，你可以将其登录尝试视为有效的登录尝试，并向其授予对应用程序或服务的访问权限。

若要启用此策略，请完成以下步骤：

1. 从左侧菜单中选择“登录风险策略”。
1. 默认情况下，该策略将应用于所有用户。 如果需要，请选择“分配”，然后选择要将此策略应用到的用户或组。
1. 在“条件”下，选择“选择条件”>“选择风险级别”，然后选择“中等及以上”。
1. 单击“选择”，然后单击“完成” 。
1. 在“访问”下，选择“选择控制”。 请确保选中“允许访问”和“需要多重身份验证”选项，然后选择“选择”。
1. 将“强制执行策略”设置为“开”，然后选择“保存”。

    ![有关如何在 Azure 门户中启用登录风险策略的屏幕截图](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>测试风险登录事件

大多数用户登录事件都不会触发在前面的步骤中配置的基于风险的策略。 用户可能永远不会看到要求其执行进一步的 MFA 或重置其密码的提示。 如果他们的凭据仍然安全，并且其行为与往常一致，则他们的登录事件将会成功。

若要测试在前面的步骤中创建的 Azure AD 标识保护策略，你需要采用一种方法来模拟风险行为或潜在攻击。 执行这些测试的步骤因要验证的 Azure AD 标识保护策略而异。 有关方案和步骤的详细信息，请参阅[模拟 Azure Active Directory 标识保护中的风险检测](../identity-protection/howto-identity-protection-simulate-risk.md)。

## <a name="clean-up-resources"></a>清理资源

如果已完成测试，不再需要启用基于风险的策略，请返回到需禁用的每项策略，然后将“强制实施策略”设置为“关”。

## <a name="next-steps"></a>后续步骤

在本教程中，已为 Azure AD 标识保护启用了基于风险的用户策略。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 了解 Azure AD 标识保护的可用策略
> * 启用 Azure 多重身份验证注册
> * 启用基于风险的密码更改
> * 启用基于风险的多重身份验证
> * 针对用户登录尝试测试基于风险的策略

> [!div class="nextstepaction"]
> [详细了解 Azure AD 标识保护](../identity-protection/overview-identity-protection.md)
