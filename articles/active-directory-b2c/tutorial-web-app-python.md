---
title: 教程：在 Python Web 应用程序中启用身份验证
titleSuffix: Azure AD B2C
description: 在本教程中，了解如何使用 Azure Active Directory B2C 为 Python Flask Web 应用程序提供用户登录功能。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 6a113b3a3df475853f3690a0e932378bc0e2ab02
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844620"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>教程：使用 Azure AD B2C 在 Python Web 应用程序中启用身份验证

本教程介绍如何使用 Azure Active Directory B2C (Azure AD B2C) 在 Python Flask Web 应用程序中进行用户登录和注册。

本教程的内容：

> [!div class="checklist"]
> * 将回复 URL 添加到在 Azure AD B2C 租户中注册的应用程序
> * 从 GitHub 下载代码示例
> * 修改示例应用程序的代码，使其适用于你的租户
> * 使用注册/登录用户流进行注册

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

在继续执行本教程中的步骤以前，需将以下 Azure AD B2C 资源准备到位：

* [Azure AD B2C 租户](tutorial-create-tenant.md)
* 在租户中[注册的应用程序](tutorial-register-applications.md)，及其应用程序（客户端）ID 和客户端密码
* 在租户中[创建的用户流](tutorial-create-user-flows.md)

另外，需要在本地开发环境中备好以下项：

* [Visual Studio Code](https://code.visualstudio.com/) 或其他代码编辑器
* [Python](https://nodejs.org/en/download/) 2.7+ 或 3+

## <a name="add-a-redirect-uri"></a>添加重定向 URI

在按照先决条件完成的第二个教程中，你已在 Azure AD B2C 中注册了 Web 应用程序。 若要使用本教程中的代码示例实现通信，请将一个回复 URL（也称为重定向 URI）添加到应用程序注册。

要更新 Azure AD B2C 租户中的应用程序，可以使用新的统一“应用注册”体验或旧版“应用程序(旧版)”体验 。 [详细了解此新体验](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[应用注册](#tab/app-reg-ga/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 依次选择“应用注册(预览版)”、“拥有的应用程序”选项卡，然后选择“webapp1”应用程序 。
1. 在“管理”下，选择“身份验证”。 
1. 在“Web”下，选择“添加 URI”链接，然后在文本框中输入 `http://localhost:5000/getAToken` 。
1. 选择“保存”。

#### <a name="applications-legacy"></a>[应用程序(旧版)](#tab/applications-legacy/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
1. 选择“应用程序(旧版)”，然后选择“webapp1”应用程序。
1. 在“回复 URL”下添加 `http://localhost:5000/getAToken`。
1. 选择“保存”。
* * *

## <a name="get-the-sample-code"></a>获取示例代码

在本教程中，你将配置从 GitHub 下载的代码示例，以其适用于你的 B2C 租户。 该示例演示 Python Flask Web 应用程序如何使用 Azure AD B2C 执行用户注册和登录。

从 GitHub [下载 .ZIP 存档](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)或[克隆代码示例存储库](https://github.com/Azure-Samples/ms-identity-python-webapp)。

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>更新示例

获取示例后，将应用程序配置为使用 Azure AD B2C 租户、应用程序注册和用户流。

在项目的根目录中：

1. 将 app_config.py 文件重命名为 app_config.py.OLD
1. 将 app_config_b2c.py 重命名为 app_config.py

使用作为先决条件一部分创建的 Azure AD B2C 租户和应用程序注册的值更新新重命名的 app_config.py。

1. 在编辑器中打开 app_config.py 文件。
1. 使用 Azure AD B2C 租户的名称（例如 contosob2c）更新 `b2c_tenant` 值。
1. 更新每个 `*_user_flow` 值，以便与作为先决条件一部分创建的用户流的名称相匹配。
1. 使用作为先决条件一部分注册的 Web 应用程序的应用程序（客户端）ID 更新`CLIENT_ID` 值。
1. 使用在先决条件中创建的客户端密码的值更新 `CLIENT_SECRET` 值。 为了提高安全性，请考虑改为将它存储在注释中所建议的环境变量中。

app_config.py 的开头部分现在应类似于以下代码片段：

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> 如代码片段注释中所述，建议不要在应用程序代码中以明文存储机密。 在代码示例中使用硬编码变量只是为了方便起见。 请考虑使用环境变量或机密存储，如 Azure Key Vault。

## <a name="run-the-sample"></a>运行示例

1. 在控制台或终端中，切换到包含示例的目录。 例如：

    ```console
    cd ms-identity-python-webapp
    ```
1. 运行以下命令，从 PyPi 安装所需包，并在本地计算机上运行 Web 应用：

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    控制台窗口显示本地运行的应用程序的端口号：

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. 浏览到 `http://localhost:5000`，查看在本地计算机上运行的 Web 应用程序。

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="显示了本地运行的 Python Flask Web 应用程序的 Web 浏览器":::

### <a name="sign-up-using-an-email-address"></a>使用电子邮件地址注册

此示例应用程序支持注册、登录和密码重置。 在本教程中，你将使用电子邮件地址进行注册。

1. 选择“登录”以启动在前面步骤中指定的 B2C_1_signupsignin1 用户流。
1. Azure AD B2C 会显示一个包含注册链接的登录页。 由于你还没有帐户，因此请选择“立即注册”链接。
1. 注册工作流会显示一个页面，用于收集用户的标识并通过电子邮件地址对其进行验证。 注册工作流还会收集用户的密码和所请求的属性（在用户流中定义）。

    请使用有效的电子邮件地址，并使用验证码进行验证。 设置密码。 输入请求的属性的值。

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Azure AD B2C 用户流显示的注册页":::

1. 选择“创建”，在 Azure AD B2C 目录中创建本地帐户。

选择“创建”时，应用程序会显示已登录用户的名称。

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="显示包含已登录用户的 Python Flask Web 应用程序的 Web 浏览器":::

若要测试登录，请选择“注销”链接，然后选择“登录”，并使用注册时输入的电子邮件地址和密码进行登录。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已将某个 Python Flask Web 应用程序配置为使用 Azure AD B2C 租户中的用户流来提供注册和登录功能。 你已完成以下步骤：

> [!div class="checklist"]
> * 将回复 URL 添加到了在 Azure AD B2C 租户中注册的应用程序
> * 从 GitHub 下载了代码示例
> * 修改了示例应用程序的代码，使其适用于你的租户
> * 使用注册/登录用户流完成了注册

接下来，了解如何通过 Azure AD B2C 自定义向用户显示的用户流页面的 UI：

> [!div class="nextstepaction"]
> [教程：在 Azure AD B2C 中自定义用户界面体验 >](tutorial-customize-ui.md)
