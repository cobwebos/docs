---
title: 创建新应用 - LUIS
titleSuffix: Azure Cognitive Services
description: 在语言理解 (LUIS) 网页上创建和管理应用程序。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 3b9466bf52b3650216408a8f0906f3208dfae42d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653898"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>在 LUIS 门户中创建新的 LUIS 应用
可通过多种方法创建 LUIS 应用。 可以在 LUIS 门户中创建 LUIS 应用，也可以通过 LUIS 创作 [API](developer-reference-resource.md) 创建。

## <a name="using-the-luis-portal"></a>使用 LUIS 门户

可以通过以下几种方式在门户中创建新应用：

* 从一个空应用开始，创建意向、表述和实体。
* 从一个空应用开始，添加[预生成的域](luis-how-to-use-prebuilt-domains.md)。
* 从 `.lu` `.json` 已包含意向、最谈话和实体的或文件导入 LUIS 应用。

## <a name="using-the-authoring-apis"></a>使用创作 API
可以通过以下几种方式使用创作 API 创建新应用：

* [添加应用程序](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)-使用空应用开始，并创建意向、最谈话和实体。
* [添加预构建的应用程序](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5)-从预生成的域开始，包括意向、最谈话和实体。


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>在 LUIS 中创建新应用

1. 在 **"我的应用**" 页上，选择你的**订阅**，然后创建**资源**，然后单击 " **+ 创建**"。 如果使用的是免费试用密钥，请学习如何[创建创作资源](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)。

> [!div class="mx-imgBorder"]
> ![LUIS 应用列表](./media/create-app-in-portal.png)

1. 在 "" 对话框中，输入应用程序的名称，例如 `Pizza Tutorial` 。

    ![“创建新应用”对话框](./media/create-pizza-tutorial-app-in-portal.png)

1. 选择应用程序区域性，然后选择 "**完成**"。 说明和预测资源此时是可选的。 可以在门户的 "**管理**" 部分中随时设置。

    > [!NOTE]
    > 创建应用程序后将无法更改区域性。

    创建应用后，LUIS 门户会显示意向**列表，** 其中 `None` 已为你创建意向。 现在，你已有一个空的应用程序。

    > [!div class="mx-imgBorder"]
    > ![未创建意向的意向列表，无示例最谈话。](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available-on-my-apps-page"></a>"我的应用" 页上提供的其他操作

上下文工具栏提供了其他操作：

* 重命名应用
* 使用或从文件导入 `.lu``.json`
* 将应用导出为 `.lu` （对于[LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)）、 `.json` 或 `.zip` （对于[LUIS 容器](luis-container-howto.md)）
* 导入容器终结点日志，查看终结点最谈话
* 将终结点日志作为 `.csv` 脱机分析的进行导出
* 删除应用

## <a name="next-steps"></a>后续步骤

如果你的应用程序设计包含意向检测，请[创建新意向](luis-how-to-add-intents.md)，并添加示例最谈话。 如果你的应用程序设计只是数据提取，请将最谈话示例添加到无意向，然后[创建实体](luis-how-to-add-example-utterances.md)，然后用这些实体标记示例最谈话。
