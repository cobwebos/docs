---
title: 教程：自定义用户界面
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure 门户在 Azure Active Directory B2C 中自定义应用程序的用户界面 (UI)。
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e023e9c8c4c6f0021eabccad8783c27eba98d0d5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116520"
---
# <a name="tutorial-customize-the-interface-of-user-experiences-in-azure-active-directory-b2c"></a>教程：在 Azure Active Directory B2C 中自定义用户界面体验

对于更常见的用户体验，例如注册、登录和配置文件编辑，可在 Azure Active Directory B2C (Azure AD B2C) 中使用[用户流](user-flow-overview.md)。 本教程中的信息有助于了解如何使用自己的 HTML 和 CSS 文件[自定义用户界面 (UI)](customize-ui-overview.md)。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建 UI 自定义文件
> * 更新用户流以使用文件
> * 测试自定义 UI

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

[创建用户流](tutorial-create-user-flows.md)，以便用户能够注册并登录应用程序。

## <a name="create-customization-files"></a>创建自定义文件

创建 Azure 存储帐户和容器，然后将基本 HTML 和 CSS 文件放在容器中。

### <a name="create-a-storage-account"></a>创建存储帐户

虽然可以通过多种方式存储文件，但在本教程中，可以将其存储在 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 请确保使用的是包含 Azure 订阅的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含订阅的目录。 此目录与包含 Azure B2C 租户的目录不同。
3. 选择 Azure 门户左上角的“所有服务”，搜索并选择“存储帐户”。
4. 选择 **添加** 。
5. 在“资源组”下，选择“新建”，输入新资源组的名称，然后单击“确定”  。
6. 输入存储帐户的名称。 所选名称在 Azure 中需唯一，且必须为 3 到 24 个字符，并且只能包含数字和小写字母。
7. 选择存储帐户的位置或接受默认位置。
8. 接受所有其他默认值，选择“查看 + 创建”，然后点击“创建” 。
9. 创建存储帐户后，选择“转到资源”。

### <a name="create-a-container"></a>创建容器

1. 在存储帐户的概述页面上，选择“Blob”。
2. 选择“容器”，输入容器的名称，选择“Blob（仅限 Blob 的匿名读取访问）”，然后单击“确定”  。

### <a name="enable-cors"></a>启用 CORS

 浏览器中的 Azure AD B2C 代码使用新式标准方法从用户流中指定的 URL 加载自定义内容。 跨源资源共享 (CORS) 允许从其他域请求网页上的受限资源。

1. 在菜单中，选择“CORS”。
2. 对于“允许的源”，请输入 `https://your-tenant-name.b2clogin.com`。 将 `your-tenant-name` 替换为 Azure AD B2C 租户的名称。 例如，`https://fabrikam.b2clogin.com` 。 输入租户名称时，需要使用全小写字母。
3. 对于“允许的方法”，请选择 `GET`、`PUT` 和 `OPTIONS`。
4. 对于“允许的标头”，请输入一个星号 (*)。
5. 对于“公开的标头”，请输入一个星号 (*)。
6. 对于“最大期限”，请输入 200。

    ![Azure 门户的 Azure Blob 存储中的“CORS 配置”页](./media/tutorial-customize-ui/enable-cors.png)

5. 单击“ **保存**”。

### <a name="create-the-customization-files"></a>创建自定义文件

若要自定义注册体验的 UI，首先要创建一个简单的 HTML 和 CSS 文件。 可以根据需要配置 HTML，但其中必须具有“div”元素，其标识符为 `api`。 例如，`<div id="api"></div>` 。 显示页面时，Azure AD B2C 会将元素注入 `api` 容器。

1. 在本地文件夹中，创建以下文件，并确保将 `your-storage-account` 更改为存储帐户的名称，并将 `your-container` 更改为所创建容器的名称。 例如，`https://store1.blob.core.windows.net/b2c/style.css` 。

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

1. 选择 Azure 门户左上角的“所有服务”，搜索并选择“存储帐户” 。
2. 选择创建的存储帐户，选择“Blob”，然后选择创建的容器。
3. 选择“上传”，导航到“custom-ui.html”文件并选择该文件，然后点击“上传”。

    ![门户中的“上传 blob”页，其中突出显示了“上传”按钮和“文件”](./media/tutorial-customize-ui/upload-blob.png)

4. 复制所上传文件的 URL，以便稍后在本教程中使用。
5. 对“style.css”文件重复步骤 3 和 4。

## <a name="update-the-user-flow"></a>更新用户流

1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C” 。
2. 依次选择“用户流(策略)”和“B2C_1_signupsignin1”用户流。
3. 选择“页面布局”，然后在“统一注册或登录页面”下，对“使用自定义页面内容”单击“是”   。
4. 在“自定义页面 URI”中，输入之前记录的“custom-ui.html”文件的 URI。
5. 在页面顶部，选择“保存”。

## <a name="test-the-user-flow"></a>测试用户流

1. 在 Azure AD B2C 租户中选择“用户流”，然后选择“B2C_1_signupsignin1”用户流。
2. 在该页顶部，单击“运行用户流”。
3. 单击“运行用户流”按钮。

    ![注册或登录用户流的“运行用户流”页](./media/tutorial-customize-ui/run-user-flow.png)

    应该会看到类似于以下示例的页面，其中元素基于所创建的 CSS 文件集中在一起：

    ![Web 浏览器，显示带有自定义 UI 元素的注册或登录页](./media/tutorial-customize-ui/run-now.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建 UI 自定义文件
> * 更新用户流以使用文件
> * 测试自定义 UI

> [!div class="nextstepaction"]
> [在 Azure Active Directory B2C 中自定义 UI](customize-ui-overview.md)
