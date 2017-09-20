---
title: "如何使用 Azure MFA 中的应用密码？ | Microsoft 文档"
description: "此页面将帮助用户了解什么是应用密码，以及在 Azure MFA 中，应用密码有什么作用。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: kgremban
ms.custom: end-user
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 1ecc2bdef5ff7ef8ed8dded7dc12428ce9657821
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Azure 多重身份验证中的应用密码是什么？
某些非浏览器应用（例如使用 Exchange Active Sync 的 Apple 本机电子邮件客户端）目前不支持多重身份验证。 多重身份验证是按用户启用的。  这意味着，用户在以下情况下不能使用多重身份验证：

- 已为用户启用多重身份验证
- 用户尝试使用非浏览器应用。

应用密码允许用户使用该应用。

创建应用密码后，可在这些非浏览器应用中使用此密码取代原始密码。 注册双重验证时，就是在告知 Microsoft 不让任何人使用密码登录（如果这些人还无法执行第二个验证）。 手机上的 Apple 本机电子邮件客户端不能以身份登录，因为它无法请求双重验证。 此问题的解决方案是创建不经常使用的更安全的应用密码。 应用密码仅适用于不支持双重验证的这些应用。 请使用应用密码，以便应用可以绕过多重身份验证并继续工作。


> [!NOTE]
> Office 2013 客户端（包括 Outlook）支持新的身份验证协议，并可用于双重验证。 不需要对 Office 2013 客户端使用应用密码。  有关详细信息，请参阅 [Office 2013 现代身份验证公共预览版发布声明](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。


## <a name="how-to-use-app-passwords"></a>如何使用应用密码
下面是有关应用密码的一些重要事项：

* 不要创建自己的应用密码。 它们会自动生成。
* 目前，每个用户有 40 个密码的限制。 
* 如果在达到该限制后尝试创建应用密码，则必须删除一个现有的应用密码，然后再创建新密码。
* 对每个设备（而不是每个应用程序）使用一个应用密码。 例如，可以为笔记本电脑创建一个应用密码，并将该应用密码用于该笔记本电脑上的所有应用程序。 然后，创建要用于台式机上的所有应用的第二个应用密码。 
* 首次注册双重验证时，系统会提供一个应用密码。  如果需要更多的应用密码，可以创建。



## <a name="creating-and-deleting-app-passwords"></a>创建和删除应用密码
首次登录期间，系统将提供一个可用的应用密码。  以后你还可以创建和删除应用密码。 删除应用密码的方式取决于使用多重身份验证的方式。 回答以下问题以确定应在哪里管理应用密码： 

1. 是否要对个人 Microsoft 帐户使用双重验证？ 如果是，应参考[应用密码和双重验证](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-two-step-verification)一文以获取帮助。 如果不是，请继续回答第二个问题。

2. 好吧，那么请对工作或学校帐户使用双重验证。 是否要使用它登录到 Office 365 应用？ 如果是，应参考[为 Office 365 创建应用密码](https://support.office.com/article/Create-an-app-password-for-Office-365-3e7c860f-bda4-4441-a618-b53953ee1183)以获取帮助。 如果不是，请继续回答第三个问题。 

3. 是否要在 Microsoft Azure 中使用双重验证？ 如果是，请继续阅读本文的[在 Azure 门户中管理应用密码](#manage-app-passwords-in-the-Azure-portal)部分。 如果不是，请继续回答第四个问题。

4. 不确定在哪里使用双重验证？ 请继续阅读本文的[使用 MyApps 门户管理应用密码](#manage-app-passwords-with-the-myapps-portal)部分。 


## <a name="manage-app-passwords-in-the-azure-portal"></a>在 Azure 门户中管理应用密码
如果在 Azure 中使用双重验证，则需要通过 Azure 门户创建应用密码。

### <a name="to-create-app-passwords-in-the-azure-portal"></a>在 Azure 门户中创建应用密码
1. 登录到 Azure 经典门户。
2. 在顶部，右键单击用户名并选择“其他安全性验证”。
3. 在验证页的顶部选择应用密码
4. 单击“创建” 。
5. 输入应用密码的名称，并单击“下一步”
6. 将应用密码复制到剪贴板，然后将它粘贴到应用。
   
   ![云](./media/multi-factor-authentication-end-user-app-passwords/app2.png)


### <a name="to-delete-app-passwords-in-the-azure-portal"></a>在 Azure 门户中删除应用密码
1. 登录到 Azure 经典门户。
2. 在顶部，右键单击用户名并选择“其他安全性验证”。
3. 在顶部的“其他安全性验证”旁边，选择“应用密码”。
4. 在要删除的应用密码旁边，选择“删除”。
5. 单击“是”确认删除。
6. 删除应用密码后，可以单击“关闭”。


## <a name="manage-app-passwords-with-the-myapps-portal"></a>使用 MyApps 门户管理应用密码。
如果不确定多重身份验证的使用方式，则始终可以通过 myapps 门户创建和删除应用密码。

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>使用 Myapps 门户创建应用密码
1. 登录 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 单击右上角的名称，并选择“配置文件”。
3. 选择“其他安全性验证”。
   ![选择“其他安全性验证”- 屏幕截图](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. 选择“应用密码”。
   ![选择“应用密码”- 屏幕截图](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. 单击“创建” 。
6. 输入应用密码的名称，并单击“下一步”。
7. 将应用密码复制到剪贴板，然后将它粘贴到应用。
   ![创建应用密码](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>使用 Myapps 门户删除应用密码
1. 登录 [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. 在顶部选择配置文件。
3. 选择“其他安全性验证”。

   ![选择“其他安全性验证”- 屏幕截图](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. 选择“应用密码”。

   ![选择“应用密码”- 屏幕截图](./media/multi-factor-authentication-end-user-app-passwords/apppass2.png)

5. 在要删除的应用密码旁边，单击“删除”。

   ![删除应用密码](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)

6. 通过单击“是”确认要删除该密码。
7. 删除应用密码后，可以单击“关闭”。

## <a name="next-steps"></a>后续步骤

- [管理双重验证设置](multi-factor-authentication-end-user-manage-settings.md)

- 试用 [Microsoft 验证器应用](microsoft-authenticator-app-how-to.md)以验证是否可以使用应用通知（而不是通过接收短信或电话）登录。 

