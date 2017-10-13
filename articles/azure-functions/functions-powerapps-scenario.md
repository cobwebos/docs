---
title: "从 PowerApps 调用函数 | Microsoft Docs"
description: "创建一个自定义连接器，然后使用此连接器调用函数。"
services: functions
keywords: "云应用, 云服务, PowerApps, 商业流程, 商业应用程序"
documentationcenter: 
author: mgblythe
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: mblythe
ms.custom: 
ms.openlocfilehash: 1e262fde37b68bcfcee3c974deb91bd07965de19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="call-a-function-from-powerapps"></a>从 PowerApps 调用函数
[PowerApps](https://powerapps.microsoft.com) 平台专为商业专家设计，无需写入传统的应用程序代码即可生成应用。 专业开发人员可以使用 Azure Functions 扩展 PowerApps 的功能，同时使 PowerApps 应用构建者摆脱技术细节。

本主题基于风力涡轮机的维护方案生成应用。 介绍如何调用在[为函数创建 OpenAPI 定义](functions-openapi-definition.md)中定义的函数。 此函数确定风力涡轮机上的紧急修复是否经济高效。

![PowerApps 中已完成的应用](media/functions-powerapps-scenario/finished-app.png)

有关从 Microsoft Flow 调用同一函数的信息，请参阅[从 Microsoft Flow 调用函数](functions-flow-scenario.md)。

本主题介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Excel 中准备示例数据。
> * 导出 API 定义。
> * 添加到 API 的连接。
> * 创建应用并添加数据源。
> * 添加控件以查看应用中的数据。
> * 添加控件以调用函数并显示数据。
> * 运行应用以确定修复是否经济高效。

## <a name="prerequisites"></a>先决条件

+ 一个有效的 [PowerApps 帐户](https://powerapps.microsoft.com/tutorials/signup-for-powerapps.md)，其中登录凭据与 Azure 帐户相同。 
+ Excel，因为要使用 Excel 作为应用的数据源。
+ 完成[为函数创建 OpenAPI 定义](functions-openapi-definition.md)教程。


## <a name="prepare-sample-data-in-excel"></a>在 Excel 中准备示例数据
首先准备应用中使用的示例数据。 将下表复制到 Excel。 

| 标题      | 纬度  | 经度  | 上次维修日期 | 最大输出 | 需要维修 | 预计工作量 | 检查备注                            |
|------------|-----------|-------------|-----------------|-----------|-----------------|-----------------|--------------------------------------------|
| 涡轮机 1  | 47.438401 | -121.383767 | 2/23/2017       | 2850      | 是             | 6               | 这是本月的第二个问题。       |
| 涡轮机 4  | 47.433385 | -121.383767 | 5/8/2017        | 5400      | 是             | 6               |                                            |
| 涡轮机 33 | 47.428229 | -121.404641 | 6/20/2017       | 2800      |                 |                 |                                            |
| 涡轮机 34 | 47.463637 | -121.358824 | 2/19/2017       | 2800      | 是             | 7               |                                            |
| 涡轮机 46 | 47.471993 | -121.298949 | 3/2/2017        | 1200      |                 |                 |                                            |
| 涡轮机 47 | 47.484059 | -121.311171 | 8/2/2016        | 3350      |                 |                 |                                            |
| 涡轮机 55 | 47.438403 | -121.383767 | 10/2/2016       | 2400      | 是             | 40               | 我们为这台涡轮机采购了一些即将到货的部件。 |

1. 在 Excel 中，选择数据，在“开始”选项卡上，单击“套用表格格式”。

    ![套用表格格式](media/functions-powerapps-scenario/format-table.png)

1. 选择任意样式，单击“确定”。

1. 选择表后，在“设计”选项卡上，输入 `Turbines` 作为“表名称”。

    ![表名称](media/functions-powerapps-scenario/table-name.png)

1. 保存 Excel 工作簿。

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>添加到 API 的连接
自定义 API（也称为自定义连接器）可用于 PowerApps，但必须先创建到 API 的连接，然后才能用于应用。

1. 在 [web.powerapps.com](https://web.powerapps.com) 中，单击“连接”。

    ![PowerApps 连接](media/functions-powerapps-scenario/powerapps-connections.png)

1. 单击“新建连接”，向下滚动到“涡轮机修复”连接器并在其上单击。

    ![新建连接](media/functions-powerapps-scenario/new-connection.png)

1. 输入 API 密钥，然后单击“创建”。

    ![创建连接](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> 如果与他人共享应用，在应用上操作或使用应用的所有用户也必须输入 API 密钥才能连接到 API。 此行为将来可能会更改，到时我们将更新此主题以反映更改。

## <a name="create-an-app-and-add-data-sources"></a>创建应用并添加数据源
现在可以在 PowerApps 中创建应用，并添加 Excel 数据和自定义 API 作为应用数据源。

1. 在 [web.powerapps.com](https://web.powerapps.com) 中，在左侧窗格中，单击“新建应用”。

1. 在“空白应用”下，单击“手机布局”。

    ![创建平板电脑应用](media/functions-powerapps-scenario/create-phone-app.png)

    应用在 Web PowerApps Studio 中打开。 下图显示了 PowerApps Studio 的各部分。 此图显示的是已完成的应用；中间窗格最初是空白屏幕。

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(1) 左导航栏**，其中可以看见每个屏幕上所有控件的分层视图

    **(2) 中间窗格**，其中显示当前操作的屏幕

    **(3) 右窗格**，设置布局和数据源等选项的位置

    **(4) 属性**下拉列表，选择公式应用到的属性的位置

    **(5) 公式栏**，添加定义应用行为的公式（如在 Excel 中）的位置
    
    **(6) 功能区**，添加控件并自定义设计元素的位置

1. 将 Excel 文件添加为数据源。

    1. 在右窗格中，在“数据”选项卡上，单击“添加数据源”。

        ![添加数据源](media/functions-powerapps-scenario/add-data-source.png)

    1. 单击“将静态数据添加到应用”。

        ![添加数据源](media/functions-powerapps-scenario/add-static-data.png)

        通常情况下是从外部数据源中读取和写入数据，但这里是将 Excel 数据添加为静态数据，因为这是一个示例。

    1. 导航到保存的 Excel 文件，选择“涡轮机”表，再单击“连接”。

        ![添加数据源](media/functions-powerapps-scenario/choose-table.png)

1. 将自定义 API 添加为数据源。

    1. 在“数据”选项卡上，单击“添加数据源”。

    1. 单击“涡轮机修复”。

        ![涡轮机修复连接器](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>添加控件以在应用中查看数据
现在应用中已具有数据源，接下来可以将屏幕添加到应用，以查看涡轮机数据。

1. 在“主页”选项卡上，单击“新建屏幕” > “列表屏幕”。

    ![列表屏幕](media/functions-powerapps-scenario/list-screen.png)

    PowerApps 将添加一个屏幕，该屏幕包含的“库”可显示项和其他可进行搜索、排序和筛选的控件。

1. 将标题栏更改为 `Turbine Repair` 并重设库的大小，在其下方留出添加更多控件所需的空间。

    ![更改标题和重设库的大小](media/functions-powerapps-scenario/gallery-title.png)

1. 选择库后，在右窗格的“数据”选项卡上，将数据源从“CustomGallerySample”更改为“涡轮机”。

    ![更改数据源](media/functions-powerapps-scenario/change-data-source.png)

    数据集不包含图像，所以下一步更改布局以更好地适应数据。 

1. 仍在右窗格中，将“布局”更改为“标题、副标题和正文”。

    ![更改库布局](media/functions-powerapps-scenario/change-layout.png)

1. 在右窗格中执行的最后一步是更改库中显示的字段。

    ![更改库字段](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = 上次维修时间
    + **Subtitle2** = 需要维修
    + **Title2** = 标题 

1. 选择库后，将“TemplateFill”属性设置为以下公式：`If(ThisItem.IsSelected, Orange, White)`。

    ![模板填充公式](media/functions-powerapps-scenario/formula-fill.png)

    现在更容易看出选择了哪个库项。

    ![选择的项](media/functions-powerapps-scenario/selected-item.png)

1. 不需要应用中的原始屏幕。 在左窗格中，将鼠标悬停在“Screen1”，依次单击“...”和“删除”。

    ![删除屏幕](media/functions-powerapps-scenario/delete-screen.png)

在生产应用中通常还会涉及到许多其他格式设置，但我们将转移到此方案的重要部分，即调用函数。

## <a name="add-controls-to-call-the-function-and-display-data"></a>添加控件以调用函数并显示数据
拥有一个显示所有涡轮机的摘要数据之后，现在可以添加控件来调用创建的函数并显示返回的数据。 基于函数在 OpenAPI 定义中的命名方式（此示例中为 `TurbineRepair.CalculateCosts()`）访问函数。

1. 在功能区的“插入”选项卡上，单击“按钮”。 然后在同一选项卡上，单击“标签”

    ![插入按钮和标签](media/functions-powerapps-scenario/insert-controls.png)

1. 拖动库下面的按钮和标签，并重设标签的大小。 

1. 选择按钮文本，并将其更改为 `Calculate costs`。 应用应如下图所示。

    ![带按钮的应用](media/functions-powerapps-scenario/move-button-label.png)

1. 选择按钮，并为按钮的“OnSelect”属性输入以下公式。

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    此公式在单击按钮时执行，如果所选库项的“ServiceRequired”值为 `Yes`，则会执行以下操作：

    + 清除集合 `DetermineRepair` 以从前面的调用中删除数据。 集合是一个表格变量。

    + 为集合分配通过调用函数 `TurbineRepair.CalculateCosts()` 返回的数据。 
    
        传递到函数的值来自库中所选项的“EstimatedEffort”和“MaxOutput”字段。 这些字段未显示在库中，但仍可用于公式。

1. 选择标签，为标签的“文本”属性输入以下公式。

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    此公式使用 `First()` 函数访问 `DetermineRepair` 集合的第一（唯一）行。 然后它显示该函数返回的三个值：`message`、`costToFix` 和 `revenueOpportunity`。 应用首次运行前，这些值都为空。

    完成的应用应如下图所示。

    ![运行前完成的应用](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>运行应用程序
已有一个完整的应用了！ 现在可以运行应用并观察操作中的函数调用。

1. 在 PowerApps Studio 的右上角，单击“运行”按钮： ![运行应用按钮](media/functions-powerapps-scenario/f5-arrow-sm.png)。

1. 选择“ServiceRequired”为 `Yes` 值的涡轮机，然后单击“计算成本”按钮。 应看到类似于下图的结果。

    ![PowerApps 中已完成的应用](media/functions-powerapps-scenario/finished-app.png)

1. 请尝试其他涡轮机，了解函数每次返回的内容。

## <a name="next-steps"></a>后续步骤
本主题介绍了如何执行以下操作：

> [!div class="checklist"]
> * 在 Excel 中准备示例数据。
> * 导出 API 定义。
> * 添加到 API 的连接。
> * 创建应用并添加数据源。
> * 添加控件以查看应用中的数据。
> * 添加控件以调用函数并显示数据
> * 运行应用以确定修复是否经济高效。

若要了解有关 PowerApps 的更多详细信息，请参阅 [PowerApps 简介](https://powerapps.microsoft.com/tutorials/getting-started/)。

若要了解其他有关使用 Azure Functions 的有趣方案，请参阅[从 Microsoft Flow 调用函数](functions-flow-scenario.md)和[创建与 Azure 逻辑应用集成的函数](functions-twitter-email.md)。