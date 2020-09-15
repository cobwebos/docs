---
title: 安全问题身份验证方法-Azure Active Directory
description: 了解如何在 Azure Active Directory 中使用安全问题，以帮助改进和保护登录事件
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3bf90909cdd3bd3013dc43d50a9589945f5215c7
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532592"
---
# <a name="authentication-methods-in-azure-active-directory---security-questions"></a>Azure Active Directory 安全问题中的身份验证方法

在登录事件期间，不会使用安全问题作为身份验证方法。 而是在自助服务密码重置 (SSPR) 过程中，可以使用安全问题来确认用户身份。 管理员帐户不能使用安全问题作为 SSPR 的验证方法。

当用户注册 SSPR 时，系统会提示他们选择要使用的身份验证方法。 如果他们选择使用安全问题，他们需要从一组提示问题中进行选择，然后提供自己的答案。

![Azure 门户的屏幕截图显示身份验证方法和安全问题的选项](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> 安全问题以专用、安全的方式存储在目录的用户对象上，并且只能由用户在注册期间回答。 管理员无法读取或修改用户的问题或答案。

因为某些人可能知道其他用户的问题的答案，因此这种方法可能比其他方法更不安全。 如果在 SSPR 中使用安全问题，建议在使用这些问题的同时结合另一种方法。 可以提示用户使用 Microsoft Authenticator 应用或电话身份验证在 SSPR 过程中验证其身份，并且仅当用户的手机或注册设备不在身边时才选择使用安全问题。

## <a name="predefined-questions"></a>预定义问题

以下预定义的安全问题可作为 SSPR 的验证方法使用。 所有这些安全问题都已根据用户的浏览器区域设置翻译和本地化为一整套 Microsoft 365 语言：

* 在哪个城市遇到了第一任配偶/伴侣？
* 父母在哪个城市相识？
* 与你年龄最近的兄弟姐妹居住在哪个城市？
* 父亲在哪个城市出生？
* 第一份工作是在哪个城市？
* 母亲在哪个城市出生？
* 2000 年元旦你在哪个城市？
* 中学时你最喜欢的老师姓什么？
* 填报了志愿，但未能入学的大学名称是什么？
* 举办第一次婚宴的地名是什么？
* 父亲的中间名是什么？
* 最喜欢的食品是什么？
* 外婆的姓氏和名字是什么？
* 母亲的中间名是什么？
* 最年长的兄弟姐妹的生日年份和月份是什么？ （例如：1985 年 11 月）
* 最年长的兄弟姐妹的中间名是什么？
* 爷爷的姓氏和名字是什么？
* 最年幼的兄弟姐妹的中间名是什么？
* 六年级时你就读哪所学校？
* 童年时最好的朋友的名字和姓氏是什么？
* 第一位伴侣的名字和姓氏是什么？
* 最喜欢的小学老师姓什么？
* 第一辆汽车或摩托车是哪个品牌和型号？
* 就读的第一所学校的校名是什么？
* 在哪家医院出生？
* 童年时第一个家庭地址的街道名称是什么？
* 童年时崇拜的人是谁？
* 最喜欢哪种填充动物玩具？
* 第一个宠物叫什么？
* 童年时的绰号是什么？
* 在中学最喜欢的运动是什么？
* 第一份工作是什么？
* 童年时的电话号码最后四位数是什么？
* 年少时，希望长大后当什么？
* 遇到过的最有名的人是谁？

## <a name="custom-security-questions"></a>自定义安全问题

为了增加灵活性，你可以定义自己的自定义安全问题。 自定义安全问题的最大长度为 200 个字符。

自定义安全问题不会像默认安全问题那样自动进行本地化。 所有自定义问题的显示语言是在管理用户界面中输入这些问题时所用的语言，即使用户浏览器的区域设置与此不同。 如果需要本地化的问题，应使用预定义的问题。

## <a name="security-question-requirements"></a>安全问题要求

对于默认和自定义安全问题，适用以下要求和限制：

* 答案包含的字符数下限为 3 个字符。
* 答案包含的字符数上限为 40 个字符。
* 用户不能多次回答同一问题。
* 用户不能为多个问题提供相同的答案。
* 可以使用任何字符集来定义问题和答案（包括 Unicode 字符）。
* 所定义问题的数量必须大于或等于注册所需问题数量。

## <a name="next-steps"></a>后续步骤

若要开始，请参阅 [自助服务密码重置教程 (SSPR) ][tutorial-sspr]。

要详细了解 SSPR 概念，请参阅[ Azure AD 自助式密码重置的工作原理][concept-sspr]。

详细了解如何使用 [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)配置身份验证方法。

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[concept-sspr]: concept-sspr-howitworks.md
