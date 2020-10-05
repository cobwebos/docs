---
title: 创建 LUIS 资源
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "80879186"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>在 Azure 门户中创建 LUIS 资源

1. 使用[此链接](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)在 Azure 门户中开始创建 LUIS 资源。

1. 输入所有必需的设置：

    |名称|目的|
    |--|--|
    |订阅名称| 将对资源计费的订阅。|
    |资源组| 你选择或创建的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。|
    |名称| 你选择的自定义名称，作为自定义子域用于创作以及预测终结点查询。|
    |创作位置|与模型关联的区域。|
    |创作定价层|定价层确定每秒和每月的最大事务数。|
    |运行时位置|与已发布的预测终结点运行时关联的区域。|
    |运行时定价层|定价层确定每秒和每月的最大事务数。|

    > [!div class="mx-imgBorder"]
    > [![创建语言理解资源](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. 单击“查看 + 创建”  并等待创建资源。
1. 创建这两个资源后，请仍在 Azure 门户中选择新的创作资源，然后选择“快速入门”  以获取创作终结点 URL  和密钥  ，从而以编程方式进行创作。

> [!TIP]
> 若要使用资源，请在 LUIS 门户中[分配资源](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)。