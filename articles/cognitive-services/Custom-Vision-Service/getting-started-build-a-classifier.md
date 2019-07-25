---
title: 快速入门：生成分类器 - 自定义视觉服务
titlesuffix: Azure Cognitive Services
description: 在本快速入门中，你将了解如何使用自定义视觉服务网站创建图像分类模型。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: anroth
ms.openlocfilehash: 748336dcea580cefaf7638c86c1466bf0c16a472
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423568"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>快速入门：如何使用自定义影像服务生成分类器

在本快速入门中，你将了解如何通过自定义视觉服务网站生成分类器。 生成分类器模型后，可以使用自定义视觉服务进行图像分类。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 一组用于训练分类器的图像。 有关选择图像的提示，请参阅下文。

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>在 Azure 门户中创建自定义视觉资源

若要使用自定义视觉服务，需要在 Azure 门户中创建“自定义视觉训练和预测”资源。 填写“创建自定义视觉”[](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)页上的对话框窗口，以创建“训练和预测”资源。 

## <a name="create-a-new-project"></a>创建新项目

在 Web 浏览器中，导航到[自定义影像服务网页](https://customvision.ai)，然后选择“登录”  。 使用用于登录 Azure 门户的帐户登录。

![“登录”页的图像](./media/browser-home.png)


1. 若要创建首个项目，请选择“新建项目”  。 将出现“创建新项目”对话框  。

    ![“新建项目”对话框中包含名称、描述和域字段。](./media/getting-started-build-a-classifier/new-project.png)

1. 输入项目名称和描述。 然后选择一个资源组。 如果登录帐户与 Azure 帐户相关联，则“资源组”下拉列表将显示包含自定义影像服务资源的所有 Azure 资源组。 

   > [!NOTE]
   > 如果没有可用的资源组，请确认已使用登录 [Azure 门户](https://portal.azure.com/)时所用的同一帐户登录 [customvision.ai](https://customvision.ai)。 此外，请确认在自定义视觉门户中选择的“目录”与自定义视觉资源所在 Azure 门户中的目录相同。 在这两个站点中，可从屏幕右上角的下拉帐户菜单中选择目录。 

1. 选择“项目类型”下的“分类”   。 然后，在“分类类型”下，根据用例选择“多标签”或“多类”    。 多标签分类将任意数量的标记应用于图像（零个或多个），而多类分类将图像分类为单个类别（提交的每个图像将被分类为最有可能的标记）。 将能够根据需要稍后更改分类类型。

1. 接下来，选择一个可用域。 每个域都会针对特定类型的图像优化分类器，如下表所述。 将能够根据需要稍后更改域。

    |域|目的|
    |---|---|
    |__常规__| 针对各种图像分类任务进行优化。 如果其他域都不合适，或者不确定要选择哪个域，请选择“通用”域。 |
    |__食品__|针对餐厅菜肴的照片进行优化。 如果要对各种水果或蔬菜的照片进行分类，请使用“食品”域。|
    |__地标__|针对可识别的自然和人造地标进行优化。 在照片中的地标清晰可见的情况下，该域效果最佳。 即使照片中的人物稍微遮挡了地标，该域仍然有效。|
    |__零售__|针对购物目录或购物网站中的图像进行优化。 若想对连衣裙、裤子和衬衫进行精准分类，请使用此域。|
    |__压缩域__| 针对移动设备上实时分类的约束进行优化。 可导出压缩域生成的模型在本地运行。|

1. 最后，选择“创建项目”  。

## <a name="choose-training-images"></a>选择训练图像

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>上传和标记图像

在本部分，将上传图像并手动标记图像来帮助训练分类器。 

1. 若要添加图像，请单击“添加图像”按钮，然后选择“浏览本地文件”   。 选择“打开”以移至标记  。 标记选择将应用于选择上传的整个图像组，因此可以根据所需的标记更轻松地将图像上传到不同的组中。 还可在上传图像后更改单个图像的标记。

    ![添加图像控件位于左上角，以按钮形式显示在底部中央。](./media/getting-started-build-a-classifier/add-images01.png)


1. 若要创建标记，请在“我的标记”字段中输入文本，然后按 Enter 键  。 如果标记已存在，它会在下拉列表菜单中显示。 在多标签项目中，可以将多个标记添加到图像，但多类项目中只能添加一个标记。 若要完成上传图像，请使用“上传 [编号] 文件”按钮  。 

    ![“标记和上传”页面的图像](./media/getting-started-build-a-classifier/add-images03.png)

1. 上传图像后，选择“完成”  。

    ![进度条显示已完成的所有任务。](./media/getting-started-build-a-classifier/add-images04.png)

若要上传另一组图像，请返回到本部分顶部并重复上述步骤。

## <a name="train-the-classifier"></a>训练分类器

若要训练分类器，请选择“训练”按钮  。 分类器使用所有当前图像来创建模型，该模型可标识每个标记的视觉质量。

![网页标头工具栏右上角的训练按钮](./media/getting-started-build-a-classifier/train01.png)

此训练过程应该只需要几分钟的时间。 在此期间，会在“性能”选项卡显示有关训练过程的信息  。

![主要部分中带有训练对话的浏览器窗口](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>评估分类器

完成训练后，评估并显示该模型的性能。 自定义影像服务使用所提交的训练图像来计算精确度和召回率，采用的是名为 [k 倍交叉验证](https://en.wikipedia.org/wiki/Cross-validation_(statistics))的过程。 精确度和召回率是分类器有效性的两个不同的度量：

- 精确度表示已识别的正确分类的分数  。 例如，如果模型将 100 张图像识别为狗，实际上其中 99 张是狗，那么精确度为 99%。
- 召回率表示正确识别的实际分类的分数  。 例如，如果实际上有 100 张苹果的图像，并且该模型将 80 张标识为苹果，则召回率为 80%。

![训练结果显示分类器中的整体精确度和召回率，以及每个标签的精确度和召回率。](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>概率阈值

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>管理训练迭代

每次训练分类器时，都会创建一个新的迭代，其中包含自己更新的性能指标  。 可以在“性能”选项卡的左窗格中查看所有迭代  。在左侧窗格中，还可以找到“删除”按钮，如果迭代已过时，可以使用该按钮删除迭代  。 删除迭代时，会删除唯一与其关联的所有图像。

## <a name="next-steps"></a>后续步骤

在本快速入门中，已了解了如何使用自定义视觉服务网站创建和训练图像分类模型。 接下来，获取有关改进模型的迭代过程的详细信息。

> [!div class="nextstepaction"]
> [测试和重新训练模型](test-your-model.md)

