---
title: 教程：在 Azure Static Web Apps 中使用 Blazor 生成静态 Web 应用
description: 了解如何使用 Blazor 生成 Azure Static Web Apps 网站。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 60e62228e33d2d86bb407e45802f5c0621a94049
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761084"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>教程：在 Azure Static Web Apps 中使用 Blazor 生成静态 Web 应用

Azure Static Web Apps 通过从 GitHub 存储库生成应用来将网站发布到生产环境。 在此教程中，使用 Azure 门户将 Web 应用程序部署到 Azure Static Web Apps。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

- [GitHub](https://github.com) 帐户
- [Azure](https://portal.azure.com) 帐户

## <a name="application-overview"></a>应用概览

通过 Azure Static Web Apps，你可以创建无服务器后端支持的静态 Web 应用程序。 以下教程演示了如何部署返回天气数据的 C# Blazor Web 应用程序。

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="完成 Blazor 应用":::

本教程中介绍的应用由三个不同的 Visual Studio 项目组成：

- Api：C# Azure Functions 应用程序，该应用程序实现向静态应用提供天气信息的 API 终结点。 [`WeatherForecastFunction`](https://github.com/ssdeepak/blazor/blob/main/Api/WeatherForecastFunction.cs) 返回 `WeatherForecast` 对象的数组。

- **客户端**：前端 Blazor Web 程序集项目。 已实现[回退路由](#fallback-route)，以确保为所有路由提供 index.html 文件。

- 共享：保存 Api 和客户端项目引用的公共类，这是数据可从 API 终结点流向前端 Web 应用。 [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) 类在两个应用之间共享。

这些项目共同构成了创建 Blazor Web 程序集应用程序的所需部件，该应用程序在 API 后端支持的浏览器中运行。

## <a name="fallback-route"></a>回退路由

应用程序公开诸如 /counter 和 /fetchdata 这样的 URL，它们映射到应用程序的特定路由 。 由于此应用是作为单页应用程序实现的，因此为所有路由提供了 index.html 文件。 为了确保对于任何路径的请求均返回 index.html，可在客户端项目的 wwwroot 文件夹中找到 routes.json 文件，在该文件中实现[回退路由](./routes.md#fallback-routes)  。

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

上述配置确保对应用中任何路由的请求都将返回 index.html 页。

## <a name="create-a-repository"></a>创建存储库

本文使用 GitHub 模板存储库，使你能够轻松入门。 该模板具有一个部署到 Azure Static Web Apps 的入门应用。

1. 请确保已登录到 GitHub，然后导航到以下位置以创建新存储库：
    - https://github.com/staticwebdev/blazor-starter/generate
1. 将存储库命名为 my-first-static-blazor-app

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

现在，已经创建了存储库，可以从 Azure 门户创建静态 Web 应用。

1. 导航到 [Azure 门户](https://portal.azure.com)
1. 选择“创建资源”
1. 搜索“静态 Web 应用”
1. 选择“Static Web Apps (预览)”
1. 选择“创建”

在“基本信息”部分中，首先配置新应用，并将其链接到 GitHub 存储库。

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="完成 Blazor 应用":::

1. 选择 Azure 订阅
1. 选择或创建新资源组
1. 将应用命名为 my-first-static-blazor-app
    - 有效字符为 `a-z`（不区分大小写）、`0-9` 和 `-`。
1. 选择离你最近的区域
1. 选择免费 SKU
1. 选择“使用 GitHub 登录”按钮，然后向 GitHub 进行身份验证

登录 GitHub 后，输入存储库信息。

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="完成 Blazor 应用":::

1. 选择首选组织
1. 选择“存储库”下拉列表中的“my-first-static-blazor-app”
1. 从“分支”下拉列表中选择“主分支”

    如果看不到任何存储库，则可能需要在 GitHub 中授权 Azure Static Web Apps。 浏览到 GitHub 存储库，转到“设置”>“应用程序”>“授权 OAuth 应用”，选择“Azure Static Web Apps”，然后选择“授予”。 对于组织存储库，你必须是组织的所有者才能授予权限。

1. 在“生成详细信息”部分中，添加特定于 Blazor 的配置详细信息。

    - 从“生成预设”下拉列表中选择“Blazor”，并保留所有默认值。

1. 选择“查看 + 创建”。

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="完成 Blazor 应用":::

1. 选择“创建”。

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="完成 Blazor 应用":::

1. 选择“转到资源”。

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="完成 Blazor 应用":::

## <a name="view-the-website"></a>查看网站

通过两个方面来部署静态应用。 第一个预配构成应用的基础 Azure 资源。 第二个是生成和发布应用程序的 GitHub Actions 工作流。

在导航到新静态站点之前，必须先完成部署生成的运行。

静态 Web 应用“概述”窗口显示了一系列链接，可帮助你与 Web 应用进行交互。

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="完成 Blazor 应用":::

1. 单击“单击此处以查看 GitHub Actions 运行的状态”的标题，即可转到针对存储库运行的 GitHub Actions。 确认部署作业完成后，即可通过生成的 URL 导航到网站。

2. GitHub Actions 工作流完成后，可以选择该 URL 链接以在新选项卡中打开网站。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可按以下步骤删除 Azure 静态 Web 应用实例：

1. 打开 [Azure 门户](https://portal.azure.com)
1. 在顶部搜索栏中搜索“my-first-static-blazor-app”
1. 选择应用名称
1. 选择“删除”按钮
1. 选择“是”以确认删除操作

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [身份验证和授权](./authentication-authorization.md)
