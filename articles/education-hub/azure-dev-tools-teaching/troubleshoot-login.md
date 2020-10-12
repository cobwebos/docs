---
title: 解决用于教学的 Azure 开发工具的登录错误
description: 本文介绍当用户登录到 Azure 开发工具进行教学时收到错误消息时，学生应采取的操作。
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 276ab2cc1d79235c00ce1bb50ff9b648bb1de6ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095878"
---
# <a name="troubleshooting-student-login-issues"></a>学生登录问题疑难解答
访问用于教学的 Azure 开发工具要求用户具有 Microsoft 帐户 (MSA) 。 如果用户的帐户尚不是 MSA 或已链接到 MSA，则学生将自动定向到创建 MSA。 如果域与 Active Directory 相关联，则该域下的所有帐户都已被视为 MSA。

如果学生尝试登录并收到以下错误消息，请使用下面所述的解决方案之一。

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="登录错误消息。" border="false":::

有两个解决方案：创建新 Microsoft 帐户或使用现有 Microsoft 帐户。

## <a name="create-a-new-microsoft-account"></a>创建新 Microsoft 帐户
### <a name="use-a-university-email-address"></a>使用大学电子邮件地址
如果你使用大学电子邮件地址登录 (例如， `John.Smith@university.edu`) ，则需要使用该电子邮件地址创建一个 Microsoft 帐户。 创建帐户是免费的，只需几分钟即可完成。 如果有 Microsoft 帐户，则可以使用单个用户名和密码自动登录到所有 Microsoft 产品。

### <a name="use-a-personal-email-address"></a>使用个人电子邮件地址
如果你使用个人电子邮件地址登录 (例如， `John.Smith@email.com`) 但还具有大学电子邮件地址，请尝试使用大学电子邮件地址。 如果你以前使用个人电子邮件地址登录到你的机构的 webstore 或没有大学电子邮件地址，则需要创建一个 Microsoft 帐户，并将其链接到你的个人电子邮件地址。

## <a name="use-an-existing-microsoft-account"></a>使用现有 Microsoft 帐户
如果学生 Microsoft 帐户 (例如 Xbox) ，则可以将该帐户连接到 Azure 开发工具帐户。

1. 转到 https://account.microsoft.com。
1. 用 Microsoft 帐户凭据登录。
1. 从顶部功能区菜单中选择 **信息** 。

1. 单击 " **管理登录 Microsoft 的方式**"。 系统会要求你验证身份。 你将通过电子邮件发送安全代码。

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="登录错误消息。" border="false":::

1. 输入通过电子邮件发送的安全代码。

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="登录错误消息。" border="false":::！

1. 单击 " **将电子邮件添加** 到你的帐户"，然后输入你的大学电子邮件地址。
下一次登录时，你可以使用你的大学电子邮件地址来访问用于教学的 Azure 开发工具。

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="登录错误消息。" border="false":::

## <a name="next-steps"></a>后续步骤
- [常见问题解答](program-faq.md)

- [支持选项](program-support.md)
