---
title: 教程 - 在 Azure Active Directory B2C 中自定义应用程序的用户界面 | Microsoft Docs
description: 了解如何使用 Azure 门户在 Azure Active Directory B2C 中自定义应用程序的用户界面。
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9c206ac7a13ea222a01cac78c447c0764f753517
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669344"
---
# <a name="tutorial-customize-the-user-interface-of-your-applications-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中自定义应用程序的用户界面

对于更常见的用户体验，例如注册、登录和配置文件编辑，可在 Azure Active Directory (Azure AD) B2C 中使用[内置策略](active-directory-b2c-reference-policies.md)。 本教程中的信息有助于了解如何使用自己的 HTML 和 CSS 文件[自定义用户界面 (UI)](customize-ui-overview.md)。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建 UI 自定义文件
> * 创建使用这些文件的注册和登录策略
> * 测试自定义 UI

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

如果尚未创建自己的 [Azure AD B2C 租户](tutorial-create-tenant.md)，请立即创建一个。 如果在上一教程中创建了租户，则可以使用现有租户。

## <a name="create-customization-files"></a>创建自定义文件

创建 Azure 存储帐户和容器，然后将基本 HTML 和 CSS 文件放在容器中。

### <a name="create-a-storage-account"></a>创建存储帐户

虽然可以通过多种方式存储文件，但在本教程中，可以将其存储在 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中。

1. 请确保使用的是包含 Azure 订阅的目录。 选择顶部菜单中的“目录和订阅筛选器”，然后选择包含订阅的目录。 此目录与包含 Azure B2C 租户的目录不同。

    ![切换到订阅目录](./media/tutorial-customize-ui/switch-directories.png)

2. 选择 Azure 门户左上角的“所有服务”，搜索并选择“存储帐户”。 
3. 选择 **添加** 。
4. 在“资源组”下，选择“新建”，输入新资源组的名称，然后单击“确定”。
5. 输入存储帐户的名称。 所选名称在 Azure 中需唯一，且必须为 3 到 24 个字符，并且只能包含数字和小写字母。
6. 选择存储帐户的位置或接受默认位置。 
7. 接受所有其他默认值，选择“查看 + 创建”，然后点击“创建”。
8. 创建存储帐户后，选择“转到资源”。

### <a name="create-a-container"></a>创建容器

1. 在存储帐户的概述页面上，选择“Blob”。
2. 选择“容器”，输入容器的名称，选择“Blob（仅限 Blob 的匿名读取访问）”，然后单击“确定”。

### <a name="enable-cors"></a>启用 CORS

 浏览器中的 Azure AD B2C 代码使用新式标准方法从策略中指定的 URL 加载自定义内容。 跨源资源共享 (CORS) 允许从其他域请求网页上的受限资源。

1. 在菜单中，选择“CORS”。
2. 对于“允许的来源”、“允许的标题”和“公开的标题”，请输入 `your-tenant-name.b2clogin.com`。 将 `your-tenant-name` 替换为 Azure AD B2C 租户的名称。 例如，`fabrikam.b2clogin.com`。
3. 对于“允许的谓词”，请同时选择 `GET` 和 `OPTIONS`。
4. 对于“最大期限”，请输入 200。

    ![启用 CORS](./media/tutorial-customize-ui/enable-cors.png)

5. 单击“ **保存**”。

### <a name="create-the-customization-files"></a>创建自定义文件

若要自定义注册体验的 UI，首先要创建一个简单的 HTML 和 CSS 文件。 可以根据需要配置 HTML，但其中必须具有“div”元素，其标识符为 `api`。 例如，`<div id="api"></div>`。 显示页面时，Azure AD B2C 会将元素注入 `api` 容器。

1. 在本地文件夹中，创建以下文件，并确保将 `your-storage-account` 更改为存储帐户的名称，并将 `your-container` 更改为所创建容器的名称。 例如，`https://store1.blob.core.windows.net/b2c/style.css`。

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>My B2C Application</title>
        <link rel="stylesheet" href="https://your-storage-account.blob.core.windows.net/your-container/style.css">
      </head>
      <body>  
        <h1>My B2C Application</h1>
        <div id="api"></div>
      </body>
    </html>
    ```

    可以根据需要设计页面，但是所创建的任何 HTML 自定义文件都需要“api”div 元素。 

3. 将文件另存为“custom-ui.html”。
4. 创建以下简单的 CSS，将所有元素集中在注册或登录页面上，包括 Azure AD B2C 注入的元素。

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center; 
    }
    .entry {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center; 
    }
    .create {
      width: 300px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

5. 将文件另存为“style.css”。

### <a name="upload-the-customization-files"></a>上传自定义文件

本教程会在存储帐户中存储所创建的文件，以便 Azure AD B2C 对其进行访问。

1. 选择 Azure 门户左上角的“所有服务”，搜索并选择“存储帐户”。
2. 选择创建的存储帐户，选择“Blob”，然后选择创建的容器。
3. 选择“上传”，导航到“custom-ui.html”文件并选择该文件，然后点击“上传”。

    ![上传自定义文件](./media/tutorial-customize-ui/upload-blob.png)

4. 复制所上传文件的 URL，以便稍后在本教程中使用。
5. 对“style.css”文件重复步骤 3 和 4。

## <a name="create-a-sign-up-and-sign-in-policy"></a>创建注册或登录策略

若要完成本教程中的步骤，需要在 Azure AD B2C 中创建测试应用程序和注册或登录策略。 可以将本教程中所述的原则应用于其他用户体验，例如配置文件编辑。

### <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

通过在租户中创建的应用程序与 Azure AD B2C 进行通信。 以下步骤创建一个应用程序，该应用程序可重定向返回到 [https://jwt.ms](https://jwt.ms) 的授权令牌。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是单击顶部菜单中的“目录和订阅筛选器”，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 选择“应用程序”，然后选择“添加”。
5. 输入应用程序的名称，例如 *testapp1*。
6. 对于“Web 应用/Web API”，请选择 `Yes`，然后为“回复 URL”输入 `https://jwt.ms`。
7. 单击“创建”。

### <a name="create-the-policy"></a>创建策略

若要测试自定义文件，请创建使用先前所创建应用程序的内置注册或登录策略。

1. 在 Azure AD B2C 租户中，选择“注册或登录策略”，然后单击“添加”。
2. 输入该策略的名称。 例如，“signup_signin”。 创建策略时，前缀“B2C_1”会自动添加到名称中。
3. 选择“标识提供者”，为本地帐户设置“电子邮件注册”，然后单击“确定”。
4. 选择“注册属性”，选择要在注册期间从客户收集的属性。 例如，设置“国家/地区”、“显示名称”和“邮政编码”，然后点击“确定”。
5. 选择“申请声明”，选择需要在授权令牌中返回的声明，该令牌在成功获得注册或登录体验后会发回到应用程序。 例如，选择“显示名称”、“标识提供者”、“邮政编码”、“用户是新用户”和“用户的对象 ID”，然后单击“确定”。
6. 选择“页面 UI 自定义”，选择“统一注册或登录页面”，然后点击“是”，查看“使用自定义页”。
7. 在“自定义页面 URI”中，输入之前记录的“custom-ui.html”文件的 URL，然后单击“确定”。
8. 单击“创建”。

## <a name="test-the-policy"></a>测试策略

1. 在 Azure AD B2C 租户中，选择“注册或登录策略”，然后选择所创建的策略。 例如，“B2C_1_signup_signin”。
2. 确保在“选择应用程序”中选择了所创建的应用程序，然后单击“立即运行”。

    ![运行注册或登录策略](./media/tutorial-customize-ui/signup-signin.png)

    应该会看到类似于以下示例的页面，其中元素基于所创建的 CSS 文件集中在一起：

    ![策略结果](./media/tutorial-customize-ui/run-now.png) 

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建 UI 自定义文件
> * 创建使用这些文件的注册和登录策略
> * 测试自定义 UI

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 中的语言自定义 ](active-directory-b2c-reference-language-customization.md)