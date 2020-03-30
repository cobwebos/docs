---
title: 使用视频索引器进行动画角色检测
titleSuffix: Azure Media Services
description: 本主题演示如何将动画角色检测与视频索引器一起使用。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989903"
---
# <a name="animated-character-detection-preview"></a>动画角色检测（预览）

Azure 媒体服务视频索引器支持通过与[认知服务自定义视觉](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)集成来检测、分组和识别动画内容中的字符。 此功能可通过门户和 API 获得。

使用特定动画模型上传动画视频后，视频索引器提取关键帧、检测这些帧中的动画角色、对相似角色进行分组以及选择最佳示例。 然后，它将分组字符发送到自定义视觉，根据所训练的模型标识字符。 

在开始训练模型之前，将无名地检测到字符。 添加名称并训练模型时，视频索引器将识别字符并相应地命名它们。

## <a name="flow-diagram"></a>流程图

下图演示了动画角色检测过程的流程。

![流程图](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>帐户

根据视频索引器帐户的类型，可以使用不同的功能集。 有关如何将帐户连接到 Azure 的信息，请参阅[创建连接到 Azure 的视频索引器帐户](connect-to-azure.md)。

* 试用帐户：视频索引器使用内部自定义视觉帐户创建模型并将其连接到您的视频索引器帐户。 
* 付费帐户：将自定义视觉帐户连接到视频索引器帐户（如果您还没有帐户，则需要先创建一个帐户）。

### <a name="trial-vs-paid"></a>试用 vs. 已付款

|功能|试用|付费|
|---|---|---|
|自定义愿景帐户|由视频索引器在幕后管理。 |您的自定义视觉帐户已连接到视频索引器。|
|动画模型数|一种|每个帐户最多 100 个型号（自定义视觉限制）。|
|模型定型|视频索引器为新字符训练模型，以补充现有字符的示例。|当帐户所有者准备好进行更改时，他们将训练模型。|
|自定义视觉的高级选项|无法访问自定义视觉门户。|您可以在自定义视觉门户中自行调整模型。|

## <a name="use-the-animated-character-detection-with-portal"></a>将动画角色检测与门户一起使用 

本节介绍开始使用动画角色检测模型所需的步骤。 

由于在试用帐户中，自定义视觉集成由视频索引器管理，因此您可以开始创建和使用动画角色模型，并跳过以下部分（"连接自定义视觉帐户"）。

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>连接自定义愿景帐户（仅限付费帐户）

如果您拥有视频索引器付费帐户，则需要首先连接自定义视觉帐户。 如果您还没有自定义视觉帐户，请创建一个帐户。 有关详细信息，请参阅[自定义愿景](../../cognitive-services/custom-vision-service/home.md)。

> [!NOTE]
> 两个帐户需要位于同一区域。 日本区域目前不支持自定义愿景集成。

#### <a name="connect-a-custom-vision-account-with-api"></a>使用 API 连接自定义视觉帐户 

按照以下步骤将自定义视觉帐户连接到视频索引器，或更改当前连接到视频索引器的自定义视觉帐户：

1. 浏览到[www.customvision.ai](https://www.customvision.ai)并登录。
1. 复制以下键： 

    * 培训密钥（针对培训资源）
    * 预测键（用于预测资源）
    * 终结点 
    * 预测资源 ID
    
    > [!NOTE]
    > 要提供所有密钥，您需要在自定义视觉中具有两个单独的资源，一个用于培训，一个用于预测。
1. 浏览并登录到[视频索引器](https://vi.microsoft.com/)。
1. 单击页面右上角的问号，然后选择**API 参考**。
1. 通过单击 **"产品"** 选项卡，确保您已订阅 API 管理。如果连接了 API，则可以继续执行下一步，否则，请订阅。 
1. 在开发人员门户上，单击 **"完整的 API 引用"** 并浏览到**操作**。  
1. 选择 **"连接自定义视觉帐户（预览）"，** 然后单击"**试用"。**
1. 填写所需的字段以及访问令牌，然后单击"**发送**"。 

    有关如何获取视频索引器访问令牌的详细信息，请访问[开发人员门户](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)，并查看[相关文档](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)。  
1. 一旦呼叫返回 200 OK 响应，您的帐户将连接。
1. 要通过浏览到[视频索引器](https://vi.microsoft.com/)（ 门户来验证您的连接：
1. 单击右上角**的内容模型自定义**按钮。
1. 转到 **"动画角色"** 选项卡。
1. 单击"自定义视觉"中的"管理模型"*后，您将被转移到您刚刚连接的自定义视觉帐户。

> [!NOTE]
> 目前，仅支持通过视频索引器创建的模型。 通过自定义视觉创建的模型将不可用。 此外，最佳做法是仅通过视频索引器平台编辑通过视频索引器创建的模型，因为通过自定义视觉所做的更改可能会导致意外的结果。

### <a name="create-an-animated-characters-model"></a>创建动画角色模型

1. 浏览到[视频索引器](https://vi.microsoft.com/)网站并登录。
1. 单击页面右上角的内容模型自定义按钮。

    ![内容模型自定义](./media/animated-characters-recognition/content-model-customization.png)
1. 转到模型自定义部分中的 **"动画角色"** 选项卡。
1. 单击 **"添加模型**"。
1. 命名模型，然后单击 Enter 以保存名称。

> [!NOTE]
> 最佳做法是为每个动画系列创建一个自定义视觉模型。 

### <a name="index-a-video-with-an-animated-model"></a>使用动画模型为视频编制索引

1. 单击顶部菜单中的 **"上传**"按钮。
1. 选择要上传的视频（从文件或 URL）。
1. 单击 **"高级"选项**。
1. 在 **"人/动画角色**"下选择**动画模型**。
1. 如果您有一个模型，它将自动选择，如果您有多个模型，则可以从下拉菜单中选择相关的模型。
1. 点击上传。
1. 为视频编制索引后，您将在 **"见解"** 窗格中的 **"动画角色**"部分中看到检测到的字符。

> [!NOTE] 
> 在标记和培训模型之前，所有动画角色都将命名为"未知#X"。 训练模型后，它们也将被识别。

### <a name="customize-the-animated-characters-models"></a>自定义动画角色模型

1. 标记并训练模型。

    1. 通过编辑检测到的字符的名称来标记其检测到的字符。 一旦将角色训练到模型中，它将被识别为使用该模型索引的下一个视频。 
    1. 要在视频中标记动画角色，请转到 **"见解"** 选项卡，然后单击窗口右上角的 **"编辑"** 按钮。
    1. 在 **"见解"** 窗格中，单击检测到的任何动画角色，并将其名称从"未知#X"（或以前分配给该角色的名称）更改。
    1. 键入新名称后，单击新名称旁边的复选图标。 这将在视频索引器中保存模型中的新名称。
    1. 完成所有所需名称的编辑后，需要训练模型。

        打开自定义页面并单击 **"动画角色"** 选项卡，然后单击 **"训练"** 按钮以训练模型。
         
        如果您有付费帐户，则可以单击 **"客户愿景"链接中的"管理模型**"（如下所示）。 然后，您将被转发到自定义**视觉**中的模型页面。
 
        ![内容模型自定义](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. 经过培训，任何将用该模型编制索引或重新编制索引的视频都将识别经过训练的角色。 
    有权访问其自定义视觉帐户的付费帐户可以看到模型和标记的图像。 详细了解在[自定义视觉中改进分类器](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier)。

1. 删除动画角色。

    1. 要删除视频见解中的动画角色，请转到 **"见解"** 选项卡，然后单击窗口右上角的 **"编辑"** 按钮。
    1. 选择动画角色，然后单击其名称下的 **"删除**"按钮。

    > [!NOTE]
    > 这将从此视频中删除见解，但不会影响模型。

1. 删除模型。

    1. 单击顶部菜单上**的内容模型自定义**按钮，然后转到 **"动画字符"** 选项卡。
    1. 单击要删除的模型右侧的省略号图标，然后单击删除按钮。
    
    * 付费帐户：模型将与视频索引器断开连接，您将无法重新连接它。
    * 试验帐户：该模型也将从海关愿景中删除。 
    
        > [!NOTE]
        > 在试用帐户中，您只能使用一个模型。 删除后，无法训练其他模型。

## <a name="use-the-animated-character-detection-with-api"></a>将动画字符检测与 API 一起使用 

1. 连接自定义视觉帐户。

    如果您拥有视频索引器付费帐户，则需要首先连接自定义视觉帐户。 <br/>
    如果您还没有自定义视觉帐户，请创建一个帐户。 有关详细信息，请参阅[自定义愿景](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)。

    [使用 API 连接自定义视觉帐户](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)。
1. 创建动画角色模型。

    使用[创建动画模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)API。
1. 索引或重新索引视频。

    使用[重新索引](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)API。 
1. 自定义动画角色模型。

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

* 目前，东亚地区不支持"动画识别"功能。
* 如果视频质量较差，则可能无法正确识别视频中看似小或远的字符。
* 建议使用每组动画角色（例如，动画系列）的模型。

## <a name="next-steps"></a>后续步骤

[视频索引器概述](video-indexer-overview.md)
