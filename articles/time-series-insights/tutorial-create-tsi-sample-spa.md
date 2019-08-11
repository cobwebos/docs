---
title: 教程：创建 Azure 时序见解单页 Web 应用 | Microsoft Docs
description: 了解如何创建可以查询和呈现 Azure 时序见解环境中的数据的单页 Web 应用程序。
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 4d9af918c222107cfca5863309efb391b8e6d2e0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720866"
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

注册一个[免费的 Azure 订阅](https://azure.microsoft.com/free/)（如果还没有）。

## <a name="prerequisites"></a>先决条件

* Visual Studio 的免费副本。 下载 [2017 或 2019 Community 版本](https://www.visualstudio.com/downloads/)以开始使用。

* 适用于 Visual Studio 的 IIS Express、Web 部署和 Azure 云服务核心工具组件。 通过修改 Visual Studio 安装来添加组件。

## <a name="understand-application-design"></a>了解应用程序设计

时序见解示例 SPA 是本教程中使用的设计和代码的基础。 代码使用时序见解 JavaScript 客户端库。 时序见解客户端库提供两个主要 API 类别的抽象：

- **用于调用时序见解查询 API 的包装器方法**：可用于通过基于 JSON 的表达式查询时序见解数据的 REST API。 这些方法组织在库的 TsiClient.server 命名空间下。

- **用于创建和填充多种图表控件的方法**：可以用于在网页中可视化时序见解数据的方法。 这些方法组织在库的 TsiClient.ux 命名空间下。

本教程还会使用示例应用程序时序见解环境中的数据。 有关时序见解示例应用程序的结构以及它如何使用时序见解客户端库的详细信息，请参阅教程[探索 Azure 时序见解 JavaScript 客户端库](tutorial-explore-js-client-lib.md)。

## <a name="register-with-azure-ad"></a>注册到 Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>生成并发布

1. 创建一个存储应用程序项目文件的目录。 然后访问以下每个 URL。 右键单击页面右上角的“原始”  链接，然后选择“另存为”  以将文件保存在项目目录中。

   - [index.html  ](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html)：用于页面的 HTML 和 JavaScript
   - [sampleStyles.css](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css)：CSS 样式表 

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

   1. 若要配置应用以使用 Azure AD 应用注册 ID，请更改 `clientID` 值以使用[注册应用程序以使用 Azure AD](#register-with-azure-ad) 时在**步骤 3** 中复制的**应用程序 ID**。 如果已在 Azure AD 中创建了**注销 URL**，请将该值设置为 `postLogoutRedirectUri` 值。

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
AADSTS50011：  未为应用程序注册回复地址。 | Azure AD 注册缺少“回复 URL”  属性。 转到 Azure AD 应用程序注册的“设置”   >   “回复 URL”。 验证在[注册应用程序以使用 Azure AD](#register-with-azure-ad) 时，可以选择在**步骤 2** 或**步骤 4** 中指定的**重定向 URI** 是否存在。
AADSTS50011：*在请求中指定的回复 URL 与为以下应用程序配置的回复 URL 不匹配：'\<Application ID GUID>'。* | 在[生成并发布 Web 应用程序](#build-and-publish)的**步骤 6.b** 中指定的 `postLogoutRedirectUri` 必须与 Azure AD 应用程序注册中“设置”   > “回复 URL”  下指定的值匹配。 |
Web 应用程序会加载，但登录页是未设置样式的纯文本页面，背景为白色。 | 验证在[生成并发布 Web 应用程序](#build-and-publish)的**步骤 6** 中论述的路径是否正确。 如果 Web 应用程序找不到 .css 文件，则页面无法正确设置样式。

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
