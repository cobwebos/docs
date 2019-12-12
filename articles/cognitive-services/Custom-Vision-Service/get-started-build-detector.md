---
title: 有关生成对象检测器的快速入门 - 自定义视觉服务
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将了解如何使用自定义视觉服务网站创建图像分类模型。
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: anroth
ms.openlocfilehash: 0d9c175db1370fe07b3278a46d910c59d81df860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969915"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>快速入门：如何使用自定义视觉生成对象检测器

本快速入门介绍如何通过自定义视觉网站生成对象检测器。 生成检测器模型后，可以使用自定义视觉服务进行对象检测。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- 一组用于训练检测器模型的图像。 可以使用 GitHub 上的一组[示例图像](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images)。 或者，可以根据下面的提示选择你自己的图像。

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>在 Azure 门户中创建自定义视觉资源

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>创建新项目

在 Web 浏览器中，导航到[自定义影像服务网页](https://customvision.ai)，然后选择“登录”  。 使用用于登录 Azure 门户的帐户登录。

![“登录”页的图像](./media/browser-home.png)


1. 若要创建首个项目，请选择“新建项目”  。 将出现“创建新项目”对话框  。

    ![“新建项目”对话框中包含名称、描述和域字段。](./media/get-started-build-detector/new-project.png)

1. 输入项目名称和描述。 然后选择一个资源组。 如果登录帐户与 Azure 帐户相关联，则“资源组”下拉列表将显示包含自定义影像服务资源的所有 Azure 资源组。 

   > [!NOTE]
   > 如果没有可用的资源组，请确认已使用登录 [Azure 门户](https://portal.azure.com/)时所用的同一帐户登录 [customvision.ai](https://customvision.ai)。 此外，请确认在自定义视觉门户中选择的“目录”与自定义视觉资源所在 Azure 门户中的目录相同。 在这两个站点中，可从屏幕右上角的下拉帐户菜单中选择目录。 

1. 选择“项目类型”下的“对象检测”。  

1. 接下来，选择一个可用域。 每个域都会针对特定类型的图像优化检测器，如下表所述。 将能够根据需要稍后更改域。

    |域|目的|
    |---|---|
    |__常规__| 已针对各种对象检测任务进行优化。 如果其他域都不合适，或者不确定要选择哪个域，请选择“通用”域。 |
    |__徽标__|已针对在图像中查找品牌徽标进行优化。|
    |__压缩域__| 已针对移动设备上实时对象检测的约束进行优化。 可导出压缩域生成的模型在本地运行。|

1. 最后，选择“创建项目”  。

## <a name="choose-training-images"></a>选择训练图像

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>上传和标记图像

在本部分，你将上传图像并手动标记图像来帮助训练检测器。 

1. 若要添加图像，请单击“添加图像”按钮，然后选择“浏览本地文件”   。 选择“打开”以上传图像。 

    ![添加图像控件位于左上角，以按钮形式显示在底部中央。](./media/get-started-build-detector/add-images.png)

1. UI 的“未标记”部分会显示已上传的图像。  下一步是手动标记你希望检测器通过学习识别的对象。 单击第一个图像打开“标记”对话框窗口。 

    ![“未标记”部分中已上传的图像](./media/get-started-build-detector/images-untagged.png)

1. 单击并拖动图像中对象周围的矩形。 然后，使用 **+** 按钮输入新的标记名称，或者从下拉列表中选择现有的标记。 必须标记要检测的对象的每个实例，这一点非常重要，因为检测器在训练中使用未标记的背景区域作为负面示例。 完成标记后，单击右侧的箭头保存标记并转到下一个图像。

    ![使用矩形选择控件来标记对象](./media/get-started-build-detector/image-tagging.png)

若要上传另一组图像，请返回到本部分顶部并重复上述步骤。

## <a name="train-the-detector"></a>训练检测器

若要训练检测器模式，请选择“训练”按钮。  检测器将使用所有当前图像及其标记来创建用于识别每个标记对象的模型。

![网页标头工具栏右上角的训练按钮](./media/getting-started-build-a-classifier/train01.png)

此训练过程应该只需要几分钟的时间。 在此期间，会在“性能”选项卡显示有关训练过程的信息  。

![主要部分中带有训练对话的浏览器窗口](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>评估检测器

完成训练后，将计算并显示模型的性能。 自定义视觉服务使用提交用于训练的图像来计算精度、召回率和平均精度。 精度和召回率是检测器有效性的两个不同度量值：

- 精确度表示已识别的正确分类的分数  。 例如，如果模型将 100 张图像识别为狗，实际上其中 99 张是狗，那么精确度为 99%。
- 召回率表示正确识别的实际分类的分数  。 例如，如果实际上有 100 张苹果的图像，并且该模型将 80 张标识为苹果，则召回率为 80%。

![训练结果将显示总体精度和召回率，以及平均精度。](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>概率阈值

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>管理训练迭代

每次训练检测器时，都会创建一个新的迭代，其中包含其自身的已更新性能指标  。 可以在“性能”选项卡的左窗格中查看所有迭代  。在左侧窗格中，还可以找到“删除”按钮，如果迭代已过时，可以使用该按钮删除迭代  。 删除迭代时，会删除唯一与其关联的所有图像。

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用自定义视觉网站创建和训练对象检测器模型。 接下来，获取有关改进模型的迭代过程的详细信息。

> [!div class="nextstepaction"]
> [测试和重新训练模型](test-your-model.md)

