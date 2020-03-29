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
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220827"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>在 LUIS 门户中创建新的 LUIS 应用
可通过多种方法创建 LUIS 应用。 可以在 LUIS 门户中创建 LUIS 应用，也可以通过 LUIS 创作 [API](developer-reference-resource.md) 创建。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>使用 LUIS 门户

您可以通过多种方式在预览门户中创建新应用：

* 从一个空应用开始，创建意向、表述和实体。
* 从一个空应用开始，添加[预生成的域](luis-how-to-use-prebuilt-domains.md)。
* 从已包含意图、陈述`.lu`和`.json`实体的 或 文件中导入 LUIS 应用。

## <a name="using-the-authoring-apis"></a>使用创作 API
可以通过以下几种方式使用创作 API 创建新应用：

* [添加应用程序](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)- 从空应用开始，然后创建意图、陈述和实体。
* [添加预构建的应用程序](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5)- 从预构建的域开始，包括意图、陈述和实体。  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>在 LUIS 中创建新应用

1. 在 **"我的应用"** 页上，选择您的订阅，然后创作资源 **，然后创建**。 如果使用免费试用密钥，请了解如何[创建创作资源](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)。

    ![LUIS 应用列表](./media/create-app-in-portal.png)


1. 在对话框中，输入应用程序的名称，如`Pizza Tutorial`。

    ![“创建新应用”对话框](./media/create-pizza-tutorial-app-in-portal.png)

1. 选择应用程序区域性，然后选择 **"完成**"。 此时，描述和预测资源是可选的。 您可以随时在门户的 **"管理**"部分中设置。

    > [!NOTE]
    > 创建应用程序后将无法更改区域性。 

    创建应用后，LUIS 门户将显示 **"意向"** 列表，`None`并列出已为您创建的意图。 现在，您有一个空应用。 
    
    > [!div class="mx-imgBorder"]
    > ![创建"无"意图的意向列表，无需示例陈述。](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>其他可用的操作

上下文工具栏提供其他操作：

* 重命名应用
* 使用`.lu`或`.json`
* 将应用导出`.lu`为 （对于`.json`[LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)）或`.zip`（对于[LUIS 容器](luis-container-howto.md)）
* 导入容器终结点日志，以查看终结点陈述
* 导出终结点日志，作为`.csv`脱机分析。
* 删除应用

## <a name="next-steps"></a>后续步骤

如果应用设计包括意图检测，[请创建新的意图](luis-how-to-add-intents.md)，并添加示例陈述。 如果应用设计仅提取数据，请向 None 意图添加示例陈述，然后[创建实体](luis-how-to-add-example-utterances.md)，并将示例陈述与这些实体一起标记。 
