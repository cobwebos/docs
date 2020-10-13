---
title: 快速入门：在 Azure 门户中使用 Azure Static Web Apps 构建你的第一个静态 Web 应用
description: 了解如何使用 Azure 门户生成 Azure Static Web Apps 实例。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: cshoe
ms.openlocfilehash: b009b34be69e48ae6205cd9ab88d1583ca9c6561
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91280550"
---
# <a name="quickstart-building-your-first-static-web-app-in-the-azure-portal"></a>快速入门：在 Azure 门户中构建你的第一个静态 Web 应用

Azure Static Web Apps 通过从 GitHub 存储库生成应用来将网站发布到生产环境。 在此快速入门中，你会使用 Azure 门户将 Web 应用部署到 Azure Static Web Apps。

如果没有 Azure 订阅，[请创建一个免费的试用帐户](https://azure.microsoft.com/free)。

## <a name="prerequisites"></a>先决条件

- [GitHub](https://github.com) 帐户
- [Azure](https://portal.azure.com) 帐户

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>创建静态 Web 应用

现在，已经创建了存储库，可以从 Azure 门户创建静态 Web 应用。

1. 导航到 [Azure 门户](https://portal.azure.com)
1. 选择“创建资源”
1. 搜索“静态 Web 应用”
1. 选择“Static Web Apps (预览)”
1. 选择“创建”

在“基本信息”部分中，首先配置新应用，并将其链接到 GitHub 存储库。

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="“基本信息”部分":::

1. 选择 Azure 订阅
1. 选择或创建新资源组
1. 将应用命名为 my-first-static-web-app。
      1. 有效字符为 `a-z`（不区分大小写）、`0-9` 和 `-`。
1. 选择离你最近的区域
1. 选择免费 SKU
1. 选择“使用 GitHub 登录”按钮，然后向 GitHub 进行身份验证

登录 GitHub 后，输入存储库信息。

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="“基本信息”部分":::

1. 选择首选组织
1. 选择“存储库”下拉列表中的“my-first-web-static-app”
1. 从“分支”下拉列表中选择“master”

> [!NOTE]
> 如果看不到任何存储库，则可能需要在 GitHub 中授权 Azure Static Web Apps。 浏览到 GitHub 存储库，转到“设置”>“应用程序”>“授权 OAuth 应用”，选择“Azure Static Web Apps”，然后选择“授予”。 对于组织存储库，你必须是组织的所有者才能授予权限。

1. 在“生成详细信息”部分中，添加特定于首选前端框架的配置详细信息。

    # <a name="no-framework"></a>[无框架](#tab/vanilla-javascript)

    1. 从“生成预设”下拉列表中选择“自定义”
    1. 保留“应用位置”框中的默认值
    1. 清除“Api 位置”框中的默认值
    1. 将“应用项目位置”框留空

    # <a name="angular"></a>[Angular](#tab/angular)

    1. 从“生成预设”下拉列表中选择“Angular”
    1. 保留“应用位置”框中的默认值
    1. 清除“Api 位置”框中的默认值
    1. 在“应用项目位置”框中键入 dist/angular-basic

    # <a name="react"></a>[React](#tab/react)

    1. 从“生成预设”下拉列表中选择“响应”
    1. 保留“应用位置”框中的默认值
    1. 清除“Api 位置”框中的默认值
    1. 在“应用项目位置”框中键入 build

    # <a name="vue"></a>[Vue](#tab/vue)

    1. 从“生成预设”下拉列表中选择“Vue.js”
    1. 保留“应用位置”框中的默认值
    1. 清除“Api 位置”框中的默认值
    1. 保留“应用项目位置”框中的默认值

    ---

1. 选择“查看 + 创建”。

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="“基本信息”部分":::

    > [!NOTE]
    > 若要在创建应用后更改这些值，可编辑[工作流文件](github-actions-workflow.md)。

1. 选择“创建”。

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="“基本信息”部分":::

1. 选择“转到资源”。

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="“基本信息”部分":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，可按以下步骤删除 Azure 静态 Web 应用实例：

1. 打开 [Azure 门户](https://portal.azure.com)
1. 在顶部搜索栏中搜索“my-first-web-static-app”
1. 选择应用名称
1. 选择“删除”按钮
1. 选择“是”以确认删除操作

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加 API](add-api.md)
