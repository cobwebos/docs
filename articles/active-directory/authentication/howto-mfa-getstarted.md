---
title: 云中的 Azure MFA 入门
description: Microsoft Azure 多重身份验证和条件访问入门
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: c2d0f14bca2b9ab062c61407479ab45a0104ff0a
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716257"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>部署基于云的 Azure 多重身份验证

Azure 多重身份验证 (Azure MFA) 入门是一个直截了当的过程。

在开始之前，请确保满足以下先决条件：

* Azure AD 租户中的全局管理员帐户。 在完成此步骤时如需帮助，请参阅 [Azure AD 入门](../get-started-azure-ad.md)一文
* 分配给用户的正确许可证。 如需详细信息，请参阅主题[如何获取 Azure 多重身份验证](concept-mfa-licensing.md)。

## <a name="choose-how-to-enable"></a>选择启用方法

**通过条件访问策略启用** - 本文将介绍此方法。 这是为用户启用双重验证的最灵活方式。 通过条件访问策略启用仅适用于云中的 Azure MFA，并且是 Azure AD 的高级功能。

通过 Azure AD Identity Protection 启用 - 此方法使用 Azure AD Identity Protection 风险策略，要求仅基于所有云应用程序的登录风险进行双重验证。 此方法需要 Azure Active Directory P2 授权。 有关此方法的详细信息，请参阅 [Azure Active Directory Identity Protection](../identity-protection/overview.md#risky-sign-ins)。

通过更改用户状态启用 - 这是需要进行双重验证的传统方法。 它适用于云中的 Azure MFA 以及 Azure MFA 服务器。 使用此方法要求用户**每次**登录时都执行双重验证并重写条件访问策略。 可在[如何要求对用户进行双重验证](howto-mfa-userstates.md)中找到有关此方法的详细信息

> [!Note]
> 有关许可和定价的详细信息，请参见 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) 和[多重身份验证](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)定价页。

## <a name="choose-authentication-methods"></a>选择身份验证方法

根据组织的要求至少为用户启用一种身份验证方法。 我们发现，如果为用户启用了身份验证，则 Microsoft Authenticator 应用可提供最佳用户体验。 如需了解哪些方法可用及其设置方法，请参阅[有哪些身份验证方法](concept-authentication-methods.md)一文。

## <a name="get-users-to-enroll"></a>让用户注册

启用条件访问策略后，当用户下次使用受该策略保护的应用时必须注册。 如果启用的策略要求所有云应用中的所有用户执行 MFA，则此操作可能会给用户和支持人员带来很大的麻烦。 我们建议要求用户预先使用注册门户 ([https://aka.ms/mfasetup](https://aka.ms/mfasetup)) 注册身份验证方法。 许多组织发现，创建海报、桌卡和电子邮件有助于促进服务的采用。

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>结合条件访问启用多重身份验证

使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。

### <a name="choose-verification-options"></a>选择验证选项

在启用 Azure 多重身份验证之前，组织必须确定允许的验证选项。 在本练习中，我们将启用电话呼叫和手机短信身份验证方法，因为这是大多数人都可以使用的常规选项。 有关身份验证方法及其用法的详细信息，请参阅[有哪些身份验证方法？](concept-authentication-methods.md)一文。

1. 浏览到“Azure Active Directory”、“用户”、“多重身份验证”
   ![通过 Azure 门户中的“Azure AD 用户”边栏选项卡访问多重身份验证门户](media/howto-mfa-getstarted/users-mfa.png) 
2. 在打开的新选项卡中，浏览到“服务设置”
3. 在“验证选项”下，选中以下可供用户使用的方法旁边的框
   * 拨打电话
   * 向手机发送短信

   ![在多重身份验证服务设置选项卡中配置验证方法](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. 单击“保存”
5. 关闭“服务设置”选项卡

### <a name="create-conditional-access-policy"></a>创建条件访问策略

1. 使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory”、“条件访问”。
1. 选择“新建策略”
1. 为策略提供一个有意义的名称
1. 在“用户和组”下
   * 在“包括”选项卡上，选中“所有用户”单选按钮
   * 建议：在“排除”选项卡上，选中“用户和组”旁边的框，并选择当用户无权访问其身份验证方法时用于排除的组。
   * 单击“完成” 
1. 在“云应用”下，选中“所有云应用”单选按钮
   * 可选：在“排除”选项卡上，选择组织不需要对其执行 MFA 的云应用。
   * 单击“完成” 
1. 在“条件”部分下
   * 可选：如果已启用 Azure Identity Protection，则可以选择在实施该策略的过程中评估登录风险。
   * 可选：如果已配置受信任的位置或命名的位置，则可以指定在策略中包括或排除这些位置。
1. 在“授予”下，确保选中“授权访问”单选按钮
    * 选中“要求多重身份验证”复选框
    * 单击“选择”
1. 跳过“会话”部分
1. 将“启用策略”开关设置为“开”
1. 单击“创建” 

![创建条件访问策略，为试验组中的 Azure 门户用户启用 MFA](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>测试 Azure 多重身份验证

为了确认条件访问策略是否正常工作，请测试登录到不需要 MFA 的某个资源，然后登录到需要 MFA 的 Azure 门户。

1. 在 InPrivate 或 incognito 模式下打开一个新的浏览器窗口并浏览到 [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)。
   * 使用在本文的先决条件部分创建的测试用户登录，你将发现，系统不会要求你完成 MFA。
   * 关闭浏览器窗口
2. 在 InPrivate 或 incognito 模式下打开新的浏览器窗口并浏览到 [https://portal.azure.com](https://portal.azure.com)。
   * 使用在本文的先决条件部分创建的测试用户登录，你将发现，现在系统要求你注册并使用 Azure 多重身份验证。
   * 关闭浏览器窗口

## <a name="next-steps"></a>后续步骤

祝贺你，现已在云中设置 Azure 多重身份验证。

若要配置其他设置（例如受信任的 IP、自定义语音消息和欺诈警报），请参阅[配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)一文

有关管理 Azure 多重身份验证的用户设置的信息，请参阅[管理云中 Azure 多重身份验证的用户设置](howto-mfa-userdevicesettings.md)一文

[针对 Azure 多重身份验证和 Azure AD 自助密码重置启用聚合注册](concept-registration-mfa-sspr-converged.md)
