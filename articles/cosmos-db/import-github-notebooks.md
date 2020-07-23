---
title: 将笔记本从 GitHub 存储库导入 Azure Cosmos DB 并运行
description: 了解如何连接到 GitHub 并将笔记本从 GitHub 存储库导入到 Azure Cosmos 帐户。 导入后，可以运行、编辑它们并将所做的更改保存回 GitHub。
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: d85f020152fa3cadb1d437c125d327f5e895e14e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262882"
---
# <a name="import-notebooks-from-a-github-repo-into-azure-cosmos-db"></a>将笔记本从 GitHub 存储库导入 Azure Cosmos DB

为 Azure Cosmos 帐户[启用笔记本支持](enable-notebooks.md)后，可以创建新笔记本、从本地计算机中上传新笔记本或从 GitHub 帐户导入现有笔记本。 本文演示如何将笔记本工作区连接到 GitHub 并将笔记本从 GitHub 存储库导入到 Azure Cosmos 帐户。 导入后，可以运行、更改它们，并将所做的更改保存回 GitHub。

## <a name="get-notebooks-from-github"></a>从 GitHub 获取笔记本

可以连接到自己的 GitHub 存储库或其他公共 GitHub 存储库，以便在 Azure Cosmos DB 中阅读、创作和共享笔记本。 使用以下步骤连接到 GitHub 帐户：

1. 登录到 [Azure 门户](https://portal.azure.com/)并导航到 Azure Cosmos 帐户。

1. 打开“数据资源管理器”选项卡。此选项卡显示所有现有数据库、容器和笔记本。

1. 选择“连接到 GitHub”菜单项。

1. 此时会打开一个选项卡，可以在其中选择仅连接到“公共存储库”或同时连接到“公共和专用存储库” 。  选择所需选项后，请选择“访问授权”。 Azure Cosmos DB 需要获得授权才能访问 GitHub 帐户中的存储库。

   :::image type="content" source="./media/import-github-notebooks/authorize-access-github.png" alt-text="授权 Azure Cosmos DB 访问 GitHub 存储库":::

1. 此时会重定向到“github.com”网页，可以在其中确认授权。 选择“授权 AzureCosmosDBNotebooks”按钮，然后在提示中输入 GitHub 帐户密码。

1. 授权成功后，会返回 Azure Cosmos 帐户。 然后，可以看到 GitHub 帐户中的所有公共/专用存储库。 可以从列表中选择一个可用的存储库，或使用存储库的 URL 直接添加存储库。

1. 选择所需的存储库后，相应的存储库条目将从“取消固定的存储库”部分移到“已固定的存储库”部分 。 如果需要，还可以选择要从中导入笔记本的该存储库的某个特定分支。

   :::image type="content" source="./media/import-github-notebooks/choose-repo-branch.png" alt-text="选择存储库和分支":::

1. 选择“确定”完成导入操作。 所选存储库分支中的所有可用笔记本都将导入到 Azure Cosmos 帐户中。

与 GitHub 帐户集成后，只有你能在 Azure Cosmos 帐户中查看存储库和笔记本的列表。 即使多个用户登录到 Azure Cosmos DB 帐户并添加了自己的帐户，也是如此。 换句话说，多个用户可以使用相同的 Azure Cosmos 帐户将笔记本工作区连接到 GitHub。 但是，每个用户只会看到自己导入的存储库和笔记本的列表。 你无法查看其他人导入的笔记本。

若要从笔记本工作区断开与 GitHub 帐户的连接，请打开“数据资源管理器”选项卡，选择“GitHub 存储库”旁边的 `…`，并选择“从 GitHub 断开连接”  。

## <a name="edit-a-notebook-and-push-changes-to-github"></a>编辑笔记本并将所做的更改推送到 GitHub

可以编辑现有笔记本或将新笔记本添加到存储库，并将所做更改保存回 GitHub。

编辑完现有笔记本后，选择“保存”。 此时将打开一个对话框，可以在其中为已做的更改输入提交消息。 选择“提交”，GitHub 中的笔记本便会相应更新。 可以通过登录到 GitHub 帐户并验证提交历史记录来验证更新。

提交更改后，在常规 GitHub 流中，通常会将更改推送到某个远程计算机。 但是，在这种情况下，“提交选项”的作用是将更新“暂存、提交和推送”到 GitHub。

:::image type="content" source="./media/import-github-notebooks/commit-changes-github.png" alt-text="编辑笔记本并将更改提交到 GitHub":::

## <a name="next-steps"></a>后续步骤

* 了解 [Azure Cosmos DB Jupyter 笔记本](cosmosdb-jupyter-notebooks.md)的优势。

