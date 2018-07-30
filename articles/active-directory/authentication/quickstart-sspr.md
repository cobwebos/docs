---
title: 快速入门：Azure AD 自助服务密码重置
description: 在本快速入门中，将快速配置 Azure AD 自助服务密码重置来允许用户重置其自己的密码
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: c40cb3192d514d990ea2a5d66e1484ff204e9b10
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223551"
---
# <a name="quickstart-self-service-password-reset"></a>快速入门：自助服务密码重置

本快速入门分步展示了 IT 管理员如何配置自助服务密码重置 (SSPR)，通过这种简单方法使用户能够重置其密码或解锁其帐户。

## <a name="prerequisites"></a>先决条件

* 一个至少启用了试用版许可证的有效 Azure AD 租户。
* 一个具有全局管理员权限的帐户。
* 一个你知道其密码的非管理员测试用户，如果需要创建用户，请参阅[快速入门：向 Azure Active Directory 添加新用户](../add-users-azure-active-directory.md)一文。
* 非管理员测试用户所属的用于测试的一个试点组，如果需要创建组，请参阅[在 Azure Active Directory 中创建组并添加成员](../active-directory-groups-create-azure-portal.md)一文。

## <a name="enable-self-service-password-reset"></a>启用自助服务密码重置

> [!VIDEO https://www.youtube.com/embed/Pa0eyqjEjvQ]

1. 在现有的 Azure AD 租户（位于 **Azure 门户**的 **Azure Active Directory** 下）中选择“密码重置”。

2. 在“属性”页中，在“启用自助密码重置”选项下，选择“选定”。
    * 在“选择组”中，选择在本文的先决条件部分中创建的试点组。
    * 单击“ **保存**”。

3. 在“身份验证方法”页中，进行以下选择：
   * 重置所需的方法数：**1**
   * 用户可以使用的方法：
      * **移动电话**
      * **办公电话**
   * 单击“ **保存**”。

    ![身份验证][Authentication]

4. 在“注册”页上，进行以下选择：
   * 要求用户在登录时注册：**是**
   * 设置用户必须在几天后重新确认其身份验证信息：**365**

## <a name="test-self-service-password-reset"></a>测试自助服务密码重置

现在，让我们使用测试用户来测试 SSPR 配置。 因为 Microsoft 对 Azure 管理员帐户强制实施强身份验证要求，所以，使用管理员帐户进行测试可能会改变结果。 有关管理员密码策略的详细信息，请参阅[密码策略](concept-sspr-policy.md)文章。

1. 在 InPrivate 或 incognito 模式下打开一个新的浏览器窗口并浏览到 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)。
2. 使用非管理员测试用户进行登录并注册身份验证电话。
3. 完成后，单击标记为“看起来不错”的按钮并关闭浏览器窗口。
4. 在 InPrivate 或 incognito 模式下打开一个新的浏览器窗口并浏览到 [https://aka.ms/sspr](https://aka.ms/sspr)。
5. 输入非管理员测试用户的用户 ID、来自 CAPTCHA 的字符，然后单击“下一步”。
6. 根据验证步骤来重置密码

## <a name="clean-up-resources"></a>清理资源

禁用自助密码重置很容易。 打开 Azure AD 租户，转到“密码重置” > “属性”，然后在“启用自助密码重置”下选择“无”。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何快速为纯云用户配置自助服务密码重置。 若要了解如何完成更详细的推广，请继续学习我们的推广指南。

> [!div class="nextstepaction"]
> [推广自助服务密码重置](howto-sspr-deployment.md)

[Authentication]: ./media/quickstart-sspr/sspr-authentication-methods.png "可用的 Azure AD 身份验证方法和所需数量"
