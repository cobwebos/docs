---
title: 使用 Azure 数据工厂托管 VNet 映射数据流转换数据
description: 本教程提供有关使用 Azure 数据工厂转换包含映射数据流的数据的分步说明
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/19/2019
ms.openlocfilehash: 5515f6c4dfbc5d3c0e391373e763597d7fdc89ed
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531105"
---
# <a name="transform-data-securely-using-mapping-data-flows"></a>使用映射数据流安全地转换数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果对 Azure 数据工厂不熟悉，请参阅 [Azure 数据工厂简介](https://docs.microsoft.com/azure/data-factory/introduction)。

在本教程中，你将使用 Azure 数据工厂用户界面（UX）创建一个管道，该管道使用[Azure 数据工厂托管的虚拟网络](managed-virtual-network-private-endpoint.md)中的映射数据流**从 Azure Data Lake Storage （ADLS） Gen2 源复制数据并将数据转换为 ADLS Gen2 接收器（这两种方法只允许访问所选网络）** 。 使用映射数据流转换数据时，可以展开本教程中的配置模式。

在本教程中，将执行以下步骤：

> [!div class="checklist"]
>
> * 创建数据工厂。
> * 使用数据流活动创建管道。
> * 使用四个转换构建映射数据流。
> * 测试性运行管道。
> * 监视数据流活动

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 存储帐户**。 使用 ADLS 存储作为*源*和*接收器*数据存储。 如果没有存储帐户，请参阅[创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)以获取创建步骤。 **请确保存储帐户仅允许从 "所选网络" 进行访问。** 

本教程中我们要转换的文件是 MoviesDB.csv 的，可在[此处](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)找到。 若要从 GitHub 检索该文件，请将内容复制到所选的文本编辑器，以将本地保存为 .csv 文件。 若要将文件上传到存储帐户，请参阅[上传 Azure 门户的 blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 这些示例将引用名为 "sample data" 的容器。

## <a name="create-a-data-factory"></a>创建数据工厂

在此步骤中，您将创建一个数据工厂，并打开数据工厂 UX，以在数据工厂中创建管道。

1. 打开 **Microsoft Edge** 或 **Google Chrome**。 目前，只有 Microsoft Edge 和 Google Chrome web 浏览器支持数据工厂 UI。
2. 在左侧菜单中，选择“创建资源” > “Analytics” > “数据工厂”。
3. 在“新建数据工厂”页的“名称”下输入 **ADFTutorialDataFactory** 。

   Azure 数据工厂的名称必须 *全局唯一*。 如果收到有关名称值的错误消息，请为数据工厂输入另一名称。 （例如 yournameADFTutorialDataFactory）。 有关数据工厂项目的命名规则，请参阅[数据工厂命名规则](naming-rules.md)。

4. 选择要在其中创建数据工厂的 Azure **订阅**。
5. 对于“资源组”，请执行以下步骤之一：

    a. 选择“使用现有资源组”，并从下拉列表选择现有的资源组。

    b. 选择“新建”，并输入资源组的名称。 
         
    若要了解资源组，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。 
6. 在“版本”下选择“V2”。 
7. 在“位置”下选择数据工厂所在的位置。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（例如，Azure 存储和 SQL 数据库）和计算（例如，Azure HDInsight）可以位于其他区域。

8. 选择“创建”。

9. 创建完成后，通知中心内会显示通知。 选择“转到资源”导航到“数据工厂”页。
10. 选择“创作和监视”，在单独的选项卡中启动数据工厂 UI。

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>在 ADF 托管的虚拟网络中创建 Azure Integration Runtime
在此步骤中，你将创建一个 Azure Integration Runtime 并启用托管虚拟网络。

1. 在 ADF 门户中，单击 "**管理中心**"，并单击 "**新建**" 创建新的 Azure Integration Runtime。
   ![新建 Azure Integration Runtime](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. 选择创建**Azure** Integration Runtime。
   ![新 Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. 启用**虚拟网络**。
   ![新 Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. 选择“创建”。

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>使用数据流活动创建管道

在此步骤中，你将创建一个包含数据流活动的管道。

1. 在“开始使用”页中，选择“创建管道”。 

   ![创建管道](./media/doc-common-process/get-started-page.png)

1. 在管道的 "**属性**" 窗格中，输入**TransformMovies**作为管道**名称**。
1. 在工厂顶部栏中，将 "数据流**调试**" 滑块滑动到上。 调试模式允许针对实时 Spark 群集对转换逻辑进行交互式测试。 数据流群集需要5-7 分钟才能预热，如果用户计划进行数据流开发，则建议先启用调试。 有关详细信息，请参阅[调试模式](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-debug-mode)。

    ![数据流调试](media/tutorial-data-flow-private/dataflow-debug.png)
1. 在 "**活动**" 窗格中，展开 "**移动和转换**" 可折叠面板。 将 **"数据流" 活动从**"" 窗格拖放到管道画布上。

1. 在 "**添加**数据流" 弹出窗口中，选择 "**创建新**数据流"，然后选择 "**映射数据流**"。 完成后单击“确定”****。

    ![映射数据流](media/tutorial-data-flow-private/mapping-dataflow.png)

1. 在 "属性" 窗格中将数据流命名为**TransformMovies** 。

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>在数据流画布中生成转换逻辑

创建数据流后，会自动将其发送到数据流画布。 在此步骤中，你将生成一个数据流，该数据流在 ADLS 存储中使用 moviesDB.csv，并将 comedies 的平均评级从1910到2000。 然后，将此文件写回到 ADLS 存储。

### <a name="add-the-source-transformation"></a>添加源转换

在此步骤中，将 Azure Data Lake Storage Gen2 设置为源。

1. 在 "数据流" 画布中，通过单击 "**添加源**" 框来添加源。

1. 将源命名为**MoviesDB**。 单击 "**新建**" 以创建新的源数据集。

1. 选择**Azure Data Lake Storage Gen2**。 单击“继续”(Continue)。

1. 选择**DelimitedText**。 单击“继续”(Continue)。

1. 将数据集命名为**MoviesDB**。 在 "链接服务" 下拉列表中，选择 "**新建**"。

1. 在链接的服务创建屏幕中，将 ADLS gen2 链接服务命名为**ADLSGen2** ，并指定身份验证方法。 然后输入连接凭据。 在本教程中，我们将使用帐户密钥连接到存储帐户。 

1. 请确保启用**交互式创作**。 启用可能需要大约1分钟的时间。

    ![交互式创作](./media/tutorial-data-flow-private/interactive-authoring.png)

1. 选择 "**测试连接**"，它应该会失败，因为存储帐户不允许对专用终结点进行创建和批准即可进行访问。 在错误消息中，应会看到一个链接，用于创建可用于创建托管专用终结点的**专用终结点**。 *一种替代方法是直接访问 "管理" 选项卡，然后按照[此部分](#create-a-managed-private-endpoint)中的说明创建托管专用终结点*

1. 让对话框保持打开状态，然后前往上面选定的存储帐户。

1. 按照[此部分](#approval-of-a-private-link-in-storage-account)中的说明批准专用链接。

1. 返回到对话框。 再次**测试连接**，然后选择 "**创建**" 以部署链接服务。

1. 返回数据集创建屏幕后，在 "**文件路径**" 字段下输入文件所在的位置。 在本教程中，moviesDB.csv 的文件位于容器示例-数据中。 如果文件具有标题，请选中**第一行作为标题**。 **从 "连接"/"存储**" 中选择 "直接从存储中的文件导入标头架构"。 完成后单击“确定”。

    ![源路径](media/tutorial-data-flow-private/source-file-path.png)

1. 如果调试群集已启动，请转到源转换的 "**数据预览**" 选项卡，然后单击 "**刷新**" 以获取数据的快照。 您可以使用数据预览来验证是否已正确配置转换。

    ![数据预览](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>创建托管专用终结点

如果在测试上述连接时未单击超链接，请遵循以下路径。 现在，你需要创建一个托管的专用终结点，该终结点将连接到上面创建的链接服务。

1. 中转到 "管理" 选项卡。
> [!NOTE]
> 管理选项卡可能不适用于所有数据工厂实例。 如果看不到它，仍然可以通过 "**创作**" 选项卡访问专用终结点--> "**连接**"--> "**专用终结点**"
1. 中转到 "托管专用终结点" 部分。
1. 选择 "托管专用终结点" 下的 " **+ 新建**"。

    ![新托管专用终结点](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. 从列表中选择 "Azure Data Lake Storage Gen2" 磁贴，然后选择 "**继续**"。
1. 输入前面创建的存储帐户的名称。
1. 选择“创建”。
1. 等待几秒钟后，创建的专用链接需要审批。
1. 选择上面创建的专用终结点。 你可以看到一个超链接，该超链接将引导你在存储帐户级别批准专用终结点。

    ![管理专用终结点](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>在存储帐户中批准专用链接

1. 在存储帐户中，在 "设置" 部分中，中转到**专用终结点连接**。

1. 勾选前面创建的专用终结点，并选择 "**批准**"。

    ![批准专用终结点](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. 添加说明，然后单击 **"是"**。
1. 返回到 Azure 数据工厂中 "**管理**" 选项卡的 "**托管专用终结点**" 部分。
1. 需要大约1分钟的时间才能获得为你的专用终结点反射的批准。

### <a name="add-the-filter-transformation"></a>添加筛选器转换

1. 在 "数据流" 画布上的源节点旁边，单击加号图标以添加一个新转换。 要添加的第一个转换是**筛选器**。

    ![添加筛选器](media/tutorial-data-flow-private/add-filter.png)
1. 将筛选器转换命名为**FilterYears**。 单击 "**筛选"** 旁边的 "表达式" 框以打开 "表达式生成器"。 在此处指定筛选条件。

    ![筛选年份](media/tutorial-data-flow-private/filter-years.png)
1. 数据流表达式生成器允许以交互方式生成要在各种转换中使用的表达式。 表达式可以包含内置函数、输入架构中的列和用户定义的参数。 有关如何生成表达式的详细信息，请参阅[Data Flow expression builder](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-expression-builder)。

    * 在本教程中，你要筛选在1910年到2000年之间出现的流派喜剧的电影。 由于 year 目前是一个字符串，因此需要使用函数将其转换为整数 ```toInteger()``` 。 使用大于或等于（>=）和小于或等于（<=）运算符来与文本年份值1910和200进行比较。 将这些表达式与和（&&）运算符结合在一起。 表达式如下所示：

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * 若要确定哪些电影是 comedies 的，可以使用 ```rlike()``` 函数查找列流派中的 "喜剧" 模式。 将 rlike 表达式与 year 比较以获取：

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * 如果调试群集处于活动状态，则可以通过单击 "**刷新**" 来验证逻辑，以查看表达式输出与使用的输入进行比较。 有多个正确答案可以帮助你如何使用数据流表达式语言来完成此逻辑。

        ![筛选器表达式](media/tutorial-data-flow-private/filter-expression.png)

    * 完成表达式后，单击 "**保存并完成**"。

1. 提取**数据预览**，验证筛选器是否正常工作。

    ![筛选数据预览](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>添加聚合转换

1. 要添加的下一个转换是**架构修饰符**下的**聚合**转换。

    ![添加聚合](media/tutorial-data-flow-private/add-aggregate.png)
1. 将聚合转换命名为**AggregateComedyRatings**。 在 "**分组依据**" 选项卡中，从下拉列表中选择 "**年**"，以便按电影的年份对聚合分组。

    ![聚合组](media/tutorial-data-flow-private/group-by-year.png)
1. 请参阅 "**聚合**" 选项卡。在左侧文本框中，将聚合列命名为**AverageComedyRating**。 单击右侧的 "表达式" 框，通过表达式生成器输入聚合表达式。

    ![聚合列名称](media/tutorial-data-flow-private/name-column.png)
1. 若要获取列**分级**的平均值，请使用 ```avg()``` 聚合函数。 由于 "**分级**" 是一个字符串并 ```avg()``` 采用数字输入，因此必须通过函数将该值转换为数字 ```toInteger()``` 。 表达式如下所示：

    ```avg(toInteger(Rating))```

    完成后，单击 "**保存并完成**"。

    ![保存聚合](media/tutorial-data-flow-private/save-aggregate.png)
1. 转到 "**数据预览**" 选项卡以查看转换输出。 请注意，只有两列是 "**年**" 和 " **AverageComedyRating**"。

### <a name="add-the-sink-transformation"></a>添加接收器转换

1. 接下来，您需要在 "**目标**" 下添加**接收器**转换。

    ![添加接收器](media/tutorial-data-flow-private/add-sink.png)
1. 命名接收器**接收器**。 单击 "**新建**" 以创建接收器数据集。

    ![创建接收器](media/tutorial-data-flow-private/create-sink.png)
1. 在 "新建数据集" 页中，选择**Azure Data Lake Storage Gen2**。 单击“继续”(Continue)。

1. 在 "选择格式" 页上，选择 " **DelimitedText**"。 单击“继续”(Continue)。

1. 将接收器数据集命名为**MoviesSink**。 对于 "链接服务"，请选择为源转换创建的相同 ADLSGen2 链接服务。 输入要向其写入数据的输出文件夹。 在本教程中，我们将写入 "示例-数据" 容器中的文件夹 "输出"。 文件夹不需要事先存在，可以动态创建。 将**第一行设置为标头**为 true，并为**导入架构**选择 "**无**"。 单击“确定”。

    ![接收器路径](media/tutorial-data-flow-private/sink-file-path.png)

现在，您已经完成了数据流的构建。 你已准备好在管道中运行它。

## <a name="running-and-monitoring-the-data-flow"></a>运行和监视数据流

可以在发布管道之前对其进行调试。 在此步骤中，将触发数据流管道的 "调试" 运行。 当数据预览不写入数据时，调试运行会将数据写入接收器目标。

1. 中转到管道画布。 单击 "**调试**" 以触发调试运行。

1. 数据流活动的管道调试使用活动调试群集，但至少需要一分钟的时间来初始化。 可以通过 "**输出**" 选项卡跟踪进度。运行成功后，单击 "运行详细信息" 的眼镜图标。

1. 在详细信息页中，可以看到每个转换步骤中的行数和所用的时间。

    ![监视运行](media/tutorial-data-flow-private/run-details.png)
1. 单击转换可获取有关数据的列和分区的详细信息。

如果已正确遵循本教程，则应将83行和2列写入接收器文件夹中。 可以通过检查 blob 存储来验证数据是否正确。

## <a name="summary"></a>总结

在本教程中，你将使用 Azure 数据工厂用户界面（UX）创建一个管道，该管道使用[Azure 数据工厂托管的虚拟网络](managed-virtual-network-private-endpoint.md)中的映射数据流**从 Azure Data Lake Storage （ADLS） Gen2 源复制数据并将数据转换为 ADLS Gen2 接收器（这两种方法只允许访问所选网络）** 。
