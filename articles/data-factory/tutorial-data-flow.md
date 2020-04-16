---
title: 使用映射数据流转换数据
description: 本教程提供了使用 Azure 数据工厂使用映射数据流转换数据的分步说明
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: 917a8d6edf04d8a160c3a6a5ac59949623dfee5c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418671"
---
# <a name="transform-data-using-mapping-data-flows"></a>使用映射数据流转换数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)。

在本教程中，您将使用 Azure 数据工厂用户界面 （UX） 创建管道，使用映射数据流将数据从 Azure 数据存储 （ADLS） Gen2 源复制并转换为 ADLS Gen2 接收器。 使用映射数据流转换数据时，可以扩展本教程中的配置模式

在本教程中，将执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建具有数据流活动的管道。
> * 使用四个转换构建映射数据流。
> * 测试性运行管道。
> * 监视数据流活动

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 使用 ADLS 存储作为*源*和*接收器*数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](../storage/common/storage-account-create.md)以获取创建步骤。

我们在本教程中转换的文件是 MoviesDB.csv，可以[在这里](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)找到。 若要从 GitHub 检索文件，请将内容复制到您选择的文本编辑器中，以本地保存为 .csv 文件。 要将文件上载到存储帐户，请参阅[使用 Azure 门户上载 Blob。](../storage/blobs/storage-quickstart-blobs-portal.md) 这些示例将引用名为"示例数据"的容器。

## <a name="create-a-data-factory"></a>创建数据工厂

在此步骤中，您将创建一个数据工厂并打开数据工厂 UX 以在数据工厂中创建管道。

1. 打开 **Microsoft Edge** 或 **Google Chrome**。 目前，数据工厂 UI 仅在 Microsoft 边缘浏览器和 Google Chrome 网络浏览器中支持。
2. 在左侧菜单上，选择 **"创建资源** > **分析** > **数据工厂**"：

   ![在“新建”窗格中选择“数据工厂”](./media/doc-common-process/new-azure-data-factory-menu.png)

3. 在“新建数据工厂”**** 页的“名称”下输入 **ADFTutorialDataFactory******。

   Azure 数据工厂的名称必须 *全局唯一*。 如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 （例如 yournameADFTutorialDataFactory）。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。

     ![新建数据工厂](./media/doc-common-process/name-not-available-error.png)
4. 选择要在其中创建数据工厂的 Azure **订阅**。
5. 对于“资源组”，请执行以下步骤之一：****

    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。****

    b. 选择“新建”，并输入资源组的名称。**** 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。 
6. 在“版本”下选择“V2”。********
7. 在“位置”下选择数据工厂所在的位置。**** 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如 Azure 存储和 SQL 数据库）和计算（例如 Azure HDInsight）可以位于其他区域。
8. 选择“创建”  。
9. 创建完成后，通知中心内会显示通知。 选择“转到资源”导航到“数据工厂”页。****
10. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。****

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>使用数据流活动创建管道

在此步骤中，您将创建包含数据流活动的管道。

1. 在“开始使用”页中，选择“创建管道”。********

   ![创建管道](./media/doc-common-process/get-started-page.png)

1. 在管道的 **"常规**"选项卡中，输入管道**名称**的 **"转换电影**"。
1. 在出厂顶部栏中，打开 **"数据流"调试**滑块。 调试模式允许针对实时 Spark 群集对转换逻辑进行交互式测试。 数据流群集需要 5-7 分钟才能预热，如果用户计划执行数据流开发，建议他们首先打开调试。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

    ![数据流活动](media/tutorial-data-flow/dataflow1.png)
1. 在 **"活动"** 窗格中，展开 **"移动和转换**手风琴"。 将 **"数据流"** 活动从窗格拖放到管道画布上。

    ![数据流活动](media/tutorial-data-flow/activity1.png)
1. 在 **"添加数据流**"弹出窗口中，选择 **"创建新的数据流"，** 然后命名数据流**转换影片**。 完成操作后，请单击“完成”。

    ![数据流活动](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在数据流画布中构建转换逻辑

创建数据流后，将自动发送到数据流画布。 在此步骤中，您将构建一个数据流，该流将 moviesDB.csv 用于 ADLS 存储，并聚合 1910 年至 2000 年喜剧的平均评级。 然后，您将此文件写回 ADLS 存储。

1. 在数据流画布中，通过单击"**添加源**"框添加源。

    ![数据流画布](media/tutorial-data-flow/dataflow2.png)
1. 命名源**MoviesDB**。 单击 **"新建"** 以创建新的源数据集。

    ![数据流画布](media/tutorial-data-flow/dataflow3.png)
1. 选择**Azure 数据存储第 2 代**。 单击“继续”(Continue)。

    ![数据集](media/tutorial-data-flow/dataset1.png)
1. 选择**分隔文本**。 单击“继续”(Continue)。

    ![数据集](media/tutorial-data-flow/dataset2.png)
1. 命名数据集 **"电影数据库**"。 在链接服务下拉列表中，选择 **"新建**"。

    ![数据集](media/tutorial-data-flow/dataset3.png)
1. 在链接的服务创建屏幕中，命名您的 ADLS gen2 链接服务**ADLSGen2**并指定身份验证方法。 然后输入连接凭据。 在本教程中，我们使用帐户密钥连接到我们的存储帐户。 您可以单击 **"测试连接**"以验证您的凭据输入正确。 完成后，单击“创建”。

    ![链接服务](media/tutorial-data-flow/ls1.png)
1. 返回数据集创建屏幕后，输入文件位于 **"文件路径**"字段下的位置。 在本教程中，文件电影DB.csv位于容器示例数据中。 由于文件具有标头，因此选中**第一行作为标头**。 选择 **"从连接/存储**"直接从存储中的文件导入标头架构。 完成后单击“确定”。

    ![数据集](media/tutorial-data-flow/dataset4.png)
1. 如果调试群集已启动，请转到源转换的 **"数据预览"** 选项卡，然后单击 **"刷新"** 以获取数据的快照。 您可以使用数据预览来验证转换配置正确。

    ![数据流画布](media/tutorial-data-flow/dataflow4.png)
1. 在数据流画布上的源节点旁边，单击加号图标以添加新转换。 要添加的第一个转换是**筛选器**。

    ![数据流画布](media/tutorial-data-flow/dataflow5.png)
1. 命名筛选器转换**筛选器年份**。 单击 **"筛选"** 旁边的表达式框以打开表达式生成器。 在这里，您将指定筛选条件。

    ![筛选器](media/tutorial-data-flow/filter1.png)
1. 数据流表达式生成器允许您以交互方式生成用于各种转换的表达式。 表达式可以包括内置函数、输入架构中的列和用户定义的参数。 有关如何生成表达式的详细信息，请参阅[数据流表达式生成器](concepts-data-flow-expression-builder.md)。

    在本教程中，您希望过滤 1910 年至 2000 年间的流派喜剧电影。 由于年份当前是字符串，因此您需要使用 函数```toInteger()```将其转换为整数。 使用大于或等于 （>+） 和小于或等于 （<=） 运算符来比较与字面年份值 1910 和 200-进行比较。 将这些表达式与 和 （&&） 运算符结合在一起。 表达式以：

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    要查找哪些电影是喜剧，可以使用 该```rlike()```函数在列流派中查找图案"喜剧"。 将 rlike 表达式与年份比较结合，以获得：

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    如果调试群集处于活动状态，则可以通过单击 **"刷新"** 来验证逻辑，以查看与使用的输入相比的表达式输出。 关于如何使用数据流表达式语言完成此逻辑，有一个以上正确的答案。

    ![筛选器](media/tutorial-data-flow/filter2.png)

    完成表达式后，单击"**保存"和"完成**"。

1. 获取**数据预览**以验证筛选器是否正常工作。

    ![筛选器](media/tutorial-data-flow/filter3.png)
1. 要添加的下一个转换是在**架构修改器**下的**聚合**变换。

    ![聚合](media/tutorial-data-flow/agg1.png)
1. 命名聚合转换**聚合喜剧评级**。 在 **"按组"** 选项卡中，从下拉列表中选择**年份**，按影片出名的年份对聚合进行分组。

    ![聚合](media/tutorial-data-flow/agg2.png)
1. 转到 **"聚合"** 选项卡。在左侧文本框中，命名聚合列 **"平均喜剧"。** 单击右表达式框通过表达式生成器输入聚合表达式。

    ![聚合](media/tutorial-data-flow/agg3.png)
1. 要获取列**评级**的平均值，请使用```avg()```聚合函数。 由于**评级**是一个字符串```avg()```，并且采用数字输入，因此必须通过```toInteger()```函数将值转换为数字。 这是表达式看起来像：

    ```avg(toInteger(Rating))```

    完成后单击 **"保存并完成**"。

    ![聚合](media/tutorial-data-flow/agg4.png)
1. 转到"**数据预览"** 选项卡以查看转换输出。 注意只有两列存在，**年份**和**平均喜剧。**

    ![聚合](media/tutorial-data-flow/agg3.png)
1. 接下来，您希望在 **"目标**"下添加**Sink**转换。

    ![接收器](media/tutorial-data-flow/sink1.png)
1. 命名你的**水槽**。 单击 **"新建**"以创建接收器数据集。

    ![接收器](media/tutorial-data-flow/sink2.png)
1. 选择**Azure 数据存储第 2 代**。 单击“继续”(Continue)。

    ![数据集](media/tutorial-data-flow/dataset1.png)
1. 选择**分隔文本**。 单击“继续”(Continue)。

    ![数据集](media/tutorial-data-flow/dataset2.png)
1. 命名您的接收器数据集**电影沉沦**。 对于链接服务，请选择您在步骤 6 中创建的 ADLS gen2 链接服务。 输入要将数据写入的输出文件夹。 在本教程中，我们将编写到容器"示例数据"中的文件夹"输出"。 该文件夹不需要事先存在，可以动态创建。 将**第一行设置为标题**为 true，并为**导入架构**选择 **"无**"。 单击“完成”。

    ![接收器](media/tutorial-data-flow/sink3.png)

现在，您已完成构建数据流。 您已准备好在管道中运行它。

## <a name="running-and-monitoring-the-data-flow"></a>运行和监视数据流

您可以在发布管道之前调试管道。 在此步骤中，您将触发数据流管道的调试运行。 虽然数据预览不会写入数据，但调试运行会将数据写入接收器目标。

1. 转到管道画布。 单击 **"调试"** 以触发调试运行。

    ![管道](media/tutorial-data-flow/pipeline1.png)
1. 数据流活动的管道调试使用活动调试群集，但至少需要一分钟才能初始化。 您可以通过 **"输出"** 选项卡跟踪进度。运行成功后，单击眼镜图标以打开监视窗格。

    ![管道](media/tutorial-data-flow/pipeline2.png)
1. 在监视窗格中，您可以看到每个转换步骤中花费的行数和时间。

    ![监视](media/tutorial-data-flow/pipeline3.png)
1. 单击转换以获取有关数据的列和分区的详细信息。

    ![监视](media/tutorial-data-flow/pipeline4.png)

如果正确遵循本教程，则应将 83 行和 2 列写入接收器文件夹。 您可以通过检查 Blob 存储来验证数据是否正确。

## <a name="next-steps"></a>后续步骤

本教程中的管道运行一个数据流，该数据流汇总了 1910 到 2000 年喜剧的平均评级，并将数据写入 ADLS。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建具有数据流活动的管道。
> * 使用四个转换构建映射数据流。
> * 测试性运行管道。
> * 监视数据流活动

了解有关[数据流表达式语言](data-flow-expression-functions.md)的更多详细信息。
