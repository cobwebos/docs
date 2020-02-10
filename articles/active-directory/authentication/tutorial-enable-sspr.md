---
title: 启用 Azure Active Directory 自助式密码重置
description: 本教程介绍如何为一组用户启用 Azure Active Directory 自助式密码重置，以及如何测试密码重置过程。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71b9052f364dfbae205dd324ba69de9578ccc225
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026310"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>教程：使用户能够使用 Azure Active Directory 自助式密码重置来解锁其帐户或重置密码

Azure Active Directory (Azure AD) 自助式密码重置 (SSPR) 使用户能够更改或重置其密码，而不需要管理员或支持人员的干预。 如果用户的帐户被锁定或用户忘记了自己的密码，他们可以按照提示取消对自己的阻止，回到工作状态。 当用户无法登录到其设备或应用程序时，此功能可减少呼叫支持人员的次数，降低生产力损失。

> [!IMPORTANT]
> 本快速入门演示管理员如何启用自助式密码重置。 如果你是已注册自助式密码重置的最终用户并且需要返回到你的帐户，请转到 https://aka.ms/sspr 。
>
> 如果你的 IT 团队尚未启用重置自己密码的功能，请联系支持人员以获得更多帮助。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为一组 Azure AD 用户启用自助式密码重置
> * 配置身份验证方法和注册选项
> * 以用户身份测试 SSPR 过程

## <a name="prerequisites"></a>必备条件

需有以下资源和特权才能完成本教程：

* 一个至少启用了试用版许可证的有效 Azure AD 租户。
    * 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个拥有“全局管理员”特权的帐户。 
* 你知道其密码的非管理员测试用户，例如 *testuser*。 在本教程中，你将使用此帐户来测试最终用户的 SSPR 体验。
    * 如果需要创建用户，请参阅[快速入门：向 Azure Active Directory 添加新用户](../add-users-azure-active-directory.md)。
* 该非管理员用户所属的组，例如 *SSPR-Test-Group*。 在本教程中，你将为此组启用 SSPR。
    * 如需创建一个组，请参阅如何[在 Azure Active Directory 中创建组并添加成员](../active-directory-groups-create-azure-portal.md)。

## <a name="enable-self-service-password-reset"></a>启用自助式密码重置

Azure AD 可让你为“无”、“选定”或“所有”用户启用 SSPR。    借助这种粒度，可以选择一部分用户来测试 SSPR 注册过程和工作流。 如果你很熟悉该过程，并且能够与更广泛的用户传达相关要求，则可以选择更多的用户组来启用 SSPR。 或者，可为 Azure AD 租户中的每个人启用 SSPR。

本教程将为测试组中的一组用户配置 SSPR。 以下示例使用组 *SSPR-Test-Group*。 根据需要提供自己的 Azure AD 组：

1. 使用拥有全局管理员权限的帐户登录到 [Azure 门户](https://portal.azure.com)。 
1. 搜索并选择“Azure Active Directory”，然后从左侧菜单中选择“密码重置”。  
1. 在“属性”页中的“已启用自助式密码重置”选项下，选择“选择组”。   
1. 浏览并选择 Azure AD 组（例如 *SSPR-Test-Group*），然后选择“选择”。 

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

    支持使用嵌套组作为较广泛 SSPR 部署的一部分。 确保为所选组中的用户分配适当的许可证。 目前不会针对这些许可要求运行验证过程。

1. 若要为所选用户启用 SSPR，请选择“保存”。 

## <a name="select-authentication-methods-and-registration-options"></a>选择身份验证方法和注册选项

当用户需要解锁其帐户或重置其密码时，系统会提示他们选择额外的确认方法。 此额外的身份验证因素可确保只会完成已批准的 SSPR 事件。 可以根据用户提供的注册信息，选择允许哪些身份验证方法。

1. 在“身份验证方法”页上的左侧菜单中，将“重置所需的方法数”设置为 *1*。  

    若要提高安全性，可以增加 SSPR 所需的身份验证方法数。

1. 选择组织允许的“可供用户使用的方法”。  对于本教程，请选中相应的框来启用以下方法：

    * *移动应用通知*
    * *移动应用代码*
    * *电子邮件*
    * *移动电话*
    * *办公电话*

1. 若要应用身份验证方法，请选择“保存”。 

用户必须先注册其联系信息，然后才能解锁其帐户或重置密码。 此联系信息用于前面步骤中配置的不同身份验证方法。

管理员可以手动提供此联系信息，或者用户可以转到注册门户来自行提供信息。 在本教程中，请配置为在用户下次登录时提示其注册。

1. 在“注册”页上的左侧菜单中，对“要求用户在登录时注册”选择“是”。   
1. 联系信息必须是最新的，这一点很重要。 如果在启动 SSPR 事件时联系信息已过时，用户可能无法解锁其帐户或重置其密码。

    将“在多少天后要求用户重新确认其身份验证信息”设置为“180”。  
1. 若要应用注册设置，请选择“保存”。 

## <a name="configure-notifications-and-customizations"></a>配置通知和自定义项

若要使用户随时了解帐户活动，可以配置电子邮件通知，以便在发生 SSPR 事件时发送通知。 这些通知可以涵盖普通用户帐户和管理员帐户。 对于管理员帐户，当使用 SSPR 重置特权管理员帐户密码时，此通知将提供额外的意识层。

1. 在“通知”页上的左侧菜单中配置以下选项： 

   * 将“重置密码时通知用户”选项设置为“是”。  
   * 将“当其他管理员重置其密码时通知所有管理员”设置为“是”。  

1. 若要应用通知首选项，请选择“保存”。 

如果用户在执行 SSPR 过程时需要更多帮助，你可以自定义“联系管理员”链接。 此链接将在 SSPR 注册过程中以及在用户解锁其帐户或重置其密码时使用。 为确保用户获得所需的支持，我们强烈建议提供自定义的支持电子邮件或 URL。

1. 在“自定义”页上的左侧菜单中，将“自定义支持链接”设置为“是”。   
1. 在“自定义支持电子邮件或 URL”字段中提供电子邮件地址或网页 URL（例如 *https://support.contoso.com/* ），通过此链接，用户可从你的组织获得更多帮助。 
1. 若要应用自定义链接，请选择“保存”。 

## <a name="test-self-service-password-reset"></a>测试自助式密码重置

启用并配置 SSPR 后，使用在上一部分选择的组（例如 *Test-SSPR-Group*）中的用户测试 SSPR 过程。 以下示例使用了 *testuser* 帐户。 提供自己的用户帐户，该帐户属于在本教程的第一部分为其启用了 SSPR 的组。

> [!NOTE]
> 测试自助式密码重置时，请使用非管理员帐户。 始终为管理员启用自助式密码重置，且管理员需要使用两种身份验证方法来重置其密码。

1. 若要查看手动注册过程，请以 InPrivate 或 incognito 模式打开新的浏览器窗口，并浏览到 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)。 用户在下次登录时，应会定向到此注册门户。
1. 使用非管理员测试用户（例如 *testuser*）登录，并注册身份验证方法的联系信息。
1. 完成后，选择标记为“看起来不错”的按钮并关闭浏览器窗口  。
1. 在 InPrivate 或 incognito 模式下打开一个新的浏览器窗口并浏览到 [https://aka.ms/sspr](https://aka.ms/sspr)。
1. 输入非管理员测试用户的帐户信息（例如 *testuser*）和 CAPTCHA（验证码）中的字符，然后选择“下一步”。 

    ![输入用户帐户信息以重置密码](media/tutorial-enable-sspr/password-reset-page.png)

1. 按照验证步骤来重置密码。 完成后，应会收到电子邮件通知，其中指出密码已重置。

## <a name="clean-up-resources"></a>清理资源

本教程系列的下一篇教程介绍如何配置密码写回。 此功能将 Azure AD SSPR 中的密码更改写回到本地 AD 环境。 若要继续学习本教程系列来配置密码写回，请暂时不要禁用 SSPR。

如果你不再想要使用本教程中配置的 SSPR 功能，请使用以下步骤将 SSPR 状态设置为“无”： 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”，然后从左侧菜单中选择“密码重置”。  
1. 在“属性”页中的“已启用自助式密码重置”选项下，选择“无”。   
1. 若要应用 SSPR 更改，请选择“保存”。 

## <a name="next-steps"></a>后续步骤

在本教程中，你为选定的用户组启用了 Azure AD 自助式密码重置。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 为一组 Azure AD 用户启用自助式密码重置
> * 配置身份验证方法和注册选项
> * 以用户身份测试 SSPR 过程

> [!div class="nextstepaction"]
> [启用 Azure 多重身份验证](tutorial-mfa-applications.md)
