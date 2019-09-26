---
title: Microsoft 标识平台 Python Web 应用快速入门 | Azure
description: 了解如何使用 OAuth2 在 Python Web 应用中实现 Microsoft 登录
services: active-directory
documentationcenter: dev-center-name
author: abhidnya13
editor: ''
ms.assetid: 9551f0b5-04f2-44d7-87b5-756409180fe9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: abpati
ms.custom: aaddev
ms.openlocfilehash: c5817427102bf10dcd1ece932b0f582d973efaf7
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257887"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>快速入门：向 Python Web 应用添加 Microsoft 登录功能

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

本快速入门介绍如何将 Python Web 应用程序与 Microsoft 标识平台集成。 应用会将用户登录，获取用于调用 Microsoft Graph API 的访问令牌，并针对 Microsoft Graph API 发出请求。

完成本指南后，应用程序将接受个人 Microsoft 帐户（包括 outlook.com、live.com 和其他帐户）进行登录，还能够接受使用 Azure Active Directory 的任何公司或组织的工作或学校帐户进行登录。

![显示本快速入门生成的示例应用的工作原理](media/quickstart-v2-python-webapp/python-quickstart.svg)

## <a name="prerequisites"></a>先决条件

若要运行此示例，需要：

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) 或 [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/)、[Flask-Session](https://pythonhosted.org/Flask-Session/)、[请求](https://2.python-requests.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) 
- 一个 Azure Active Directory (Azure AD) 租户。 有关如何获取 Azure AD 租户的详细信息，请参阅[如何获取 Azure AD 租户](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)。

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>注册并下载快速入门应用
>
> 可以使用两个选项来启动快速入门应用程序：“快速”（选项 1）和“手动”（选项 2）
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>选项 1：注册并自动配置应用，然后下载代码示例
>
> 1. 访问 [Azure 门户 - 应用注册](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)。
> 1. 选择“新注册”。 
> 1. 输入应用程序的名称并选择“注册”  。
> 1. 遵照说明下载内容，系统会自动配置新应用程序。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>选项 2：注册并手动配置应用程序和代码示例
>
> #### <a name="step-1-register-your-application"></a>步骤 1：注册应用程序
>
> 若要手动注册应用程序并将应用的注册信息添加到解决方案，请执行以下步骤：
>
> 1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
> 1. 如果你的帐户有权访问多个租户，请在右上角选择该帐户，并将门户会话设置为所需的 Azure AD 租户。
> 1. 导航到面向开发人员的 Microsoft 标识平台的[应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页。
> 1. 选择“新注册”。 
> 1. 出现“注册应用程序”页后，请输入应用程序的注册信息： 
>      - 在“名称”  部分输入一个会显示给应用用户的有意义的应用程序名称，例如 `python-webapp`。
>      - 在“支持的帐户类型”下，选择“任何组织目录中的帐户和个人 Microsoft 帐户”。  
>      - 在“重定向 URI”部分的下拉列表中，选择“Web”平台，然后将值设置为 `http://localhost:5000/getAToken`。  
>      - 选择“注册”  。 在应用的“概述”页上，记下“应用程序(客户端) ID”值，供稍后使用   。
> 1. 在左侧菜单中选择“证书和机密”，然后在“客户端机密”部分单击“新建客户端机密”：   
>
>      - 键入（实例应用机密）的密钥说明。
>      - 选择密钥持续时间“1 年”。 
>      - 单击“添加”时，将显示密钥值。 
>      - 复制密钥的值。 稍后需要用到此值。
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>步骤 1：在 Azure 门户中配置应用程序
>
> 若要正常运行本快速入门中的代码示例，需要：
>
> 1. 将答复 URL 添加为 `http://localhost:5000/getAToken`。
> 1. 创建客户端机密。
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [执行此更改]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![已配置](media/quickstart-v2-aspnet-webapp/green-check.png) 应用程序已使用此属性进行配置

#### <a name="step-2-download-your-project"></a>步骤 2：下载项目

[下载代码示例](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

#### <a name="step-3-configure-the-application"></a>步骤 3：配置应用程序

1. 将 zip 文件提取到更靠近根文件夹的本地文件夹（例如，**C:\Azure-Samples**）
1. 如果使用集成开发环境，请在偏好的 IDE 中打开示例（可选）。
1. 打开 **app_config.py** 文件，该文件可以在根文件夹中找到，并替换为以下代码片段：

```python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

> [!div renderon="docs"]
> 其中：
>
> - `Enter_the_Application_Id_here` - 是已注册应用程序的应用程序 ID。
> - `Enter_the_Tenant_Info_Here` - 是下述选项之一：
>   - 如果应用程序支持“仅我的组织”，  请将该值替换为**租户 ID** 或**租户名称**（例如 contoso.onmicrosoft.com）
>   - 如果应用程序支持“任何组织目录中的帐户”，请将该值替换为  `organizations`
>   - 如果应用程序支持“所有 Microsoft 帐户用户”，请将该值替换为  `common`
> - `Enter_the_Client_Secret_Here` - 是你在“证书和机密”  中为注册的应用程序创建的**客户端密码**。

#### <a name="step-4-run-the-code-sample"></a>步骤 4：运行代码示例

- 需要安装 MSAL Python 库、Flask 框架、Flask-Sessions，以便使用 pip 进行服务器端会话管理和请求，如下所示：

```Shell
pip install msal
pip install flask
pip install Flask-Session
pip install requests
```

- 如果已设置 Flask 的环境变量：从 shell 或命令行运行 app.py：

```Shell
python app.py
```

- 如果未设置 Flask 的环境变量：

    1. 通过导航到项目目录，在 shell 或命令行中键入以下命令：

```Shell
export FLASK_APP=app.py
export FLASK_DEBUG=1
flask run
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
