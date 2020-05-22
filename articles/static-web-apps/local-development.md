---
title: 为 Azure 静态 Web 应用设置本地开发
description: 了解如何为 Azure 静态 Web 应用设置本地开发环境
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 36d580b7659325d4bf5f13889f774ddaa2ab0702
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594246"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>为 Azure 静态 Web 应用预览设置本地开发

Azure 静态 Web 应用实例由两种不同类型的应用程序组成。 第一种是用于静态内容的 Web 应用。 Web 应用通常是使用前端框架和库或静态站点生成器创建的。 第二种是 API，这是一个提供丰富的后端开发环境的 Azure Functions 应用。

在云中运行时，Azure 静态 Web 应用无需 CORS 配置即可将请求无缝映射到从 Web 应用到 Azure Functions 应用的 `api` 路由。 在本地，需要将应用程序配置为模拟此行为。

本文说明了建议用于本地开发的最佳做法，包括以下概念：

- 为静态内容设置 Web 应用
- 为应用程序的 API 配置 Azure Functions 应用
- 调试并运行应用程序
- 应用的文件和文件夹结构的最佳做法

## <a name="prerequisites"></a>先决条件

- [Visual Studio Code](https://code.visualstudio.com/)
- 适用于 Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- 适用于 Visual Studio Code 的 [Live Server 扩展](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
  - 仅当不使用前端 JavaScript 框架或静态站点生成器的 CLI 时才需要

## <a name="run-projects-locally"></a>在本地运行项目

在本地运行 Azure 静态 Web 应用涉及三个过程，具体取决于你的项目是否包含 API。

- 运行本地 Web 服务器
- 运行 API
- 将 Web 项目连接到 API

在浏览器中运行应用程序时，可能需要也可能不需要本地 Web 服务器，具体取决于生成网站的方式。 使用前端 JavaScript 框架和静态站点生成器时，此功能内置于各自的 CLI（命令行接口）中。 以下链接指向 CLI 参考以便选择框架、库和生成器。

### <a name="javascript-frameworks-and-libraries"></a>JavaScript 框架和库

- [Angular CLI](https://angular.io/cli)
- [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html)
- [React CLI](https://create-react-app.dev/)

### <a name="static-site-generators"></a>静态站点生成器

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

如果使用 CLI 工具为站点提供服务，则可以跳到[运行 API](#run-api-locally) 部分。

### <a name="running-a-local-web-server-with-live-server"></a>使用 Live Server 运行本地 Web 服务器

适用于 Visual Studio Code 的 Live Server 扩展提供了服务静态内容的本地开发 Web 服务器。

#### <a name="create-a-repository"></a>创建存储库

1. 导航到 [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) 并使用此模板创建名为 vanilla-api 的新 GitHub 项目。

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="GitHub“新建存储库”窗口":::

1. 打开 Visual Studio Code。

1. 按 F1 打开命令面板。

1. 在搜索框中键入“clone”，然后选择“Git:Clone”。

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="Visual Studio Code 中的 git clone 选项":::

1. 为“存储库 URL”输入以下值。

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. 为新项目选择文件夹位置。

1. 当系统提示打开已克隆的存储库时，请选择“打开”。

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="在新窗口中打开":::

Visual Studio Code 在编辑器中打开已克隆的项目。

### <a name="run-the-website-locally-with-live-server"></a>使用 Live Server 在本地运行网站

1. 按 F1 打开命令面板。

1. 在搜索框中键入“Live Server”，然后选择“Live Server:使用 Live Server 打开”

    此时将打开一个浏览器选项卡以显示应用程序。

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="在浏览器中运行的简单静态站点":::

    此应用程序向 `api/message` 终结点发出 HTTP 请求。 现在，该请求失败，因为需要启动此应用程序的 API 部分。

### <a name="run-api-locally"></a>在本地运行 API

Azure 静态 Web 应用 API 由 Azure Functions 提供支持。 有关将 API 添加到 Azure 静态 Web 应用项目的详细信息，请参阅[使用 Azure Functions 将 API 添加到 Azure 静态 Web 应用](add-api.md)。

作为 API 创建过程的一部分，将为 Visual Studio Code 创建启动配置。 此配置位于 .vscode 文件夹中。 此文件夹包含用于在本地生成和运行 API 的所有必需设置。

1. 在 Visual Studio Code 中，按 F5 启动 API。

1. 此时将打开一个新的终端实例，其中显示来自 API 生成过程的输出。

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="Visual Studio Code 终端中运行的 API":::

   Visual Studio Code 中的状态栏现在变为橙色。 此颜色表示 API 现在正在运行且调试器已连接。

1. 接下来，按 Ctrl/Cmd 并单击终端中的 URL 以打开调用 API 的浏览器窗口。

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="API 调用的浏览器显示结果":::

### <a name="debugging-the-api"></a>调试 API

1. 在 Visual Studio Code 中打开 api/GetMessage/index.js 文件。

1. 单击第 2 行的左侧空白处以设置断点。 此时会出现一个红点，指示已设置断点。

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Visual Studio Code 中的断点":::

1. 在浏览器中，刷新 <http://127.0.0.1:7071/api/message> 上运行的页面。

1. 已在 Visual Studio Code 中命中断点且已暂停程序执行。

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Visual Studio Code 中命中的断点":::

   [API 的 Visual Studio Code 中提供了完整的调试体验](https://code.visualstudio.com/Docs/editor/debugging)。

1. 按调试栏中的“继续”按钮以继续执行。

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Visual Studio Code 中的“继续”按钮":::

### <a name="calling-the-api-from-the-application"></a>从应用程序调用 API

部署后，Azure 静态 Web 应用会自动将这些请求映射到 api 文件夹中的终结点。 此映射确保从应用程序到 API 的请求类似于以下示例。

```javascript
let response = await fetch("/api/message");
```

在本地运行应用程序时，有两种方法可以配置 `api` 路由的路径，具体取决于应用程序是否是使用 JavaScript 框架 CLI 生成的。

- 环境配置文件（建议用于 JavaScript 框架和库）
- 本地代理

### <a name="environment-configuration-files"></a>环境配置文件

如果使用具有 CLI 的前端框架生成应用，则应使用环境配置文件。 每个框架或库都以不同的方式处理这些环境配置文件。 将在本地运行应用程序时使用的配置文件用于开发，并将在生产环境中运行应用程序时使用的配置文件用于生产，这很常见。 你使用的 JavaScript 框架或静态站点生成器的 CLI 会自动知道在本地使用开发文件，而在通过 Azure 静态 Web 应用生成应用时使用生产文件。

在开发配置文件中，可以指定 API 的路径，该路径指向在本地运行站点的 API 的 `http:127.0.0.1:7071` 的本地位置。

```
API=http:127.0.0.1:7071/api
```

在生产配置文件中，将 API 的路径指定为 `api`。 这样，应用程序将在生产环境中运行时通过“yoursite.com/api”调用 API。

```
API=api
```

这些配置值可以引用为 Web 应用的 JavaScript 中的节点环境变量。

```js
let response = await fetch(`${process.env.API}/message`);
```

当 CLI 用于在开发模式下运行站点或生成用于生产的站点时，`process.env.API` 值将替换为相应配置文件中的值。

有关为前端 JavaScript 框架和库配置环境文件的详细信息，请参阅以下文章：

- [Angular 环境变量](https://angular.io/guide/build#configuring-application-environments)
- [React - 添加自定义环境变量](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue - 模式和环境变量](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>重新启动 Live Server

1. 在 Visual Studio Code 中，按 F1 打开命令面板。

1. 键入“Live Server”，然后选择“Live Server:停止 Live Server”。

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="在 Visual Studio 命令面板中停止 Live Server 命令":::

1. 按 F1 打开命令面板。

1. 键入“Live Server”，然后选择“Live Server:使用 Live Server 打开”来启动 Live Server。

1. 刷新 `http://locahost:3000` 上运行的应用程序。 浏览器现在显示从 API 返回的消息。

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="浏览器中显示的 Hello from API":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置应用设置](application-settings.md)
