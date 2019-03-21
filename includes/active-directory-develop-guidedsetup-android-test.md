---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: c8e5e4f826d7835a1fd38d1db5bfeab19b679b30
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203678"
---
## <a name="test-your-app"></a>测试应用

1. 将代码运行到设备/模拟器。
2. 请尝试使用 Azure Active Directory 帐户 （工作或学校帐户） 或 Microsoft account （live.com、 outlook.com） 进行登录。 

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
