---
title: 教程：审查电子商务产品图像 - 内容审查器
titlesuffix: Azure Cognitive Services
description: 设置一个应用程序，以通过指定的标签分析和分类产品图像（使用 Azure 计算机视觉和自定义视觉），并标记令人反感的图像以作进一步的评审（使用 Azure 内容审查器）。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: e490e51d0eb3e1c4534bed887508474ce3ffcb22
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259319"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>教程：使用 Azure 内容审查器审查电子商务产品图像

本教程介绍如何使用 Azure 认知服务（包括内容审查器）有效分类和审查电子商务场景中的产品图像。 你将使用计算机视觉和自定义视觉向图像应用各种标记（标签），然后创建团队评审，以便将内容审查器的基于机器学习的技术与人工评审团队相结合，提供一个智能审查系统。

本教程演示如何：

> [!div class="checklist"]
> * 注册内容审查器并创建评审团队。
> * 使用内容审查器的图像 API 扫描可能出现的成人和不雅内容。
> * 使用计算机视觉服务扫描名人内容（或计算机视觉可检测到的其他标记）。
> * 使用自定义视觉服务扫描国旗、玩具和笔（或其他自定义标记）。
> * 提供组合的扫描结果，供人工评审和最终决策。

GitHub 上的[电子商务目录审查示例](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)存储库中提供了完整的示例代码。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 内容审查器的订阅密钥。 遵照[创建认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的说明订阅内容审查器服务并获取密钥。
- 计算机视觉订阅密钥（遵照上面相同的说明获取）。
- 任何版本的 [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/)。
- 为每个标签提供一组图像，供自定义视觉分类器使用（在本例中为玩具、笔和美国国旗）。

## <a name="create-a-review-team"></a>创建评审团队

参阅[熟悉内容审查器](quick-start.md)快速入门，获取有关如何注册[内容审查器审查工具](https://contentmoderator.cognitive.microsoft.com/)和创建评审团队的说明。 记下“凭据”页上的“团队 ID”值。

## <a name="create-custom-moderation-tags"></a>创建自定义审查标记

接下来，在评审工具中创建自定义标记（在此过程中如需帮助，请参阅[标记](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags)一文）。 在本例中，我们将添加以下标记：“名人”、“美国”、“国旗”、“玩具”和“笔”。 请注意，并非所有标记都需要是计算机视觉中可检测到的类别（例如“名人”）；可以添加自己的自定义标记，不过，在添加后，必须训练自定义视觉分类器才能检测到这些标记。

![配置自定义标记](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>创建 Visual Studio 项目

1. 在 Visual Studio 中打开“新建项目”对话框。 依次展开“已安装”、“Visual C#”，然后选择“控制台应用(.NET Framework)”。
1. 将应用程序命名为 **EcommerceModeration**，然后单击“确定”。
1. 如果要将此项目添加到现有的解决方案，请将此项目选作单一启动项目。

本教程将突出显示项目的核心代码，但不会介绍所需的每个代码行。 将示例项目（[电子商务目录审查示例](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)）中 _Program.cs_ 的整个内容复制到新项目的 _Program.cs_ 文件。 然后，逐步完成以下各个部分，以了解项目的工作原理以及如何自行使用该项目。

## <a name="define-api-keys-and-endpoints"></a>定义 API 密钥和终结点

如前所述，本教程将使用三个认知服务；因此，需要三个对应的密钥和 API 终结点。 查看 **Program** 类中的以下字段： 

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

需要使用订阅密钥的值（稍后将获取 `CustomVisionKey`）更新 `___Key` 字段，并且可能需要更改 `___Uri` 字段，使之包含正确的区域标识符。 在 `ReviewUri` 字段的 `YOURTEAMID` 部分填写之前创建的评审团队的 ID。 稍后再填充 `CustomVisionUri` 字段的最后一个部分。

## <a name="primary-method-calls"></a>主要方法调用

查看 **Main** 方法中循环访问图像 URL 列表的以下代码。 该代码使用三个不同的服务分析每个图像，在 **ReviewTags** 数组中记录已应用的标记，然后为审查人员创建一个评审（将图像发送到内容审查器评审工具）。 以下部分将介绍这些方法。 请注意，此处可以根据需要控制要发送哪些图像供评审，只需在条件语句中使用 **ReviewTags** 数组来检查已应用的标记即可。

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy 方法

查看 **Program** 类中的 **EvaluateAdultRacy** 方法。 此方法将图像 URL 和键值对数组用作参数。 它调用内容审查器的图像 API（使用 REST）获取图像的成人和不雅评分。 如果评分大于 0.4（范围从 0 到 1），则将 **ReviewTags** 数组中的相应值设置为 **True**。

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags 方法

下一个方法采用图像 URL 和计算机视觉订阅信息，并分析图像中是否存在名人。 如果找到一个或多个名人，则将 **ReviewTags** 数组中的相应值设置为 **True**。 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags 方法

接下来查看 **EvaluateCustomVisionTags** 方法。该方法分类实际产品 &mdash; 在本例中为国旗、玩具和笔。 遵照[如何生成分类器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)指南中的说明生成自己的自定义图像分类器，以检测图像中是否存在国旗、玩具和笔（或选作自定义标记的任何内容）。

![包含笔、玩具和国旗训练图像的自定义视觉网页](images/tutorial-ecommerce-custom-vision.PNG)

训练分类器后，获取预测密钥和预测终结点 URL（检索这些信息时如需帮助，请参阅[获取 URL 和预测密钥](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key)），并将这些值分别分配到 `CustomVisionKey` 和 `CustomVisionUri` 字段。 该方法使用这些值来查询分类器。 如果分类器在图像中找到一个或多个自定义标记，此方法会将 **ReviewTags** 数组中的相应值设置为 **True**。 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>为评审工具创建评审

前面的部分已介绍了可以扫描传入图像中的成人和不雅内容（内容审查器）、名人（计算机视觉）和其他各种对象（自定义视觉）的方法。 接下来请查看 **CreateReview** 方法。该方法将图像及其中应用的所有标记（作为元数据传入）上传到内容审查器评审工具，使之可供人工评审。 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

图像将显示在[内容审查器评审工具](https://contentmoderator.cognitive.microsoft.com/)的“评审”选项卡中。

![内容审查器评审工具的屏幕截图，其中包含多个图像及其突出显示的标记](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>提交测试图像列表

在 **Main** 方法中可以看到，此程序将查找“C:Test”目录，该目录中的 _Urls.txt_ 文件包含图像 URL 列表。 请创建此类文件和目录，或者将路径更改为指向你的文本文件，并在此文件中填充要测试的图像的 URL。

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>运行程序

如果已遵循上述所有步骤，该程序应会处理每个图像（在所有三个服务中查询其相关标记），然后将图像连同标记信息一起上传到内容审查器评审工具。

## <a name="next-steps"></a>后续步骤

在本教程中，你已设置一个程序用于分析产品图像，以便按产品类型对其进行标记，并使评审团队能够在内容审查方面做出明智的决策。 接下来，请了解有关图像审查的详细信息。

> [!div class="nextstepaction"]
> [评审已审查的图像](./review-tool-user-guide/review-moderated-images.md)