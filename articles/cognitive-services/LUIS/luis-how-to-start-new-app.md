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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220827"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>在 LUIS 门户中创建新的 LUIS 应用
可通过多种方法创建 LUIS 应用。 可以在 LUIS 门户中创建 LUIS 应用，也可以通过 LUIS 创作 [API](developer-reference-resource.md) 创建。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>使用 LUIS 门户

可以通过多种方式在预览门户中创建新应用程序：

* 从一个空应用开始，创建意向、表述和实体。
* 从一个空应用开始，添加[预生成的域](luis-how-to-use-prebuilt-domains.md)。
* 从已包含意向、最谈话`.lu`和`.json`实体的或文件导入 LUIS 应用。

## <a name="using-the-authoring-apis"></a>使用创作 API
可以通过以下几种方式使用创作 API 创建新应用：

* [添加应用程序](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f)-使用空应用开始，并创建意向、最谈话和实体。
* [添加预构建的应用程序](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5)-从预生成的域开始，包括意向、最谈话和实体。  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>在 LUIS 中创建新应用

1. 在 **"我的应用**" 页上，选择你的订阅，然后创建资源，然后单击 " **+ 创建**"。 如果使用的是免费试用密钥，请学习如何[创建创作资源](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)。

    ![LUIS 应用列表](./media/create-app-in-portal.png)


1. 在 "" 对话框中，输入应用程序的名称，例如`Pizza Tutorial`。

    ![“创建新应用”对话框](./media/create-pizza-tutorial-app-in-portal.png)

1. 选择应用程序区域性，然后选择 "**完成**"。 说明和预测资源此时是可选的。 可以在门户的 "**管理**" 部分中随时设置。

    > [!NOTE]
    > 创建应用程序后将无法更改区域性。 

    创建应用后，LUIS 门户会显示`None`意向列表，其中已为你**创建意向。** 现在，你已有一个空的应用程序。 
    
    > [!div class="mx-imgBorder"]
    > ![未创建意向的意向列表，无示例最谈话。](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>其他可用操作

上下文工具栏提供了其他操作：

* 重命名应用
* 使用`.lu`或从文件导入`.json`
* 将应用导出`.lu`为（对于[LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)） `.json`、或`.zip` （对于[LUIS 容器](luis-container-howto.md)）
* 导入容器终结点日志，查看终结点最谈话
* 将`.csv`终结点日志作为脱机分析的进行导出
* 删除应用

## <a name="next-steps"></a>后续步骤

如果你的应用程序设计包含意向检测，请[创建新意向](luis-how-to-add-intents.md)，并添加示例最谈话。 如果你的应用程序设计只是数据提取，请将最谈话示例添加到无意向，然后[创建实体](luis-how-to-add-example-utterances.md)，然后用这些实体标记示例最谈话。 
