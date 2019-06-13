---
title: 教程：创建 Azure 时序见解单页 Web 应用 | Microsoft Docs
description: 了解如何创建可以查询和呈现 Azure 时序见解环境中的数据的单页 Web 应用程序。
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 2f25267b95e9ed5f7d5f6e6373fb9e3807927a7f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735354"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>教程：创建 Azure 时序见解单页 Web 应用

本教程引导你完成创建自己的单页 Web 应用程序 (SPA) 以访问 Azure 时序见解数据的过程。 

本教程介绍：

> [!div class="checklist"]
> * 应用程序设计
> * 如何将应用程序注册到 Azure Active Directory (Azure AD)
> * 如何生成、发布和测试 Web 应用程序

> [!NOTE]
> * 本教程的源代码在 [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial) 上提供。
> * 提供了时序见解[客户端示例应用](https://insights.timeseries.azure.com/clientsample)以演示在本教程中使用的完整应用。

## <a name="prerequisites"></a>先决条件

* 注册一个[免费的 Azure 订阅](https://azure.microsoft.com/free/)（如果还没有）。

* Visual Studio 的免费副本。 下载 [2017 或 2019 Community 版本](https://www.visualstudio.com/downloads/)以开始使用。

* 适用于 Visual Studio 的 IIS Express、Web 部署和 Azure 云服务核心工具组件。 通过修改 Visual Studio 安装来添加组件。

## <a name="application-design"></a>应用程序设计

时序见解示例 SPA 是本教程中使用的设计和代码的基础。 代码使用时序见解 JavaScript 客户端库。 时序见解客户端库提供两个主要 API 类别的抽象：

- **用于调用时序见解查询 API 的包装器方法**：可用于通过基于 JSON 的表达式查询时序见解数据的 REST API。 这些方法组织在库的 TsiClient.server 命名空间下。

- **用于创建和填充多种图表控件的方法**：可以用于在网页中可视化时序见解数据的方法。 这些方法组织在库的 TsiClient.ux 命名空间下。

本教程还会使用示例应用程序时序见解环境中的数据。 有关时序见解示例应用程序的结构以及它如何使用时序见解客户端库的详细信息，请参阅教程[探索 Azure 时序见解 JavaScript 客户端库](tutorial-explore-js-client-lib.md)。

## <a name="register-the-application-with-azure-ad"></a>将应用程序注册到 Azure AD

生成应用程序之前，必须将它注册到 Azure AD。 注册可提供标识配置，以便应用程序可以使用 OAuth 支持进行单一登录。 OAuth 需要 SPA 才能使用隐式授权授予类型。 在应用程序清单中更新授权。 应用程序清单是以 JSON 格式表示应用程序的标识配置。

1. 使用 Azure 订阅帐户登录到 [Azure 门户](https://portal.azure.com)。  
1. 选择“Azure Active Directory” > “应用注册” > “新建应用程序注册”    。

   [![Azure 门户 - 开始 Azure AD 应用程序注册](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. 在“创建”窗格中，填写必需的参数。 

   参数|说明
   ---|---
   **名称** | 输入有意义的注册名称。  
   **应用程序类型** | 保留为“Web 应用/API”  。
   **登录 URL** | 输入应用程序登录页（主页）的 URL。 由于应用程序会在以后托管在 Azure 应用服务中，因此必须使用 https:\//azurewebsites.net 域中的 URL。 在本示例中，名称基于注册名称。

   选择“创建”  以创建新的应用程序注册。

   [![Azure 门户 - Azure AD 应用程序注册窗格中的“创建”选项](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. 资源应用程序提供其他应用程序可以使用的 REST API。 API 也会注册到 Azure AD。 API 通过公开作用域  提供对客户端应用程序的精细安全访问。 因为应用程序会调用 Azure 时序见解 API，所有必须指定 API 和作用域。 会在运行时为 API 和作用域授予权限。 选择“设置”   > “所需权限”   > “添加”  。

   [![Azure 门户 - 用于添加 Azure AD 权限的“添加”选项](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. 在“添加 API 访问权限”  窗格中，选择“1 选择 API”  以指定 Azure 时序见解 API。 在“选择 API”窗格的搜索框中，输入“azure 时”。   然后在结果列表中选择“Azure 时序见解”  。 选择“选择”。 

   [![Azure 门户 - 用于添加 Azure AD 权限的搜索选项](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. 若要为 API 选择作用域，请在“添加 API 访问权限”  窗格中，选择“2 Select 权限”  。 在“启用访问权限”窗格中，选择“访问 Azure 时序见解服务”作用域。   选择“选择”。  会返回到“添加 API 访问权限”  窗格。 选择“完成”  。

   [![Azure 门户 - 设置作用域以便添加 Azure AD 权限](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. 在“所需权限”  窗格中，现在会显示 Azure 时序见解 API。 还需要对所有用户提供预许可权限，以便应用程序可访问 API 和作用域。 选择“授予权限”，然后选择“是”   。

   [![Azure 门户 - 用于添加 Azure AD 所需权限的“授予权限”选项](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. 如前所述，还必须更新应用程序清单。 在窗格顶部的水平菜单（“痕迹导航”）中，选择应用程序名称以返回到“已注册的应用”  窗格。 选择“清单”，将 `oauth2AllowImplicitFlow` 属性更改为 `true`，然后选择“保存”。  

   [![Azure 门户 - 更新 Azure AD 清单](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. 在痕迹导航中，选择应用程序名称以返回到“已注册的应用”窗格。  复制应用程序的“主页”  和“应用程序 ID”  值。 本教程后面会用到这些属性。

   [![Azure 门户 - 复制应用程序的主页 URL 和应用程序 ID 值](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>生成并发布 Web 应用程序

1. 创建一个存储应用程序项目文件的目录。 然后访问以下每个 URL。 右键单击页面右上角的“原始”  链接，然后选择“另存为”  以将文件保存在项目目录中。

   - [index.html  ](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html)：页面的 HTML 和 JavaScript
   - [sampleStyles.css  ]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css)：CSS 样式表

   > [!NOTE]
   > 根据浏览器，可能需要在保存文件之前将文件扩展名更改为 .html 或 .css。

1. 验证是否在 Visual Studio 中安装了所需组件。 必须安装适用于 Visual Studio 的 IIS Express、Web 部署和 Azure 云服务核心工具组件。

    [![Visual Studio - 修改已安装的组件](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > 根据版本和配置设置，Visual Studio 体验可能与描述的示例略有不同。

1. 打开 Visual Studio 并登录。 若要为 web 应用程序创建项目，请在“文件”  菜单中，选择“打开”   > “网站”  。

    [![Visual Studio - 创建新解决方案](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. 在“打开网站”  窗格中，选择存储 HTML 和 CSS 文件的工作目录，然后选择“打开”  。

   [![Visual Studio - 包含“打开”和“网站”选项的“文件”菜单](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. 在 Visual Studio 的“视图”  菜单中，选择“解决方案资源管理器”  。 新解决方案随即打开。 它包含一个网站项目（地球图标），其中包含 HTML 和 CSS 文件。

   [![Visual Studio - 解决方案资源管理器中的新解决方案](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. 在发布应用之前，必须在 index.html  中更改配置设置。

   1. 对注释 `"PROD RESOURCE LINKS"` 下的三行取消注释，以将依赖项从 DEVELOPMENT 切换到 PRODUCTION。 注释掉注释 `"DEV RESOURCE LINKS"` 下的三行。

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      依赖项应如以下示例所示进行注释：

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. 若要配置应用以使用 Azure AD 应用注册 ID，请更改 `clientID` 和 `postLogoutRedirectUri` 值以使用在[将应用程序注册到 Azure AD](#register-the-application-with-azure-ad) 的步骤 9 中复制的“应用程序 ID”  和“主页”  值。

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      例如：

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. 完成修改后，保存 index.html  。

1. 在 Azure 订阅中将该 Web 应用程序作为 Azure 应用服务进行发布。  

   > [!NOTE]
   > 以下步骤中所示的屏幕截图中的多个选项会使用 Azure 订阅中的数据自动填充。 每个窗格可能需要几秒钟才能完全加载。  

   1. 在解决方案资源管理器中，右键单击网站项目节点，然后选择“发布 Web 应用”  。  

      [![Visual Studio - 选择解决方案资源管理器“发布 Web 应用”选项](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. 选择“开始”  以开始发布应用。

      [![Visual Studio - 发布配置文件窗格](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. 选择要用于发布应用程序的订阅。 选择“TsiSpaApp”  项目。 选择“确定”。 

      [![Visual Studio - 发布配置文件应用服务窗格](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. 选择“发布”  以部署 Web 应用程序。

      [![Visual Studio - “发布”选项和发布日志输出](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. 成功发布的日志会出现在 Visual Studio “输出”  窗格中。 部署完成后，Visual Studio 会在浏览器选项卡中打开 Web 应用程序，提示进行登录。 成功登录后，时序见解控件会使用数据进行填充。

## <a name="troubleshoot"></a>故障排除  

错误代码/条件 | 说明
---------------------| -----------
AADSTS50011：  未为应用程序注册回复地址。 | Azure AD 注册缺少“回复 URL”  属性。 转到 Azure AD 应用程序注册的“设置”   >   “回复 URL”。 验证在[将应用程序注册到 Azure AD](#register-the-application-with-azure-ad) 的步骤 3 中指定的登录  URL 是否存在。
AADSTS50011：*在请求中指定的回复 URL 与为以下应用程序配置的回复 URL 不匹配：'\<Application ID GUID>'。* | 在[生成并发布 Web 应用程序](#build-and-publish-the-web-application)的步骤 6 中指定的 `postLogoutRedirectUri` 必须与 Azure AD 应用程序注册中“设置”   >   “回复 URL”下指定的值匹配。 务必还要更改“目标 URL”  的值，以按照[生成并发布 Web 应用程序](#build-and-publish-the-web-application)的步骤 5 使用 https  。
Web 应用程序会加载，但登录页是未设置样式的纯文本页面，背景为白色。 | 验证在[生成并发布 Web 应用程序](#build-and-publish-the-web-application)的步骤 4 中论述的路径是否正确。 如果 Web 应用程序找不到 .css 文件，则页面无法正确设置样式。

## <a name="clean-up-resources"></a>清理资源

本教程创建了多个正常运行的 Azure 服务。 如果不打算完成此系列教程，建议删除所有资源，以免产生不必要的费用。

在 Azure 门户左侧菜单中：

1. 选择“资源组”，然后选择针对时序见解环境创建的资源组。  在页面顶部选择“删除资源组”，输入资源组的名称，然后选择“删除”。  
1. 选择“资源组”，然后选择设备模拟解决方案加速器创建的资源组。  在页面顶部选择“删除资源组”，输入资源组的名称，然后选择“删除”。  

## <a name="next-steps"></a>后续步骤

本教程介绍了以下内容：

> [!div class="checklist"]
> * 应用程序设计
> * 如何将应用程序注册到 Azure AD
> * 如何生成、发布和测试 Web 应用程序

本教程集成 Azure AD，并使用已登录用户的标识来获取访问令牌。 若要了解如何使用服务或守护程序应用程序的标识访问时序见解 API，请参阅以下文章：

> [!div class="nextstepaction"]
> [Azure 时序见解 API 的身份验证和授权](time-series-insights-authentication-and-authorization.md)
