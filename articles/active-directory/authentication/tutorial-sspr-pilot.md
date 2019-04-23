---
title: 启用 Azure AD SSPR 试点
description: 在本教程中，你将为试点用户组启用 Azure AD 自助密码重置
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6e1095cc2c5937fa5de762f91a9830161b8d2a5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59362127"
---
# <a name="tutorial-complete-an-azure-ad-self-service-password-reset-pilot-roll-out"></a>教程：完成 Azure AD 自助密码重置试点推广

在本教程中，你将在组织中启用 Azure AD 自助密码重置 (SSPR) 的试点推广，并使用非管理员帐户进行测试。

对自助密码重置进行的任何测试都必须使用非管理员帐户。 Microsoft 管理管理员帐户的密码重置策略，需要使用更严格的身份验证方法。 此策略不允许使用安全性问题和回答，而要求使用两种重置方法。

> [!div class="checklist"]
> * 启用自助服务密码重置
> * 以用户身份测试 SSPR

## <a name="prerequisites"></a>先决条件

* 全局管理员帐户

## <a name="enable-self-service-password-reset"></a>启用自助服务密码重置

1. 使用全局管理员帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory”，然后选择“密码重置”。
1. 开始使用试点组时，请为组织中的部分用户启用自助密码。
   * 在“属性”页中的“启用自助密码重置”选项下，选择“已选”，然后选取一个试点组。
      * 仅所选特定 Azure AD 组的成员可以使用 SSPR 功能。 建议定义一组用户，在为概念证明部署此功能时使用此设置。 此处支持安全组嵌套。
      * 确保所选组中的用户已获得适当的许可。
   * 单击“保存”
1. 在“身份验证方法”页上
   * 将“重置所需的方法数”设置为 **1**
   * 选择组织允许的“可供用户使用的方法”。 对于本教程，请选中启用**电子邮件**、**移动电话**、**办公室电话**、**移动应用通知(预览)** 和**移动应用代码(预览)** 所对应的复选框。
   * 单击“保存”
1. 在“注册”页上
   * 针对“要求用户在登录时注册”选择“是”。
   * 将“在多少天后要求用户重新确认其身份验证信息”设置为“180”。 
   * 单击“保存”
1. 在“通知”页上
   * 将“重置密码时通知用户”选项设置为“是”。
   * 将“当其他管理员重置其密码时通知所有管理员”设置为“是”。
1. 在“自定义”页上
   * Microsoft 建议你将“自定义帮助台链接”设置为“是”，并在“自定义帮助台电子邮件或 URL”字段中提供电子邮件地址或网页 URL，以便用户能够从你的组织获取其他帮助。
   * 在本教程中，我们会将“自定义帮助台链接”设置为“否”。

现在，已为试点组中的云用户配置了自助密码重置。

## <a name="test-sspr-as-a-user"></a>以用户身份测试 SSPR

使用非管理员测试用户（属于试点组的成员）测试自助密码重置。 **使用非管理员测试用户（属于试点组的成员）测试自助密码重置。请注意，如果所使用的帐户具有任何管理员角色，那么身份验证方法和数量可能与所选的有所不同，因为由Microsoft管理管理员策略。**

1. 打开一个新的 InPrivate 或 incognito 模式浏览器窗口。
1. 通过 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 中的注册门户使用测试用户注册自助密码重置。
1. 使用同一测试用户浏览到自助密码重置门户 [https://aka.ms/sspr](https://aka.ms/sspr)，尝试使用在上一步提供的信息来重置密码。
1. 应该能够成功重置密码。

## <a name="clean-up-resources"></a>清理资源

如果你决定不再使用作为本教程的一部分配置的功能，请进行以下更改。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory”，然后选择“密码重置”。
1. 在“属性”页中，在“启用自助密码重置”选项下，选择“无”。
1. 单击“保存”

## <a name="next-steps"></a>后续步骤

在本教程中，你已经启用了 Azure AD 自助密码重置。 请继续学习下一教程，了解如何将本地 Active Directory 域服务基础结构集成到自助密码重置体验中。

> [!div class="nextstepaction"]
> [启用 SSPR 本地写回集成](tutorial-enable-writeback.md)
