---
title: 快速入门：使用 Azure 静态 Web 应用生成第一个静态 Web 应用
description: 了解如何使用首选前端框架生成 Azure 静态 Web 应用实例。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 14fd237b6437c15ede2da7c0694004f6c22736cc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599127"
---
# <a name="quickstart-building-your-first-static-web-app"></a>快速入门：生成第一个静态 Web 应用

Azure 静态 Web 应用通过从 GitHub 存储库生成应用来将网站发布到生成环境。 在本快速入门中，将使用 GitHub 存储库中首选前端框架生成 Web 应用程序。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

- [GitHub](https://github.com) 帐户
- [Azure](https://portal.azure.com) 帐户

## <a name="create-a-repository"></a>创建存储库

本文使用 GitHub 模板存储库来轻松创建新存储库。 这些模板的特点是具有使用不同前端框架生成的入门级应用。

# <a name="angular"></a>[Angular](#tab/angular)

- 导航到以下位置以创建新存储库
  - https://github.com/staticwebdev/angular-basic/generate
- 将存储库命名为 my-first-static-web-app

# <a name="react"></a>[React](#tab/react)

- 导航到以下位置以创建新存储库
  - https://github.com/staticwebdev/react-basic/generate
- 将存储库命名为 my-first-static-web-app

# <a name="vue"></a>[Vue](#tab/vue)

- 导航到以下位置以创建新存储库
  - https://github.com/staticwebdev/vue-basic/generate
- 将存储库命名为 my-first-static-web-app

# <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

- 导航到以下位置以创建新存储库
  - https://github.com/staticwebdev/vanilla-basic/generate
- 将存储库命名为 my-first-static-web-app

> [!NOTE]
> Azure 静态 Web 应用需要至少一个 HTML 文件来创建 Web 应用。 在此步骤中创建的存储库包括单个 index.html 文件。

---

单击“从模板创建存储库”按钮。

:::image type="content" source="media/getting-started/create-template.png" alt-text="从模板创建存储库":::

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

现在，已经创建了存储库，可以从 Azure 门户创建静态 Web 应用。

- 导航到 [Azure 门户](https://portal.azure.com)
- 单击“创建资源”
- 搜索“静态 Web 应用”
- 单击“静态 Web 应用(预览)”
- 单击“创建” 

### <a name="basics"></a>基础

首先，配置新应用，并将其链接到 GitHub 存储库。

:::image type="content" source="media/getting-started/basics-tab.png" alt-text="“基本信息”选项卡":::

- 选择 Azure 订阅
- 选择或创建新资源组
- 将应用命名为 my-first-static-web-app。
  - 有效字符为 `a-z`（不区分大小写）、`0-9` 和 `_`。
- 选择离你最近的区域
- 选择免费 SKU
- 单击“使用 GitHub 登录”按钮，然后使用 GitHub 进行身份验证

登录 GitHub 后，请输入存储库信息。

:::image type="content" source="media/getting-started/repository-details.png" alt-text="存储库详细信息":::

- 选择首选组织
- 选择“存储库”下拉列表中的“my-first-web-static-app”
- 从“分支”下拉列表中选择“master”
- 单击“下一步:生成 >”按钮以编辑生成配置

:::image type="content" source="media/getting-started/next-build-button.png" alt-text="下一个生成按钮":::

### <a name="build"></a>构建

接下来，添加特定于首选前端框架的详细信息。

# <a name="angular"></a>[Angular](#tab/angular)

- 在“应用位置”框中输入“/”
- 清除“Api 位置”框中的默认值
- 在“应用项目位置”框中输入“dist/angular-basic”

# <a name="react"></a>[React](#tab/react)

- 在“应用位置”框中输入“/”
- 清除“Api 位置”框中的默认值
- 在“应用项目位置”框中输入“build”

# <a name="vue"></a>[Vue](#tab/vue)

- 在“应用位置”框中输入“/”
- 清除“Api 位置”框中的默认值
- 在“应用项目位置”框中输入“dist”

# <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

- 在“应用位置”框中输入“/”
- 清除“Api 位置”框中的默认值
- 清除“应用项目位置”框中的默认值

---

单击“查看 + 创建”按钮。

:::image type="content" source="media/getting-started/review-create.png" alt-text="“查看创建”按钮":::

### <a name="review--create"></a>查看 + 创建

请求验证后，可以继续创建应用程序。

单击“创建”按钮

:::image type="content" source="media/getting-started/create-button.png" alt-text="“创建”按钮":::

创建资源后，单击“转到资源”按钮

:::image type="content" source="media/getting-started/resource-button.png" alt-text="“转到资源”按钮":::

## <a name="view-the-website"></a>查看网站

通过两个方面来部署静态应用。 第一个预配构成应用的基础 Azure 资源。 第二个是生成和发布应用程序的 GitHub Actions 工作流。

在导航到新静态站点之前，必须先完成部署生成的运行。

静态 Web 应用“概述”窗口显示了一系列链接，可帮助你与 Web 应用进行交互。

:::image type="content" source="media/getting-started/overview-window.png" alt-text="“概述”窗口":::

1. 单击“单击此处以查看 GitHub Actions 运行的状态”的标题，即可转到针对存储库运行的 GitHub Actions。 确认部署作业完成后，即可通过生成的 URL 导航到网站。

2. GitHub Actions 工作流完成后，可以单击该 URL 链接以在新选项卡中打开网站。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可按以下步骤删除 Azure 静态 Web 应用实例：

1. 打开 [Azure 门户](https://portal.azure.com)
1. 在顶部搜索栏中搜索“my-first-web-static-app”
1. 单击应用名称
1. 单击“删除”按钮
1. 单击“是”以确认删除操作

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
