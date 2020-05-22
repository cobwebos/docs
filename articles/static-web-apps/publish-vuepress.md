---
title: 教程：将 VuePress 站点发布到 Azure 静态 Web 应用
description: 本教程介绍如何将 VuePress 应用程序部署到 Azure 静态 Web 应用。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: bd9eaad0c141eda815da159e3c13d6c51f5e6200
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593594"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>教程：将 VuePress 站点发布到 Azure 静态 Web 应用（预览）

本文演示如何创建 [VuePress](https://vuepress.vuejs.org/) Web 应用程序并将其部署到 [Azure 静态 Web 应用](overview.md)。 最终的成果是一个新的 Azure 静态 Web 应用以及相关联的可控制应用生成和发布方式的 GitHub 操作。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> - 创建 VuePress 应用
> - 设置 Azure 静态 Web 应用
> - 将 VuePress 应用部署到 Azure

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 如果没有帐户，可以[免费创建一个帐户](https://azure.microsoft.com/free/)。
- 一个 GitHub 帐户。 如果没有帐户，可以[免费创建一个帐户](https://github.com/join)。
- 安装 [Node.js](https://nodejs.org)。

## <a name="create-a-vuepress-app"></a>创建 VuePress 应用

通过命令行接口 (CLI) 创建 VuePress 应用程序：

1. 新建一个文件夹，用于 VuePress 应用。

   ```bash
   mkdir static-site
   ```

1. 在此文件夹中添加 README.md 文件。

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. 初始化 package.json 文件。

   ```bash
   npm init -y
   ```

1. 将 VuePress 作为 `devDependency` 添加。

   ```bash
   npm install --save-dev vuepress
   ```

1. 从文本编辑器中打开 package.json 文件，将生成命令添加到 [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html) 部分。

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. 创建 .gitignore 文件，以排除“node\_modules”文件夹。

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. 初始化 Git 存储库。

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>将应用程序推送到 GitHub

你需要 GitHub 上的存储库以连接到 Azure 静态 Web 应用。 以下步骤展示了如何创建站点的存储库。

1. 从名为 vuepress-static-app 的 [https://github.com/new](https://github.com/new) 创建空白 GitHub 存储库（不创建自述文件）。

1. 将 GitHub 存储库作为远程存储库添加到本地存储库。 确保添加 GitHub 用户名，来替代下面命令中的 `<YOUR_USER_NAME>` 占位符。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. 将本地存储库推送到 GitHub。

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>部署 Web 应用

下列步骤说明了如何创建新的静态 Web 应用并将其部署到生产环境。

### <a name="create-the-application"></a>创建应用程序

1. 导航到 [Azure 门户](https://portal.azure.com)
1. 单击“创建资源”
1. 搜索“静态 Web 应用”
1. 单击“静态 Web 应用(预览)”
1. 单击“创建” 

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="在门户中创建静态 Web 应用（预览）":::

1. 对于“订阅”，请接受列出的订阅，或从下拉列表中选择一个新订阅。

1. 在“资源组”中选择“新建”。 在“新资源组名称”中，输入“vuepress-static-app”并选择“确定”。

1. 接下来，在“名称”框中提供应用的全局唯一名称。 有效字符包括 `a-z`、`A-Z`、`0-9` 和 `-`。 此值用作静态应用的 URL 前缀，采用 `https://<APP_NAME>.azurestaticapps.net` 格式。

1. 对于“区域”，选择靠近你的可用区域。

1. 对于“SKU”，选择“免费”。

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="已填写的详细信息":::

1. 单击“使用 GitHub 登录”按钮。

1. 选择在其中创建存储库的“组织”。

1. 选择“vuepress-static-app”作为“存储库”。

1. 对于“分支”，选择“主”。

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="已完成的 GitHub 信息":::

### <a name="build"></a>构建

接下来，添加生成过程用于生成应用的配置设置。 以下设置配置 GitHub Actions 工作流文件。

1. 单击“下一步:生成 >”按钮以编辑生成配置

1. 将“应用位置”设置为 /。

1. 将“应用项目位置”设置为“.vuepress/dist”。

“API 位置”的值不是必需的，因为目前不会部署 API。

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="生成设置":::

### <a name="review-and-create"></a>查看并创建

1. 单击“查看 + 创建”按钮以验证详细信息是否都正确。

1. 单击“创建”以开始创建 Azure 静态 Web 应用并为部署预配 GitHub Action。

1. 部署完成后，单击“转到资源”。

1. 在资源屏幕中，单击 URL 链接，打开已部署的应用程序。 可能需要等待一两分钟，GitHub 操作才能完成。

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="已部署的应用程序":::

### <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加自定义域](custom-domain.md)
