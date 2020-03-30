---
title: 如何从电源应用查询 Azure 认知搜索
titleSuffix: Azure Cognitive Search
description: 有关如何创建认知搜索的自定义连接器以及如何从 Power App 可视化的分步指南
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: c246f8652227a5ad2c0798880e530d6039cdeea8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385106"
---
# <a name="how-to-query-a-cognitive-search-index-from-power-apps"></a>如何从电源应用查询认知搜索索引

本文档演示如何创建 Power Apps 自定义连接器，以便从搜索索引检索搜索结果。 它还演示如何发出搜索查询并可视化来自 Power App 的结果。 

## <a name="prerequisites"></a>先决条件
*    能够创建自定义连接器的"为应用应用帐户访问供电"。
*    我们假设您已经创建了 Azure 搜索索引。

## <a name="create-a-custom-connector-to-query-azure-search"></a>创建自定义连接器以查询 Azure 搜索

有两个主要步骤是使用显示 Azure 认知搜索结果的 PowerApp。 首先，让我们创建一个可以查询搜索索引的连接器。 [在下一节中](#visualize-results-from-the-custom-connector)，我们将更新您的 Power Apps 应用程序，以可视化连接器返回的结果。

1. 转到[make.powerapps.com](http://make.powerapps.com)并**登录**。

1. 搜索**数据** > **自定义连接器**
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="自定义连接器菜单" border="true":::

1. 单击 **= 新建自定义连接器**，然后选择**从空白创建**。

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="从空白菜单创建" border="true":::

1. 为自定义连接器指定名称。 （即*Azure 搜索查询*），然后单击"**继续**"。 这将启动一个向导来创建新连接器。

1. 在"常规页面"中输入信息。

    - 图标背景颜色（例如，#007ee5）
    - 说明（例如，"Azure 认知搜索的连接器"）
    - 在主机中，您需要输入搜索服务 URL（例如， `<yourservicename>.search.windows.net`
    - 对于基本 URL，只需输入"/"
    
    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="一般信息对话" border="true":::

1. 在"安全页"中，将*API 密钥*设置为**身份验证类型**，将参数标签和参数名称字段设置为*api 键*。 对于**参数位置**，选择*如下所示的"标题*"。
 
    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="身份验证类型选项" border="true":::

1. 在"定义页"中，选择 **"新建操作"** 以创建将查询索引的操作。 输入摘要的值"查询"和操作 ID 的名称。 输入类似 *"查询搜索索引"* 的描述。
 
    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="新的操作选项" border="true":::


1. 按 **" 从示例导入**"按钮定义参数和标头。 接下来，您将定义查询请求。  

    * 选择动词`GET`
    * 对于 URL，请输入搜索索引的示例查询，例如：
       
    >https://yoursearchservicename.search.windows.net/indexes/yourindexname/docs?search=[&api 版本=2019-05-06-预览
    

    **Power Apps**将使用语法从查询中提取参数。 请注意，我们显式定义了搜索字段。 

    :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="从示例导入" border="false":::

1.  单击"**导入**"可自动预填"请求"对话框。

    :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="从示例对话导入" border="false":::


1. 通过单击 **...** 每个参数旁边的符号。

    - 对于*搜索*：设置为`*`**默认值**，**将所需**设置为*false，* 并将**可见性**设置为*none*。 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="搜索参数元数据" border="true":::

    - 对于*api 版本*：`2019-05-06-Preview`设置为**默认值**，将可见性设置为内部 **，** 并将**可见性**设置为*True*。  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="版本参数元数据" border="true":::

    - 同样，对于*api 键*，根据需要设置**required**它，具有*内部***可见性**。 输入搜索服务 API 密钥作为**默认值**。
    
    进行这些更改后，将切换至 **"斯瓦格编辑器"** 视图。 在参数部分中，您应该看到以下配置：    

    ```
          parameters:
          - {name: search, in: query, required: false, type: string, default: '*'}
          - {name: api-version, in: query, required: true, type: string, default: 2019-05-06-Preview,
            x-ms-visibility: internal}
          - {name: api-key, in: header, required: true, type: string, default: YOURKEYGOESHERE,
            x-ms-visibility: internal}
    ```

1. 在"响应"部分，单击 **"添加默认响应"。** 这一点至关重要，因为它将帮助**Power Apps**了解响应的架构。 粘贴示例响应。

    > [!TIP] 
    > 您可以输入的 JSON 响应存在字符限制，因此您可能需要简化 JSON，以便在粘贴 JSON 之前进行。 响应的重要方面架构/格式。 示例响应中的实际值不太重要，可以简化以减少字符计数。
    

1.    单击屏幕右上角的 **"创建连接器**"按钮，然后才能对其进行测试。

1.  在"测试页"中，单击 **+ 新连接**，然后输入搜索服务查询键作为*api 键*的值。

    此步骤可能会将您带到向导的外，并进入"连接"页。 您可能希望返回自定义连接编辑器以实际测试连接。 转到**自定义连接器**>选择新创建的连接器 *>...* > **查看属性** > **编辑** > **4。测试**以返回测试页。

1.    现在单击 **"测试"操作**，以确保从索引中获得结果。 如果您成功，您应该会看到 200 状态，在响应正文中，您应该会看到 JSON，其中描述了您的搜索结果。




## <a name="visualize-results-from-the-custom-connector"></a>可视化来自自定义连接器的结果
本教程的目标不是向您展示如何使用电源应用创建花哨的用户体验，因此 UI 布局将是极简的。 让我们创建一个带有搜索框的 PowerApp，一个搜索按钮，并在库控件中显示结果。  PowerApp 将连接到我们最近创建的自定义连接器，以便从 Azure 搜索获取数据。

1. 创建新的电源应用。 转到 **"应用"** 部分，单击 **"新建应用**"，然后选择 **"画布**"。

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="创建画布应用" border="true":::

1. 选择您喜欢的应用程序类型。 对于本教程创建一个**空白应用程序**与**电话布局**。 **将显示"电源应用工作室**"。

1. 进入演播室后，选择"**数据源**"选项卡，然后单击刚刚创建的新连接器。 在我们的案例中，它被称为*Azure 搜索查询*。 单击"**添加连接**"。

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="连接连接器" border="true":::

    现在 *，AzureSearchQuery*是一个数据源，可从应用程序使用。
    
1. 导航到 **"插入"选项卡**，以便我们可以向窗体中添加一些控件。

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="插入控件" border="true":::

1.  插入以下元素：
    -   值"查询" 的文本标签：
    -   文本输入元素（称为*txtQuery，* 默认值："*"
    -   带有文本"搜索"的按钮 
    -   称为垂直库（称为*库结果*）
    
    您的表单应如下所示：

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="控件布局" border="true":::

1. 要使 **"搜索"按钮**发出查询，请选择该按钮，并粘贴以下操作以执行**OnSelect**：

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Get({search: txtQuery.Text}).value))
    ```

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="按钮打开选择" border="true":::
 
    此操作将导致按钮使用*txtQuery*文本框中的文本作为查询术语，使用搜索查询的结果更新名为*azResult*的新集合。
    
1.  作为下一步，我们将我们创建的垂直库链接到*azResult*集合。 选择库控件，并在属性窗格中执行以下操作。

    -  将**数据源**设置为*azResult*。
    
    -  根据索引中的数据类型选择适合您的**布局**。 在这种情况下，我们使用*标题、字幕和正文*布局。
    
    -  **编辑字段**，并选择要可视化的字段。

    由于我们在定义连接器时提供了示例结果，因此应用知道索引中可用的字段。
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="库字段" border="true":::   
 
1.  按**F5**预览应用。  

    请记住，字段可以设置为计算值。      
    对于示例，使用 *"图像、标题和字幕"* 布局设置并指定*Image*函数作为数据和文件名（例如`"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`）的根路径的串联将生成以下结果。

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="最终应用" border="true":::        

## <a name="next-steps"></a>后续步骤

有关详细信息和在线培训，请参阅[电源应用学习目录](https://docs.microsoft.com/powerapps/learning-catalog/get-started)。

