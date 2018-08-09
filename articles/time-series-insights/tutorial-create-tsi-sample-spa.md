---
title: 创建 Azure 时序见解单页 Web 应用
description: 了解如何创建可以查询和呈现 TSI 环境中的数据的单页 Web 应用程序。
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.openlocfilehash: 312e15f976a6782e3f39cfcc5ce0721ac6357a16
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626749"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>教程：创建 Azure 时序见解单页 Web 应用

本教程逐步讲解如何根据 [Time Series Insights (TSI) sample application](https://insights.timeseries.azure.com/clientsample)（时序见解 (TSI) 示例应用程序）中的模型创建自己的单页 Web 应用程序 (SPA)，以便访问 TSI 数据。 本教程介绍：

> [!div class="checklist"]
> * 应用程序设计
> * 如何将应用程序注册到 Azure Active Directory (AD)
> * 如何生成、发布和测试 Web 应用程序 

## <a name="prerequisites"></a>先决条件

注册一个[免费的 Azure 订阅](https://azure.microsoft.com/free/)（如果还没有）。 

此外还需安装 Visual Studio（如果尚未安装）。 就本教程来说，可以[下载/安装免费社区版或免费试用版](https://www.visualstudio.com/downloads/)。

## <a name="application-design-overview"></a>应用程序设计概述

如前所述，TSI 示例应用程序是本教程中使用的设计和代码的基础。 代码使用 TSI 客户端 JavaScript 库。 TSI 客户端库提供两个主要 API 类别的抽象：

- **用于调用 TSI 查询 API 的包装器方法**：用于通过基于 JSON 的表达式查询 TSI 数据的 REST API。 这些方法组织在库的 `TsiClient.server` 命名空间下。
- **用于创建和填充多种图表控件的方法**：用于在网页中可视化 TSI 数据的方法。 这些方法组织在库的 `TsiClient.ux` 命名空间下。

本教程还会使用示例应用程序的 TSI 环境中的数据。 若要详细了解 TSI 示例应用程序的结构及其使用 TSI 客户端库的方式，请参阅[探索 Azure 时序见解 JavaScript 客户端库](tutorial-explore-js-client-lib.md)教程。

## <a name="register-the-application-with-azure-ad"></a>将应用程序注册到 Azure AD 

在生成应用程序之前，需将其注册到 Azure AD。 注册为应用程序提供标识配置，让其可以使用 OAuth 支持进行单一登录。 OAuth 要求 SPA 使用“隐式”授权，后者可以在应用程序清单中进行更新。 应用程序清单是以 JSON 格式表示应用程序的标识配置。 

1. 使用 Azure 订阅帐户登录到 [Azure 门户](https://portal.azure.com)。  
1. 在左窗格中依次选择“Azure Active Directory”资源、“应用注册”、“+ 新建应用程序注册”：  
   
   ![Azure 门户 Azure AD 应用程序注册](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. 在“创建”页中，填写必需的参数：
   
   参数|Description
   ---|---
   **Name** | 提供一个有意义的注册名称。  
   **应用程序类型** | 由于生成的是 SPA Web 应用程序，因此请将其保留为“Web 应用/API”。
   **登录 URL** | 输入应用程序的主页/登录页的 URL。 由于应用程序会在以后托管在 Azure 应用服务中，因此必须使用“https://azurewebsites.net”域中的 URL。 在本示例中，名称基于注册名称。

   完成后请单击“创建”，创建新的应用程序注册。

   ![Azure 门户 Azure AD 应用程序注册 - 创建](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. 资源应用程序会提供可供其他应用程序使用的 REST API，并且还会注册到 Azure AD。 API 通过公开“作用域”提供对客户端应用程序的精细/安全的访问。 由于应用程序会调用“Azure 时序见解”API，因此需指定 API 和作用域，以便在运行时为其请求/授予权限。 依次选择“设置”、“所需权限”、“+ 添加”：

   ![Azure 门户 Azure AD 添加权限](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. 在“添加 API 访问权限”页中单击“1 选择 API”，以便指定 TSI API。 在“选择 API”页的搜索字段中输入“azure 时”。 然后在结果列表中选择“Azure 时序见解”API，再单击“选择”： 

   ![Azure 门户 Azure AD 添加权限 - API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. 现在请在 API 上指定作用域。 回到“添加 API 访问权限”页，单击“2 选择权限”。 在“启用访问权限”页上，选择“访问 Azure 时序见解服务”作用域。 单击“选择”，返回到“添加 API 访问权限”页，在其中单击“完成”：

   ![Azure 门户 Azure AD 添加权限 - 作用域](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. 返回到“所需权限”页后，会发现“Azure 时序见解”API 现在已列出。 此外还需为所有用户预许可应用程序访问 API 和作用域的权限。 单击顶部的“授予权限”按钮，然后选择“是”：

   ![Azure 门户 Azure AD 所需权限 - 许可](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. 如前所述，还需要更新应用程序清单。 单击痕迹导航中的应用程序名称，返回到“已注册应用”页。 选择“清单”，将 `oauth2AllowImplicitFlow` 属性更改为 `true`，然后单击“保存”：

   ![Azure 门户 Azure AD 更新清单](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. 最后，单击痕迹导航再次返回到“已注册应用”页，复制应用程序的**主页** URL 和“应用程序 ID”属性。 将在后面的一个步骤中使用这些属性：

   ![Azure 门户 Azure AD 属性](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>生成并发布 Web 应用程序

1. 创建一个存储应用程序项目文件的目录。 然后浏览到下面的每个 URL，右键单击页面右上区域的“原始”链接，将文件以“另存为”方式保存到项目目录中：

   > [!NOTE]
   > 可能需要在保存文件之前更正文件扩展名（更正为 HTML 或 CSS），具体取决于浏览器。

   - **index.html** HTML 和 JavaScript 适合页面 https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css：** CSS 样式表：https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
1. 启动 Visual Studio 并登录到其中，以便创建适合 Web 应用程序的项目。 在“文件”菜单上，选择“打开”>“网站”选项。 在“打开网站”对话框中，选择存储了 HTML 和 CSS 文件的工作目录，然后单击“打开”：

   ![VS - 文件打开网站](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. 在 Visual Studio 的“视图”菜单中打开“解决方案资源管理器”。 此时会看到新的解决方案，其中包含一个网站项目（地球图标），该项目包含 HTML 和 CSS 文件：

   ![VS - 解决方案资源管理器新建解决方案](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. 发布应用程序之前，需在 **index.html** 中更新 JavaScript 代码的多个部分： 

   a. 首先，在 `<head>` 元素中更改 JavaScript 和样式表文件引用的路径。 打开 Visual Studio 解决方案中的 **index.html** 文件，找到以下 JavaScript 代码行。 取消注释“PROD RESOURCE LINKS”下的三行，注释掉“DEV RESOURCE LINKS”下的三行：
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      更改后的代码应如以下示例所示：

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. 接下来，更改访问令牌逻辑，使用新的 Azure AD 应用程序注册。 分别更改下面的 `clientID` 和 `postLogoutRedirectUri` 变量，使用从[将应用程序注册到 Azure AD](#register-the-application-with-azure-ad)部分的步骤 9 复制的“应用程序 ID”和“主页 URL”：

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      更改后的代码应如以下示例所示：

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. 编辑完以后，保存 **index.html**。

1. 现在请将 Web 应用程序作为 Azure 应用服务发布到 Azure 订阅中：  

   > [!NOTE]
   > 以下对话框中的多个字段填充的是 Azure 订阅中的数据。 因此，在继续操作之前，可能需要等待数秒钟，确保每个对话框加载完毕。  

   a. 右键单击“解决方案资源管理器”中的网站项目节点，然后选择“发布 Web 应用”：  

      ![VS - 解决方案资源管理器的“发布 Web 应用”](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. 选择“Microsoft Azure 应用服务”，以便创建发布目标：  

      ![VS - 发布配置文件](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. 选择要用于发布应用程序的订阅，然后单击“新建”： 

      ![VS - 副本配置文件 - 应用服务](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. 给“创建应用服务”对话框几秒钟时间来加载所有字段，然后修改以下字段：
   
      字段 | Description
      ---|---
      **应用名称** | 更改为在[将应用程序注册到 Azure AD](#register-the-application-with-azure-ad) 的步骤 3 中使用的 Azure AD 应用程序注册名称。 
      **资源组** | 使用“新建...”按钮，通过更改来匹配“应用名称”字段。
      应用服务计划 | 使用“新建...”按钮，通过更改来匹配“应用名称”字段。

      完成后，单击“创建”。 在创建 Azure 资源期间（数秒钟），左下角的“导出”按钮会被替换为“正在部署:”。 创建资源以后，将会返回到上一对话框。 

      ![VS - 副本配置文件 - 添加新的应用服务](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. 返回到“发布”对话框以后，请确保“发布方法”已设置为“Web 部署”。 另请更改“目标 URL”，使用 `https` 而不是 `http`，以便与 Azure AD 应用程序注册匹配。 然后单击“发布”，以部署 Web 应用程序：  

      ![VS - 发布 Web 应用 - 发布应用服务](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. 应该会在 Visual Studio 的“输出”窗口中看到记录成功发布的日志。 部署完成以后，Visual Studio 还会在浏览器标签页中打开 Web 应用程序，提示你登录。 成功登录以后，会看到填充了数据的所有 TSI 控件：  

      [![VS - 发布 Web 应用 - 发布日志输出](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![TSI SPA 应用 - 登录](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>故障排除  

错误代码/条件 | Description
---------------------| -----------
AADSTS50011：未为应用程序注册回复地址。 | Azure AD 注册缺少“回复 URL”属性。 转到 Azure AD 应用程序注册的“设置” / “回复 URL”页。 验证在[将应用程序注册到 Azure AD](#register-the-application-with-azure-ad) 的步骤 3 中指定的**登录** URL 是否存在。 
AADSTS50011：在请求中指定的回复 URL 与为应用程序“<Application ID GUID>”配置的回复 URL 不匹配。 | 在[生成并发布 Web 应用程序](#build-and-publish-the-web-application)的步骤 4.b 中指定的 `postLogoutRedirectUri` 必须与 Azure AD 应用程序注册中“设置” / “回复 URL”属性下指定的值匹配。 另外，请确保将“目标 URL”更改为使用 `https`，详见 [生成并发布 Web 应用程序](#build-and-publish-the-web-application)的步骤 5.e 中的说明。
Web 应用程序会加载，但登录页是未设置样式的纯文本页面，背景为白色。 | 验证在[生成并发布 Web 应用程序](#build-and-publish-the-web-application)的步骤 4.a 中论述的路径是否正确。 如果 Web 应用程序找不到 .css 文件，则页面无法正确设置样式。

## <a name="clean-up-resources"></a>清理资源

本教程创建了多个正常运行的 Azure 服务。 如果不打算完成此系列教程，建议删除所有资源，以免产生不必要的费用。 

在 Azure 门户的左侧菜单中：

1. 单击“资源组”图标，然后选择针对 TSI 环境创建的资源组。 在页面顶部单击“删除资源组”，输入资源组的名称，然后单击“删除”。 
1. 单击“资源组”图标，然后选择设备模拟解决方案加速器创建的资源组。 在页面顶部单击“删除资源组”，输入资源组的名称，然后单击“删除”。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 应用程序设计
> * 如何将应用程序注册到 Azure Active Directory (AD)
> * 如何生成、发布和测试 Web 应用程序 

本教程集成 Azure AD，使用已登录用户的标识来获取访问令牌。 若要了解如何使用服务/守护程序的标识来访问 TSI API，请参阅：

> [!div class="nextstepaction"]
> [Azure 时序见解 API 的身份验证和授权](time-series-insights-authentication-and-authorization.md)
