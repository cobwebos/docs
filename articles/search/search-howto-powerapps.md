---
title: 教程：从 Power Apps 查询
titleSuffix: Azure Cognitive Search
description: 此分步指南介绍了如何构建 Power App 来连接 Azure 认知搜索索引、发送查询以及呈现结果。
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 04434753949f0e00eaf99610199cc4997ef53caf
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751066"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>教程：从 Power Apps 查询认知搜索索引

利用 Power Apps 的快速应用程序开发环境，针对 Azure 认知搜索中的可搜索内容创建自定义应用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 连接到 Azure 认知搜索
> * 设置查询请求
> * 在画布应用中将结果可视化

如果你没有 Azure 订阅，请在开始之前建立一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* [Power Apps 帐户](http://make.powerapps.com)

* [Hotels-sample 索引](search-get-started-portal.md)

* [查询 API 密钥](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 - 创建自定义连接器

Power Apps 中的连接器是一个数据源连接。 在此步骤中，你将创建一个自定义连接器，用以连接到云中的搜索索引。

1. [登录](http://make.powerapps.com)到 Power Apps。

1. 在左侧，展开“数据” > “自定义连接器”。 
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="“自定义连接器”菜单" border="true":::

1. 依次选择“+新建自定义连接器”、“从空白开始创建”。 

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="“从空白开始创建”菜单" border="true":::

1. 为自定义连接器命名（例如 AzureSearchQuery），然后单击“继续”。

1. 在“常规”页中输入信息：

   * 图标背景色（例如 #007ee5）
   * 说明（例如“用于连接 Azure 认知搜索的连接器”）
   * 在“主机”中，需要输入搜索服务 URL（例如 `<yourservicename>.search.windows.net`）
   * 对于“基 URL”，只需输入“/”

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="“常规信息”对话框" border="true":::

1. 在“安全”页中，将“API 密钥”设置为“身份验证类型”，并将参数标签和参数名称都设置为“api-key”。 对于“参数位置”，请选择“标头”，如下所示。

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="“身份验证类型”选项" border="true":::

1. 在“定义”页中，选择“+ 新建操作”以创建用于查询索引的操作。 输入值“查询”作为操作 ID 的摘要和名称。 输入说明，例如“查询搜索索引”。

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="“新建操作”选项" border="true":::

1. 向下滚动。 在“请求”中，选择“+ 从示例导入”按钮，以配置对你的搜索服务的查询请求：

   * 选择谓词 `GET`

   * 对于“URL”，请输入对你的搜索索引的示例查询（`search=*` 返回所有文档，`$select=` 可让你选择字段）。 “API 版本”是必需的。 完全指定后，URL 可能如下所示：`https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * 对于“标头”，请键入 `Content-Type`。 

     Power Apps 将使用语法从查询中提取参数。 请注意，我们显式定义了搜索字段。 

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="从示例导入" border="true":::

1. 单击“导入”以自动填充请求。 通过单击每个参数旁边的“…”符号来完成参数元数据的设置。 每次更新参数后，可以单击“后退”返回到“请求”页。

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="“从示例导入”对话框" border="true":::

1. 对于 search：将 `*` 设置为默认值，将 required 设置为 False，将 visibility 设置为 none。  

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="搜索参数元数据" border="true":::

1. 对于 select：将 `HotelName,Description,Address/City` 设置为默认值，将 required 设置为 False，将 visibility 设置为 none。   

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="选择参数元数据" border="true":::

1. 对于 api-version：将 `2020-06-30` 设置为默认值，将 required 设置为 True，将 visibility 设置为 internal。   

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="版本参数元数据" border="true":::

1. 对于 Content-Type：设置为 `application/json`。

1. 进行这些更改后，切换到“Swagger 编辑器”视图。 在 parameters 节中，应会看到以下配置：

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. 返回到“3.请求”步骤，并向下滚动到“响应”部分。 单击“添加默认响应”。 此操作至关重要，因为它会帮助 Power Apps 了解响应的架构。 

1. 粘贴示例响应。 通过 Azure 门户中的“搜索浏览器”可以轻松捕获示例响应。 在“搜索浏览器”中，应该输入针对请求所提供的相同查询，但还要添加 $top=2，以将结果限制为仅包括两个文档：`search=*&$select=HotelName,Description,Address/City&$top=2`。 

   Power Apps 只需要几条结果即可检测到架构。

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > 可以输入的 JSON 响应存在字符限制，因此建议在粘贴 JSON 之前先将其简化。 响应的架构和格式比值本身更重要。 例如，可以简化“说明”字段，使其仅包含第一个句子。

1. 单击右上角的“创建连接器”。

## <a name="2---test-the-connection"></a>2 - 测试连接

首次创建连接器时，需从“自定义连接器”列表中将其重新打开，以便对其进行测试。 以后如果做出了其他更新，可以在向导中进行测试。

需要使用一个[查询 API 密钥](search-security-api-keys.md#find-existing-keys)来完成此任务。 每次创建连接时（无论是针对测试运行还是针对应用中包含的内容），连接器都需要用于连接到 Azure 认知搜索的查询 API 密钥。

1. 在最左侧，单击“自定义连接器”。

1. 按名称（在本教程中为“AzureSearchQuery”）搜索连接器。

1. 选择该连接器，展开操作列表，然后选择“查看属性”。

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="查看属性" border="true":::

1. 选择右上角的“编辑”。

1. 选择“4.测试”以打开测试页。

1. 在“测试操作”中，单击“+ 新建连接”。

1. 输入一个查询 API 密钥。 这是一个对索引进行只读访问的 Azure 认知搜索查询。 可以在 Azure 门户中[找到该密钥](search-security-api-keys.md#find-existing-keys)。 

1. 在“操作”中，单击“测试操作”按钮。 如果测试成功，应会看到 200 状态，并且在响应正文中，应会看到描述搜索结果的 JSON。

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="JSON 响应" border="true":::

## <a name="3---visualize-results"></a>3 - 将结果可视化

在此步骤中，创建一个带有搜索框、搜索按钮和结果显示区域的 Power App。 该 Power App 将连接到最近创建的自定义连接器，以从 Azure 搜索中获取数据。

1. 在左侧，展开“应用” > “+ 新建应用” > “画布”。  

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="创建画布应用" border="true":::

1. 选择应用程序的类型。 对于本教程，请创建采用“手机布局”的空白应用。  此时会显示“Power Apps Studio”。

1. 进入 Studio 后，选择“数据源”选项卡，然后单击刚刚创建的新连接器。 在本例中，该连接器名为 AzureSearchQuery。 单击“添加连接”。

   输入查询 API 密钥。

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="连接连接器" border="true":::

    现在，AzureSearchQuery 是可从应用程序使用的数据源。

1. 在“插入”选项卡上，将几个控件添加到画布。

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="插入控件" border="true":::

1. 插入以下元素：

   * 值为“查询:”的一个文本标签
   * 一个文本输入元素（将其命名为 txtQuery，默认值："*"）
   * 带有文本“搜索”的一个按钮 
   * 垂直库（将其命名为 galleryResults）

    画布应如下所示：

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="控件布局" border="true":::

1. 要使“搜索”按钮发出查询，请将以下操作粘贴到 OnSelect 中： 

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   以下屏幕截图显示了 OnSelect 操作的公式栏。

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="OnSelect 按钮" border="true":::

   此操作会导致按钮使用 txtQuery 文本框中的文本作为查询词，使用搜索查询结果更新名为 azResult 的新集合。 

   > [!NOTE]
   > 如果收到公式语法错误“函数 'ClearCollect' 包含一些无效函数”，请尝试以下解决方法：
   > 
   > * 首先，请确保连接器引用正确。 清除连接器名称，然后开始键入连接器的名称。 Intellisense 应会建议正确的连接器和谓词。
   > 
   > * 如果该错误依然出现，请删除并重新创建连接器。 如果有多个连接器实例，应用可能会使用错误的实例。
   > 

1. 将“垂直库”控件链接到完成上一步骤时创建的 azResult 集合。 

   选择库控件，并在属性窗格中执行以下操作。

   * 将“DataSource”设置为“azResult”。
   * 根据索引中的数据类型，选择合适的布局。 在本例中，我们使用了“标题、副标题和正文”布局。
   * 单击“编辑字段”，并选择要可视化的字段。

    由于我们在定义连接器时提供了示例结果，因此该应用能够识别到索引中可用的字段。
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="库字段" border="true":::   
 
1. 按 F5 预览应用。  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="最终的应用" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。

## <a name="next-steps"></a>后续步骤

使用 Power Apps 可以快速开发自定义应用。 了解如何连接到搜索索引后，接下来请详细了解如何在自定义 Power App 中创建丰富的可视化体验。

> [!div class="nextstepaction"]
> [Power Apps 学习目录](https://docs.microsoft.com/powerapps/learning-catalog/get-started)

