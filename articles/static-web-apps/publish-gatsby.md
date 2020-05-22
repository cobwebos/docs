---
title: 教程：将 Gatsby 站点发布到 Azure 静态 Web 应用
description: 本教程介绍如何将 Gatsby 应用程序部署到 Azure 静态 Web 应用。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 14706b623c6973c09b499e4fcd905e3eed430898
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593374"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>教程：将 Gatsby 站点发布到 Azure 静态 Web 应用（预览）

本文演示如何创建 [Gatsby](https://gatsbyjs.org) Web 应用程序并将其部署到 [Azure 静态 Web 应用](overview.md)。 最终结果是一个新的静态 Web 应用站点（其中包含关联的 GitHub 操作），使你可以控制如何生成和发布应用。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> - 创建 Gatsby 应用
> - 设置 Azure 静态 Web 应用站点
> - 将 Gatsby 应用部署到 Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 如果没有帐户，可以[免费创建一个帐户](https://azure.microsoft.com/free/)。
- 一个 GitHub 帐户。 如果没有帐户，可以[免费创建一个帐户](https://github.com/join)。
- 安装 [Node.js](https://nodejs.org)。

## <a name="create-a-gatsby-app"></a>创建 Gatsby 应用

使用 Gatsby 命令行接口 (CLI) 创建 Gatsby 应用：

1. 打开终端
1. 使用 [npx](https://www.npmjs.com/package/npx) 工具通过 Gatsby CLI 创建新应用。 这可能需要几分钟的时间。

   ```bash
   npx gatsby new static-web-app
   ```

1. 导航到新建的应用

   ```bash
   cd static-web-app
   ```

1. 初始化 Git 存储库

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>将应用程序推送到 GitHub

你需要具有 GitHub 上的存储库来创建新的 Azure 静态 Web 应用资源。

1. 从名为 gatsby-static-web-app 的 [https://github.com/new](https://github.com/new) 创建空白 GitHub 存储库（不创建自述文件）。

1. 接下来，将刚创建的 GitHub 存储库作为远程存储库添加到本地存储库。 确保添加 GitHub 用户名，来替代下面命令中的 `<YOUR_USER_NAME>` 占位符。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
   ```

1. 将本地存储库推送到 GitHub。

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>部署 Web 应用

下列步骤说明了如何创建新的静态站点应用并将其部署到生产环境。

### <a name="create-the-application"></a>创建应用程序

1. 导航到 [Azure 门户](https://portal.azure.com)
1. 单击“创建资源”
1. 搜索“静态 Web 应用”
1. 单击“静态 Web 应用(预览)”
1. 单击“创建” 

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="在门户中创建静态 Web 应用（预览）":::

1. 对于“订阅”，请接受列出的订阅，或从下拉列表中选择一个新订阅。

1. 在“资源组”中选择“新建”。 在“新资源组名称”中，输入“gatsby-static-web-app”并选择“确定”。

1. 接下来，在“名称”框中提供应用的全局唯一名称。 有效字符包括 `a-z`、`A-Z`、`0-9` 和 `-`。 此值用作静态 Web 应用的 URL 前缀，采用 `https://<YOUR_APP_NAME>.azurestaticapps.net` 格式。

1. 对于“区域”，选择靠近你的可用区域。

1. 对于“SKU”，选择“免费”。

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="已填写的详细信息":::

1. 单击“使用 GitHub 登录”按钮。

1. 选择在其中创建存储库的“组织”。

1. 选择“gatsby-static-web-app”作为“存储库”。

1. 对于“分支”，选择“主”。

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="已完成的 GitHub 信息":::

### <a name="build"></a>构建

接下来，添加生成过程用于生成应用的配置设置。

1. 单击“下一步:生成 >”按钮以编辑生成配置

1. 若要在 GitHub 操作中配置步骤的设置，请将“应用位置”设置为 /。

1. 将“应用项目位置”设置为“公共”。

   “API 位置”的值不是必需的，因为目前不会部署 API。

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="生成设置":::

### <a name="review-and-create"></a>查看并创建

1. 单击“查看 + 创建”按钮以验证详细信息是否都正确。

1. 单击“创建”以开始创建应用服务静态 Web 应用并为部署预配 GitHub 操作。

1. 部署完成后，单击“转到资源”。

1. 在资源屏幕中，单击 URL 链接，打开已部署的应用程序。 可能需要等待一两分钟，GitHub 操作才能完成。

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="已部署的应用程序":::

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加自定义域](custom-domain.md)
