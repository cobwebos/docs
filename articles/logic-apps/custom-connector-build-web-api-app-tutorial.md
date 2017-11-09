---
title: "从 Web API 创建自定义连接器 - Azure 逻辑应用 | Microsoft Docs"
description: "从 Web API 生成自定义连接器"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>从 Web API 创建自定义连接器

若要生成可在 Azure 逻辑应用、Microsoft Flow 或 Microsoft PowerApps 中使用的自定义连接器，请先创建一个可以使用 Azure Web 应用托管的 Web API，使用 Azure Active Directory 对其进行身份验证，并将其作为连接器注册到逻辑应用、Flow 或 PowerApps。 本教程介绍如何通过生成 ASP.NET Web API 应用执行这些任务。 有关演示如何为连接器的触发器和操作构建代码的模式，请参阅[创建可从逻辑应用工作流调用的自定义 API](../logic-apps/logic-apps-create-api-app.md)。

## <a name="prerequisites"></a>先决条件

* [Visual Studio 2013 或更高版本](https://www.visualstudio.com/vs/)。 本教程使用 Visual Studio 2015。

* Web API 的代码。 如果没有此代码，请参阅教程 [ASP.NET Web API 2 (C#) 入门](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)。

* Azure 订阅。 如果没有订阅，可以从[免费的 Azure 帐户](https://azure.microsoft.com/free/)着手。 或者，也可注册[即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>创建 ASP.NET Web 应用并将其部署到 Azure

本教程将创建 Visual C# ASP.NET Web 应用程序。 

1. 打开 Visual Studio，选择“文件” > “新建项目”。

   1. 展开“已安装”，转到“模板”>“Visual C#”>“Web”，选择“ASP.NET Web 应用程序”。

   2. 提供应用的项目名称、位置和解决方案，选择“确定”。

   例如：

   ![创建 Visual C# ASP.NET Web 应用程序](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. 在“新建 ASP.NET Web 应用程序”框中，选择“Web API”模板。 如果尚未选择，请选择“在云中托管”。 选择“更改身份验证”。

   ![依次选择“Web API”模板、“在云中托管”、“更改身份验证”](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. 依次选择“无身份验证”、“确定”。 稍后可以设置身份验证。

   ![选择“无身份验证”](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. 再次出现“新建 ASP.NET Web 应用程序”框时，选择“确定”。 

5. 在“创建应用服务”框中，查看表中所述的托管设置，进行所需的更改，选择“创建”。 

   [应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)代表用于在 Azure 订阅中托管应用的物理资源集合。 了解[应用服务](../app-service/app-service-value-prop-what-is.md)。

   ![创建应用服务](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | Azure 工作或学校帐户，或 Microsoft 个人帐户 | *your-user-account* | 选择用户帐户。 | 
   | **Web 应用名称** | *custom-web-api-app-name* 或默认名称 | 输入要在应用的 URL 中使用的 Web API 应用名称，例如 http://*web-api-app-name*。 | 
   | **订阅** | *Azure-subscription-name* | 选择要使用的 Azure 订阅。 | 
   | **资源组** | *Azure-resource-group-name* | 选择现有的 Azure 资源组，如果没有，请创建一个资源组。 <p>**注意**：Azure 资源组可在 Azure 订阅中组织 Azure 资源。 | 
   | 应用服务计划 | *App-Service-plan-name* | 选择现有的应用服务计划，如果没有，请创建一个计划。 | 
   |||| 

   若要创建应用服务计划，请指定以下设置：

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **位置** | *deployment-region* | 选择要部署该应用的区域。 | 
   | **大小** | *App-Service-plan-size* | 选择计划大小，该大小确定服务计划的成本和计算资源容量。 | 
   |||| 

   若要设置应用所需的其他任何资源，请选择“浏览其他 Azure 服务”。

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **资源类型** | *Azure-resource-type* | 选择并设置应用所需的其他任何资源。 | 
   |||| 

6. Visual Studio 部署项目后，请生成应用的代码。

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>创建用于描述 Web API 的 OpenAPI (Swagger) 文件

若要将 Web API 应用连接到逻辑应用，需要一个用于描述 API 操作的 [OpenAPI（以前称为 Swagger）文件](http://swagger.io/)。 可以使用 [Swagger 在线编辑器](http://editor.swagger.io/)为 API 编写自己的 OpenAPI 定义，但本教程使用名为 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md) 的开源工具。

1. 在 Visual Studio 项目中安装 Swashbuckle Nuget 包（如果尚未这样做）。

   1. 在 Visual Studio 中，选择“工具”>“NuGet 包管理器”> 
   “包管理器控制台”。************

   2. 在“包管理器控制台”中，如果尚未进入应用的项目目录，请转到该位置（运行 `Set-Location "project-path"`），并运行以下 PowerShell cmdlet： 
   
      `Install-Package Swashbuckle`

      例如：

      ![在包管理器控制台中安装 Swashbuckle](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > 如果在安装 Swashbuckle 后运行应用，Swashbuckle 会在以下 URL 处生成 OpenAPI 文件： 
   >
   > http://*{Web API 应用根 URL}*/swagger/docs/v1
   > 
   > Swashbuckle 还会在以下 URL 处生成用户界面： 
   > 
   > http://*{Web API 应用根 URL}*/swagger

3. 准备就绪后，请将 Web API 应用发布到 Azure。 若要从 Visual Studio 发布，请在解决方案资源管理器中右键单击 Web 项目，选择“发布...”，并遵照提示操作。

   > [!IMPORTANT]
   > 重复的操作 ID 会使 OpenAPI 文档失效。 如果使用了示例 C# 模板，该模板会重复此操作 ID 两次：`Values_Get` 
   > 
   > 若要解决此问题，请将一个实例更改为 `Value_Get` 并重新发布。

4. 浏览到以下位置来获取 OpenAPI 文档： 

   http://*{Web API 应用根 URL}*/swagger/docs/v1

   也可以从此教程中下载[示例 OpenAPI 文档](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json)。 
   使用该文档之前，请务必删除以“//”开头的注释。

5. 将内容保存为 JSON 文件。 根据所用的浏览器，可能需要将文本复制并粘贴到空文本文件中。

## <a name="next-steps"></a>后续步骤

* [为自定义连接器设置身份验证](../logic-apps/custom-connector-azure-active-directory-authentication.md)











