---
title: 通过自定义影像服务生成分类器 - Azure 认知服务 | Microsoft Docs
description: 了解如何使用自定义影像服务创建可识别照片中对象的分类器。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: c5183078d2f9d5eb16abef4f5df240f77eea6b8b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223363"
---
# <a name="how-to-build-a-classifier-with-custom-vision"></a>如何使用自定义影像服务生成分类器

若要使用自定义影像服务，必须先生成分类器。 本文介绍如何通过 Web 浏览器生成分类器。

## <a name="prerequisites"></a>先决条件

要生成分类器，首先必须具有：

- 有效的 [Microsoft 帐户](https://account.microsoft.com/account)或 Azure Active Directory OrgID（“工作或学校帐户”），这样方可登录 customvision.ai 并开始使用。

    > [!IMPORTANT] 
    > 目前不支持[国家云](https://www.microsoft.com/en-us/trustcenter/cloudservices/nationalcloud) Azure Active Directory (Azure AD) 用户通过 OrgID 登录。

- 用于训练分类器的一系列图像（每个标记至少要有 30 张图像）。

- 用于在训练后测试分类器的几张图像。

- 可选：与 Microsoft 帐户或 OrgID 关联的 Azure 订阅。 如果没有 Azure 订阅，可在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

    > [!IMPORTANT]
    > 如果没有 Azure 订阅，则只能创建受限试用项目。 如果有 Azure 订阅，则会在创建项目期间提示用户在 [Azure 门户](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision)中创建自定义影像服务训练和预测资源。   

## <a name="create-a-new-project"></a>创建新项目

要创建新项目，请按照以下步骤操作：

1. 在 Web 浏览器中，导航到[自定义影像服务网页](https://customvision.ai)。 选择“登录”开始使用该服务。

    ![“登录”页的图像](./media/getting-started-build-a-classifier/custom-vision-web-ui.png)

    > [!NOTE]
    > 登录自定义影像服务后，会出现一个项目列表。 除两个用于测试的“受限试用”项目外，其他项目与 Azure 资源相关联。 如果是 Azure 用户，则可看到拥有访问权的与 [Azure 资源](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#grant-access-to-resources)关联的所有项目。 

2. 若要创建首个项目，请选择“新建项目”。 创建首个项目时，用户需要同意服务条款。 选择该复选框，然后选择“我同意”按钮。 随即会显示“新建项目”对话框。

    ![“新建项目”对话框中包含名称、描述和域字段。](./media/getting-started-build-a-classifier/new-project.png)

3. 输入项目名称和描述。 然后选择一个可用域。 每个域都会针对特定类型的图像优化分类器，如下表所述：

    |域|目的|
    |---|---|
    |__常规__| 针对各种图像分类任务进行优化。 如果其他域都不合适，或者不确定要选择哪个域，请选择“通用”域。 |
    |__食品__|针对餐厅菜肴的照片进行优化。 如果要对各种水果或蔬菜的照片进行分类，请使用“食品”域。|
    |__地标__|针对可识别的自然和人造地标进行优化。 在照片中的地标清晰可见的情况下，该域效果最佳。 即使照片中的人物稍微遮挡了地标，该域仍然有效。|
    |__零售__|针对购物目录或购物网站中的图像进行优化。 若想对连衣裙、裤子和衬衫进行精准分类，请使用此域。|
    |__成人__|优化后能更好地识别成人内容和非成人内容。 例如，如果要屏蔽穿泳衣的人物图像，可通过此域生成自定义分类器来执行此操作。|
    |__压缩域__| 针对移动设备上实时分类的约束进行优化。 可导出压缩域生成的模型在本地运行。|

    稍后可按需更改域。

4. 选一个择资源组。 “资源组”下拉列表显示包含自定义影像服务资源的所有 Azure 资源组。 还可选择“受限试用”。 受限试用条目是非 Azure 用户可选择的唯一资源组。

    若要创建该项目，请选择“创建项目”。

## <a name="upload-and-tag-images"></a>上传和标记图像

1. 若要将图像添加到分类器，请使用“添加图像”按钮，然后选择“浏览本地文件”。 选择“打开”以移至标记。

    > [!TIP]
    > 选择图像后，必须标记图像。 该标记将应用于选择上传的图像组，因此依据计划使用的标记上传图像可能会更容易。 还可在标记和上传图像后更改所选图像的标记。

    > [!TIP]
    > 上传在摄像角度、光线、背景、类型、样式、组、大小等方面不同的图像。请使用各种类型的照片，确保分类器不偏重某方面，可涵盖所有方面。

    自定义影像服务接受 .jpg、.png 和 .bmp 格式的训练图像，每张图像不得超过 6 MB。 （预测图像不得超过 4 MB。）建议图像最短边为 256 像素。 对于最短边不足 256 像素的图像，自定义影像服务会进行扩展。

    ![添加图像控件位于左上角，以按钮形式显示在底部中央。](./media/getting-started-build-a-classifier/add-images01.png)

    >[!NOTE] 
    > REST API 可从 URL 加载训练图像。

2. 若要设置标记，请在“我的标记”字段输入文字，然后使用 + 按钮。 若要上传图像并进行标记，请使用“上传 [编号] 文件”按钮。 可为图像添加多个标记。 

    > [!NOTE]
    > 上传时间因所选图像的数量和大小而异。

    ![“标记和上传”页面的图像](./media/getting-started-build-a-classifier/add-images03.png)

3. 上传图像后，选择“完成”。

    ![进度条显示已完成的所有任务。](./media/getting-started-build-a-classifier/add-images04.png)

4. 若要上传另一组图像，请返回步骤 1。 例如，若要区分狗和小马，请上传有小马的图像并进行标记。

## <a name="train-and-evaluate-the-classifier"></a>训练和评估分类器

若要训练分类器，请选择“训练”按钮。

![训练按钮靠近浏览器窗口的右上角。](./media/getting-started-build-a-classifier/train01.png)

训练分类器只需要几分钟的时间。 在此期间，会显示有关训练过程的信息。

![训练按钮靠近浏览器窗口的右上角。](./media/getting-started-build-a-classifier/train02.png)

训练后，会显示“性能”。 精确度和召回指标会基于自动测试指出分类器的优异程度。 自定义影像服务使用所提交的训练图像来计算这些指标，采用的是名为 [k 倍交叉验证](https://en.wikipedia.org/wiki/Cross-validation_(statistics))的过程。

![训练结果显示分类器中的整体精确度和召回率，以及每个标签的精确度和召回率。](./media/getting-started-build-a-classifier/train03.png)

> [!NOTE] 
> 每次选择“训练”按钮时，都会创建一个新的分类器迭代。 可在“性能”选项卡中查看所有旧迭代，且可删除任何可能已过时的迭代。 删除迭代时，最终会删除唯一与其关联的所有图像。

分类器使用所有图像来创建标识每个标记的模型。 为测试模型的质量，分类器会在模型上尝试使用每个图像，以查看模型能找到什么内容。

之后会显示分类器质量结果。

|术语|定义|
|---|---|
|__精度__|对图像进行分类时，分类器对图像进行正确分类的可能性有多大？ 在用于训练分类器（狗和小马）的所有图像中，模型得出正确结果的比率是多少？ 100 个图像中有 99 个得到正确标记，精确度为 99％。|
|__召回率__|在应正确分类的所有图像中，分类器正确识别了多少图像？ 100％ 的召回率意味着，如果用于训练分类器的图像中包含 38 只狗，则分类器找到了 38 只狗。|

## <a name="next-steps"></a>后续步骤

[测试和重新训练模型](test-your-model.md)

