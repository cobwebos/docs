---
title: 教程：将服务器呈现的 Nuxt.js 网站部署到 Azure 静态 Web 应用
description: 生成 Nuxt.js 动态站点并将其部署到 Azure 静态 Web 应用。
services: static-web-apps
author: christiannwamba
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: chnwamba
ms.openlocfilehash: 8a4fb581b884d28c8366cbf9a50e001eadd027d9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593748"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps-preview"></a>将服务器呈现的 Nuxt.js 网站部署到 Azure 静态 Web 应用（预览）

此教程介绍如何将 [Nuxt.js](https://nuxtjs.org) 生成的静态网站部署到 [Azure 静态 Web 应用](overview.md)。 首先将介绍如何安装、配置和部署 Nuxt.js 应用。 在此过程中，还会介绍如何处理生成 Nuxt.js 静态页面时遇到的常见问题

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/)。
- 一个 GitHub 帐户。 [免费创建帐户](https://github.com/join)。
- 安装 [Node.js](https://nodejs.org)。

## <a name="set-up-a-nuxtjs-app"></a>安装 Nuxt.js 应用

可以使用 `create-nuxt-app` 设置新的 Nuxt.js 项目。 在本教程中，首先克隆现有存储库，而不是设置新项目。 设置此存储库的目的是演示如何将动态 Nuxt.js 应用部署为静态站点。

1. 通过 GitHub 帐户从模板存储库创建新的存储库。
1. 导航到 <http://github.com/staticwebdev/nuxtjs-starter/generate>
1. 将存储库命名为 nuxtjs-starter
1. 接下来，将新存储库克隆到计算机。 确保将 <YOUR_GITHUB_ACCOUNT_NAME> 替换为你的帐户名称。

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. 导航到新克隆的 Nuxt.js 应用：

   ```bash
   cd nuxtjs-starter
   ```

1. 安装依赖项：

    ```bash
    npm install
    ```

1. 在开发环境中启动 Nuxt.js 应用：

    ```bash
    npm run dev
    ```

导航到 <http://localhost:3000>，打开应用，应看到从你首选的浏览器中打开了下面的网站：

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="启动 Nuxt.js 应用":::

单击框架/库时，应看到已选定项的详细信息页：

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="详细信息页":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>从 Nuxt.js 生成创建静态网站

使用 `npm run build` 生成 Nuxt.js 站点时，应用将生成为传统 Web 应用，而非静态站点。 若要生成静态站点，请使用以下应用程序配置。

1. 更新 package.json 的生成脚本以使用 `nuxt generate` 命令仅生成静态站点：

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate",
    },
    ```

    现在，此命令就绪后，每次推送提交时，静态 Web 应用都将运行 `build` 脚本。

1. 生成静态站点：

    ```bash
    npm run build
    ```

    Nuxt.js 将生成静态站点，并将其复制到工作目录的根目录的 dist 文件夹中。

    > [!NOTE]
    > .gitignore 文件列出了此文件夹，因为当你进行部署时，CI/CD 应生成它。

## <a name="push-your-static-website-to-github"></a>将静态网站推送到 GitHub

Azure 静态 Web 应用从 GitHub 存储库部署应用，并且每次将提交推送到指定的分支时都会继续执行此操作。 使用以下命令将更改同步到 GitHub。

1. 暂存所有已更改的文件：

    ```bash
    git add .
    ```

1. 提交所有更改

    ```bash
    git commit -m "Update build config"
    ```

1. 将所做的更改推送到 GitHub。

    ```bash
    git push origin master
    ```

## <a name="deploy-your-static-website"></a>部署静态网站

以下步骤演示了如何将刚推送到 GitHub 的应用链接到 Azure 静态 Web 应用。 在 Azure 中，可以将应用程序部署到生产环境。

### <a name="create-an-azure-static-web-apps-preview-resource"></a>创建 Azure 静态 Web 应用预览资源

1. 导航到 [Azure 门户](https://portal.azure.com)
1. 单击“创建资源”
1. 搜索“静态 Web 应用”
1. 单击“静态 Web 应用(预览)”
1. 单击“创建” 

1. 从“订阅”下拉列表中选择一个订阅，或使用默认值。
1. 单击“资源组”下拉列表下的“新建”链接。 在“新建资源组名称”中，键入“mystaticsite”，然后单击“确定”
1. 在“名称”文本框中提供应用的全局唯一名称。 有效字符包括 `a-z`、`A-Z`、`0-9` 和 `-`。 此值用作静态应用的 URL 前缀，采用 `https://<APP_NAME>.azurestaticapps.net` 格式。
1. 在“区域”下拉列表中，选择离你最近的区域。
1. 从“SKU”下拉列表中选择“可用”。

   :::image type="content" source="media/deploy-nuxtjs/create-static-web-app.png" alt-text="创建静态 Web 应用":::

### <a name="add-a-github-repository"></a>添加 GitHub 存储库

新的静态 Web 应用帐户需要使用 Nuxt.js 应用访问存储库，以便可以自动部署提交。

1. 单击“使用 GitHub 登录”按钮
1. 选择要在其中创建 Nuxt.js 项目的存储库的“组织”，这可能是你的 GitHub 用户名。
1. 查找并选择之前创建的存储库的名称。
1. 从“分支”下拉列表中选择“主”作为分支。

   :::image type="content" source="media/deploy-nuxtjs/connect-github.png" alt-text="连接 GitHub":::

### <a name="configure-the-build-process"></a>配置生成过程

生成 Azure 静态 Web 应用的目的是自动执行常见任务，例如安装 npm 模块并在每个部署过程中运行 `npm run build`。 但有一些设置（例如，应用程序源文件夹和生成目标文件夹）需要手动配置。

1. 单击“生成”选项卡以配置静态输出文件夹。

      :::image type="content" source="media/deploy-nuxtjs/build-tab.png" alt-text="“生成”选项卡":::

1. 在“应用项目位置”文本框中键入“dist”。

### <a name="review-and-create"></a>查看并创建

1. 单击“查看 + 创建”按钮以验证详细信息是否都正确。
1. 单击“创建”以开始创建资源并为部署预配 GitHub Action。
1. 部署完成后，单击“转到资源”
1. 在“概述”窗口中，单击 URL 链接，打开已部署的应用程序。 

如果网站并未立即加载，则后台 GitHub Actions 工作流仍在运行。 工作流完成后，可以单击“刷新浏览器”以查看 Web 应用。

可以通过导航到存储库的 Actions 来检查 Actions 工作流的状态：

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>同步更改

创建应用后，Azure 静态 Web 应用在存储库中创建了 GitHub Actions 工作流文件。 需要将此文件移到本地存储库，以便同步 Git 历史记录。

返回终端并运行以下命令：`git pull origin master`。

## <a name="configure-dynamic-routes"></a>配置动态路由

导航到新部署的站点，然后单击其中一个框架或库徽标。 你会看到 404 错误页，而不是详细信息页。

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="动态路由 404 错误":::

这样做的原因是，Nuxt.js 生成了静态站点，仅在主页上如此操作。 Nuxt.js 可以为每个 `.vue` 页面文件生成等效的静态 `.html` 文件，但会出现异常。 

如果页面是动态页（例如 `_id.vue`），则没有足够的信息来从此类动态页生成静态 HTML。 必须为动态路由显式提供可能的路径。

## <a name="generate-static-pages-from-dynamic-routes"></a>从动态路由生成静态页面

1. 更新 nuxt.config.js 文件，以便 Nuxt.js 使用所有可用数据的列表为每个框架/库生成静态页：

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` 是一个异步函数，因此你可以在此函数中向 API 发出请求，并使用返回的列表生成路径。

2. 将新更改推送到 GitHub 存储库并等待几分钟，同时 GitHub Actions 再次生成你的站点。 生成完成后，404 错误将消失。

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="已修复动态路由 404 错误":::

> [!div class="nextstepaction"]
> [设置自定义域](custom-domain.md)
