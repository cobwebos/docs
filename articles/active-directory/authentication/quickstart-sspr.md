---
title: 快速入门 - Azure AD 自助式密码重置
description: 在本快速入门中，你将了解如何配置 Azure AD 自助式密码重置来允许用户重置其自己的密码并降低 IT 部门的开销。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: quickstart
ms.date: 12/10/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd178f52665c77f03a48d87a9e73c9019390bb21
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154850"
---
# <a name="quickstart-configure-azure-active-directory-self-service-password-reset"></a>快速入门：配置 Azure Active Directory 自助式密码重置

在本快速入门中，你将配置 Azure Active Directory (AD) 自助式密码重置 (SSPR)，以使用户能够重置其密码或解锁其帐户。 借助 SSPR，用户可以在没有支持人员或管理员帮助的情况下重置自己的凭据。 此功能允许用户重新获得对其帐户的访问权限，而无需等待额外的支持。

> [!IMPORTANT]
> 本快速入门演示管理员如何启用自助式密码重置。 如果你是已注册自助式密码重置的最终用户并且需要返回到你的帐户，请转到 https://aka.ms/sspr 。
>
> 如果你的 IT 团队尚未启用重置自己密码的功能，请联系支持人员以获得更多帮助。

## <a name="prerequisites"></a>必备条件

* 一个至少启用了试用版许可证的有效 Azure AD 租户。
    * 如果需要，[可免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个具有全局管理员权限的帐户。
* 你知道密码的非管理员测试用户，例如 testuser  。
    * 如果需要创建用户，请参阅[快速入门：向 Azure Active Directory 添加新用户](../add-users-azure-active-directory.md)。
* 包含非管理员测试用户成员的待测试试点组，如 SSPR-Test-Group  。
    * 如需创建一个组，请参阅如何[在 Azure Active Directory 中创建组并添加成员](../active-directory-groups-create-azure-portal.md)。

## <a name="enable-self-service-password-reset"></a>启用自助式密码重置

[在 YouTube 上以视频形式查看此过程](https://youtu.be/Pa0eyqjEjvQ)

1. 在 Azure 门户菜单或主页中，选择 Azure Active Directory，然后选择“密码重置”    。

1. 在“属性”页面的“启用自助式密码重置”选项下，选择“选定”    。
1. 选择“选择组”，然后选择在本文的先决条件部分创建的试点组，例如 SSPR-Test-Group   。 准备就绪后，选择“保存”。 
1. 在“身份验证方法”页面中，进行以下选择，然后选择“保存”   ：
    * 重置所需的方法数：**1**
    * 用户可以使用的方法：
        * **移动应用代码**
        * **电子邮件**

    > [!div class="mx-imgBorder"]
    > ![为 SSPR 选择身份验证方法][Authentication]

4. 在“注册”页面上，进行以下选择，然后选择“保存”   ：
   * 要求用户在登录时注册：**是**
   * 设置用户必须在几天后重新确认其身份验证信息：365 

## <a name="test-self-service-password-reset"></a>测试自助式密码重置

现在，使用你在上一部分选择的组中包含的测试用户（如 testuser）测试 SSPR 配置  。 因为 Microsoft 对 Azure 管理员帐户强制实施强身份验证要求，所以，使用管理员帐户进行测试可能会改变结果。 有关管理员密码策略的详细信息，请参阅[密码策略](concept-sspr-policy.md)文章。

1. 在 InPrivate 或 incognito 模式下打开一个新的浏览器窗口并浏览到 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)。
2. 使用非管理员测试用户（如 testuser）登录并注册身份验证电话  。
3. 完成后，选择标记为“看起来不错”的按钮并关闭浏览器窗口  。
4. 在 InPrivate 或 incognito 模式下打开一个新的浏览器窗口并浏览到 [https://aka.ms/sspr](https://aka.ms/sspr)。
5. 输入非管理员测试用户的用户 ID（如 testuser）、来自 CAPTCHA 的字符，然后选择“下一步”   。
6. 按照验证步骤来重置密码。

## <a name="clean-up-resources"></a>清理资源

要禁用自助式密码重置，请在 Azure 门户中搜索并选择 Azure Active Directory  。 选择“密码重置”  ，然后在“已启用自助服务密码重置”  下选择“无”  。 准备就绪后，选择“保存”。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解如何为纯云用户配置自助式密码重置。 若要了解如何完成更详细的推广，请继续学习我们的推广指南。

> [!div class="nextstepaction"]
> [推广自助式密码重置](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "可用的 Azure AD 身份验证方法和所需数量"

<!-- INTERNAL LINKS -->
[register-sspr]: ../user-help/active-directory-passwords-reset-register.md
[reset-password]: ../user-help/active-directory-passwords-update-your-own-password.md
