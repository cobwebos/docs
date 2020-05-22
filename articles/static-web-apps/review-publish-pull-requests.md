---
title: 在 Azure 静态 Web 应用中查看预生产环境中的拉取请求
description: 了解如何在 Azure 静态 Web 应用中使用预生产环境查看拉取请求更改。
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 61c5917c1e4cb9dbf96e90af9a30777ea7c2e66c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594106"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>在 Azure 静态 Web 应用预览中查看预生产环境中的拉取请求

本文演示了如何使用预生产环境查看对使用 [Azure 静态 Web 应用](overview.md)部署的应用程序所做的更改。

预生产（暂存）环境是应用程序的功能齐全的暂存版本，其中包含生产中不可用的更改。

Azure 静态 Web 应用会在存储库中生成 GitHub 操作工作流。 针对工作流监视的分支创建拉取请求时，将生成预生产环境。 预生产环境暂存应用，使你能够在推送到生产环境之前执行评审。

使用 Azure 静态 Web 应用时，可以同时存在多个预生产环境。 每次针对受监视的分支创建拉取请求时，包含更改的暂存版本都将部署到不同的预生产环境中。

使用预生产环境有许多好处。 例如，你能够：

- 查看生产和暂存之间的视觉对象更改。 例如，查看内容和布局的更新。
- 演示对团队所做的更改。
- 比较不同版本的应用程序。
- 使用验收测试验证更改。
- 在部署到生产环境之前执行健全性检查。

> [!NOTE]
> 在预览期间，一次[最多仅允许一个暂存环境](quotas.md)。

## <a name="prerequisites"></a>先决条件

- 使用 Azure 静态 Web 应用配置的现有 GitHub 存储库。 如果没有，请参阅[生成第一个静态应用](getting-started.md)。

## <a name="make-a-change"></a>做出更改

首先在存储库中进行更改。 可以直接在 GitHub 上执行此操作，如以下步骤所示。

1. 导航到 GitHub 上的项目存储库，然后单击“分支”按钮以创建新分支。

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="使用 GitHub 接口创建新分支":::]

    键入分支名称，然后单击“创建分支”。

1. 转到“应用”文件夹，然后更改一些文本内容。 例如，可以更改标题或段落。 找到要编辑的文件后，单击“编辑”进行更改。

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="GitHub 接口中的“编辑文件”按钮":::

1. 进行更改后，单击“提交更改”，将更改提交到分支。

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="GitHub 接口中的“提交更改”按钮":::

## <a name="create-a-pull-request"></a>创建拉取请求

接下来，从此更改创建拉取请求。

1. 在 GitHub 上打开项目的“拉取请求”选项卡：

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="GitHub 存储库中的“拉取请求”选项卡":::

1. 单击分支的“比较和拉取请求”按钮。

1. 可以选择填写有关更改的一些详细信息，然后单击“创建拉取请求”。

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="GitHub 中的拉取请求创建":::

如果需要，可以分配审阅者并添加注释以讨论所做的更改。

> [!NOTE]
> 可以通过将新的提交推送到分支进行多次更改。 然后，拉取请求会自动更新以反映所有更改。

## <a name="review-changes"></a>查看更改

创建拉取请求后，[GitHub 操作](https://github.com/features/actions)部署工作流运行，并将更改部署到预生产环境。

工作流完成应用的生成和部署后，GitHub 机器人会向拉取请求添加注释，其中包含预生产环境的 URL。 可以单击此链接来查看暂存更改。

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="包含预生产 URL 的拉取请求注释":::

单击生成的 URL 以查看更改。

如果你详细地查看该 URL，可以看到它由以下内容组成：`https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net`。

对于给定的拉取请求，URL 保持不变，即使推送新的更新也是如此。 除了 URL 保持不变以外，在拉取请求的整个生命周期内还会重复使用同一预生产环境。

## <a name="publish-changes"></a>发布更改

更改获得批准后，可以通过合并拉取请求将更改发布到生产环境。

单击“合并拉取请求”：

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="GitHub 接口中的“合并拉取请求”按钮":::

合并将更改复制到跟踪的分支（“生产”分支）。 然后，在跟踪的分支上启动部署工作流，更改在应用程序重新生成后就会生效。

若要验证生产中的更改，请打开生产 URL 以加载网站的实时版本。

## <a name="limitations"></a>限制

即使 GitHub 存储库是专用的，应用程序的暂存版本当前可通过其 URL 公开访问。

> [!WARNING]
> 将敏感内容发布到暂存版本时要小心，因为不会限制对预生产环境的访问。

适用于使用静态 Web 应用部署的每个应用的预生产环境数取决于所使用的 SKU 层。 例如，使用免费层时，除了生产环境之外，还可以有 1 个预生产环境。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设置自定义域](custom-domain.md)
