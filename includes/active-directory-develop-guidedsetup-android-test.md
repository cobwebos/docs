---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: e87a63a0dad5e1f93b1bea62039abee8ded78ab7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988311"
---
## <a name="test-your-app"></a>测试应用

1. 将代码运行到设备/模拟器。
2. 尝试使用 Azure Active Directory 帐户（工作或学校帐户）或 Microsoft 帐户（live.com、outlook.com）登录。 

    ![测试应用程序](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![输入用户名和密码](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>许可应用

用户首次登录应用程序时，系统会提示他们同意应用所需的权限，如下所示： 

![许可应用程序访问](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)

### <a name="success"></a>成功！

登录并同意后，应用会显示 Microsoft Graph API 的响应。 此特定调用是针对 **/me** 终结点，并返回[用户配置文件](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get)。 有关其他 Microsoft Graph 终结点的列表，请参阅 [Microsoft Graph API 开发人员文档](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries)。

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>范围和委托的权限

Microsoft Graph API 需要 *User.Read* 作用域来读取用户的个人资料。 在应用程序注册门户中注册的每个应用程序中，都会自动添加此作用域。 其他 API 将需要其他作用域。 例如，Microsoft Graph API 需要 *Calendars.Read* 作用域来列出用户的日历。

若要访问用户的日历，请将 *Calendars.Read* 委托权限添加到应用程序注册信息。 然后，将 *Calendars.Read* 作用域添加到 `acquireTokenSilent` 调用。 

> [!NOTE]
> 如果你更改了应用注册，系统可能会提示你的用户进行其他同意。

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
