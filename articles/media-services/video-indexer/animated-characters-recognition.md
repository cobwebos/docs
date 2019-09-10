---
title: 视频索引器的动画字符检测
titleSuffix: Azure Media Services
description: 本主题演示如何通过视频索引器使用动画字符检测。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/05/2019
ms.author: juliako
ms.openlocfilehash: d45842a4c61f871a479c91f7b192de8e567f9821
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862013"
---
# <a name="animated-character-detection-preview"></a>动画字符检测（预览）

Azure 媒体服务视频索引器支持通过与[认知服务自定义视觉](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)集成来检测、分组和识别动画内容中的字符。 此功能可通过门户和 API 使用。

使用特定动画模型上传动画视频后，视频索引器将提取关键帧，检测这些帧中的动画字符，将类似字符分组，然后选择最佳示例。 然后，它将分组字符发送到自定义视觉，以根据它所定型的模型来标识字符。 

在开始对模型进行定型之前，将在 namelessly 中检测到字符。 添加名称和训练模型时，视频索引器将识别这些字符并对它们进行相应的命名。

## <a name="flow-diagram"></a>流程图

下图演示了动画字符检测过程的流动。

![流程图](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>帐户

根据视频索引器帐户的类型，可以使用不同的功能集。 若要了解如何将帐户连接到 Azure，请参阅[创建连接到 azure 的视频索引器帐户](connect-to-azure.md)。

* 试用帐户：视频索引器使用内部自定义视觉帐户创建模型并将其连接到视频索引器帐户。 
* 付费帐户：将自定义视觉帐户连接到视频索引器帐户（如果还没有帐户，则需要先创建帐户）。

### <a name="trial-vs-paid"></a>试用版和付费版

|功能|试用版|付费版|
|---|---|---|
|自定义视觉帐户|由视频索引器在后台进行管理。 |自定义视觉帐户已连接到视频索引器。|
|动画模型的数目|一种|每个帐户最多100个型号（自定义视觉限制）。|
|定型模型|视频索引器训练新字符的模型其他现有字符的示例。|当客户准备好进行更改时，帐户所有者训练该模型。|
|自定义视觉中的高级选项|无权访问自定义视觉门户。|您可以在自定义视觉门户中自行调整模型。|

## <a name="use-the-animated-character-detection-with-portal"></a>使用门户中的动画字符检测 

本部分介绍了开始使用动画字符检测模型需要执行的步骤。

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>连接自定义视觉帐户（仅限付费帐户）

如果你拥有视频索引器付费帐户，则需要先连接自定义视觉帐户。 如果还没有自定义视觉帐户，请创建一个。 有关详细信息，请参阅[自定义视觉](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)。

### <a name="create-an-animated-characters-model"></a>创建动画字符模型

1. 浏览到[视频索引器](https://vi.microsoft.com/)网站并登录。
1. 在页面的右上角，单击 "内容模型自定义" 按钮。

    ![内容模型自定义](./media/animated-characters-recognition/content-model-customization.png)
1. 中转到 "模型自定义" 部分中的 "**动画字符**" 选项卡。
1. 单击 "**添加模型**"。
1. 为模型命名并单击 enter 以保存名称。

> [!NOTE]
> 最佳做法是为每个动画序列提供一个自定义远景模型。 

### <a name="index-a-video-with-an-animated-model"></a>使用动画模型为视频编制索引

1. 单击顶部菜单中的 "上**传**" 按钮。
1. 选择要上传的视频（从文件或 URL）。
1. 单击 "**高级选项**"。
1. 在 "**人物/动画字符**" 下，选择**动画模型**。
1. 如果有一个模型，则会自动选择该模型，如果有多个模型，则可以从下拉菜单中选择相关的模型。
1. 单击 "上传"。
1. 为视频编制索引后，会在 "**见解**" 窗格的 "**动画字符**" 部分看到检测到的字符。

> [!NOTE] 
> 在对模型进行标记和定型之前，所有的动画字符都将命名为 "Unknown #X"。 训练模型后，还会识别它们。

### <a name="customize-the-animated-characters-models"></a>自定义动画字符模型

1. 标记和训练模型。

    1. 通过编辑检测到的名称来标记该字符。 一旦将字符训练到了模型中，就会将其识别为使用该模型建立索引的下一个视频。 
    1. 若要对视频中的动画字符进行标记，请参阅 "**见解**" 选项卡，并单击窗口右上角的 "**编辑**" 按钮。
    1. 在 "**见解**" 窗格中，单击任何检测到的动画字符，并将其名称从 "未知 #X" （或之前分配给该字符的名称）中。
    1. 键入新名称后，单击新名称旁边的复选图标。 这会将新名称保存在视频索引器的模型中。
    1. 完成编辑所需的所有名称后，需要为模型定型。

        * 试用帐户：打开 "自定义" 页，单击 "动画字符" 选项卡，然后单击所拥有的**定型**按钮模型。
        * 付费帐户：打开 "自定义" 页，然后单击 "动画字符" 选项卡。对于相关的模型，请单击 "**编辑自定义视觉**" 链接。 然后，将被转发到自定义视觉中的模型页。 单击此处的 "训练" 按钮，为模型定型。 
    1. 训练后，任何将通过该模型建立索引或重新编制索引的视频都将识别定型字符。 
    有权访问其自定义视觉帐户的付费帐户可在此处查看模型和标记图像。 详细了解如何[在自定义视觉中改进分类器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)。

1. 删除动画字符。

    1. 若要删除视频见解中的动画字符，请在 "**见解**" 选项卡上，单击窗口右上角的 "**编辑**" 按钮。
    1. 选择动画字符，然后单击其名称下面的 "**删除**" 按钮。

    > [!NOTE]
    > 这将删除此视频的见解，但不会影响模型。

1. 删除模型。

    1. 单击顶部菜单上的 "**内容模型自定义**" 按钮，然后选择 "**动画字符**" 选项卡。
    1. 单击要删除的模型右侧的省略号图标，然后在 "删除" 按钮上单击。
    
    * 付费帐户：模型将与视频索引器断开连接，并且你将无法重新连接它。
    * 试用帐户：模型也将从海关远景中删除。 
    
        > [!NOTE]
        > 在试用帐户中，只有一个可以使用的模型。 删除后，将无法对其他模型定型。

## <a name="use-the-animated-character-detection-with-api"></a>使用 API 的动画字符检测 

1. 连接自定义视觉帐户。

    如果你拥有视频索引器付费帐户，则需要先连接自定义视觉帐户。 <br/>
    如果还没有自定义视觉帐户，请创建一个。 有关详细信息，请参阅[自定义视觉](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)。

    [使用 API 连接自定义视觉帐户](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)。
1. 创建 "动画字符" 模型。

    使用[创建动画模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)API。
1. 为视频编制索引或重新编制索引。

    使用[重新索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)API。 
1. 自定义动画字符模型。

    使用[训练动画模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag)API。

### <a name="view-the-output"></a>查看输出

查看生成的 JSON 文件中的动画字符。

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>限制

* 目前，远东地区不支持 "动画标识" 功能。
* 如果视频质量较差，可能无法正确识别在视频中显示较小或更多的字符。
* 建议为每组经过动画处理的字符使用一个模型（例如，根据动画系列）。

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)
