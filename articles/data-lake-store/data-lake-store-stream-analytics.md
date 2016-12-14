---
title: "将数据从流分析流式传输到 Data Lake Store | Microsoft Docs"
description: "使用 Azure 流分析，将数据流入 Azure Data Lake Store"
services: data-lake-store,stream-analytics
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/07/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73d3e5577d0702a93b7f4edf3bf4e29f55a053ed
ms.openlocfilehash: 2864fbf1fc4f070cb4d88d1bb3efbaaf408c68ce


---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>使用 Azure Stream Analytics 从 Azure 存储 Blob 流处理数据到 Data Lake Store
在本文中，将学习如何使用 Azure Data Lake Store 作为 Azure 流分析作业的输出。 本文演示了一个简单的方案：从 Azure 存储 blob（输入）读取数据并将数据写入 Data Lake Store（输出）。

> [!NOTE]
> 目前只有 [Azure 经典门户](https://manage.windowsazure.com)支持为流分析创建和配置 Data Lake Store 输出。 因此，本教程中的某些部分将使用 Azure 经典门户。
>
>

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 为 Data Lake Store 公共预览**启用 Azure 订阅**。 请参阅[说明](data-lake-store-get-started-portal.md)。
* **Azure 存储帐户**。 将用此帐户的 blob 容器来输入流分析作业的数据。 本教程中，假设创建名为 **datalakestoreasa** 的存储帐户，并在帐户中创建名为 **datalakestoreasacontainer** 的容器。 创建容器后，将示例数据文件上传到其中。 可以从 [存储库](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)获取示例数据文件。 可以使用各种客户端（[Azure 存储资源管理器](http://storageexplorer.com/)将数据上载到 blob 容器。

  > [!NOTE]
  > 如果从 Azure 门户创建帐户，请确保使用“经典”部署模型来创建账户。 该步骤可确保通过 Azure 经典门户访问存储帐户，因为这是用来创建流分析作业的门户。 有关如何使用经典部署，在 Azure 门户创建存储帐户的说明，请参阅[创建 Azure 存储帐户](../storage/storage-create-storage-account.md#create-a-storage-account)。
  >
  > 或者，可从 Azure 经典门户创建存储帐户。
  >
  >
* **Azure Data Lake Store 帐户**。 遵循[使用 Azure 门户，实现 Azure Data Lake Store 入门](data-lake-store-get-started-portal.md) 中的说明。  

## <a name="create-a-stream-analytics-job"></a>创建流分析作业
首先创建流分析作业，其中包括输入源和输出目标。 对于本教程中，源是一个 Azure blob 容器，目标位于 Data Lake Store。

1. 登录到 [Azure 经典门户](https://manage.windowsazure.com)。
2. 在屏幕左上角，单击“新建” > “数据服务” > “流分析” > “快速创建”。 提供以下值，然后单击“创建流分析作业”

    ![创建流分析作业](./media/data-lake-store-stream-analytics/create.job.png "Create a Stream Analytics job")

## <a name="create-a-blob-input-for-the-job"></a>创建作业的 Blob 输入
1. 打开流分析作业页面，单击“输入”选项卡，然后单击“添加输入”以启动向导。
2. 在“添加输入到作业”页面上，选择“数据流”，然后单击转发箭头。

    ![添加输入到作业](./media/data-lake-store-stream-analytics/create.input.1.png "Add an input to your job")
3. 在“添加数据流到作业”页面上，选择“Blob 存储”，然后单击转发箭头。

    ![添加数据流到作业](./media/data-lake-store-stream-analytics/create.input.2.png "Add a data stream to the job")
4. 在“Blob 存储设置”上，提供用作输入数据源的 blob 存储的详细信息。

    ![提供 blob 存储设置](./media/data-lake-store-stream-analytics/create.input.3.png "Provide the blob storage settings")

   * **输入的别名**。 这是作业输入的唯一名称。
   * **选择存储帐户**。 请确保存储帐户与流分析作业所在的区域相同，否则将产生区域之间移动数据的额外成本。
   * **提供存储容器**。 可以选择创建新的容器，或选择现有容器。

     单击向前箭头。
5. 在“序列化设置” 页上，将序列化格式设置为 **CSV**，分隔符为**tab**，编码为 **UTF8**，然后单击“复选标记”。

    ![提供序列化设置](./media/data-lake-store-stream-analytics/create.input.4.png "Provide the serialization settings")
6. 完成该向导后，blob 输入会添加到“输入”选项卡，“诊断”列应该显示“确定”。 通过使用底部的“测试连接”按钮，还可以显式地测试输入的连接。

## <a name="create-a-data-lake-store-output-for-the-job"></a>创建作业的 Data Lake Store 输出
1. 打开流分析作业页面，单击“输出”选项卡，然后单击“添加输出”以启动向导。
2. 在“添加输出到作业”页面上，选择“Data Lake Store”，然后单击转发箭头。

    ![向作业添加输出](./media/data-lake-store-stream-analytics/create.output.1.png "Add an output to your job")
3. 在“授权连接”页上，如果已创建 Data Lake Store 帐户，请单击“立即授权”。 否则，请单击“立即注册”以创建新帐户。 对于本教程，我们假定已经有一个 Data Lake Store 帐户（如必备组件中所述）。 使用登录到 Azure 经典门户的凭据，自动获取授权。

    ![Azure Data Lake Store](./media/data-lake-store-stream-analytics/create.output.2.png "Authorize Data Lake Store")
4. 在“Data Lake Store 设置”页上，输入信息，如下面的屏幕截图所示。

    ![指定 Data Lake Store 设置](./media/data-lake-store-stream-analytics/create.output.3.png "Specify Data Lake Store settings")

   * **键入输出别名**。 这是作业输出的唯一名称。
   * **指定 Data Lake Store 帐户**。 如必备组件中所述，已创建此账户。
   * **指定路径前缀模式**。 需识别由流分析作业写入 Data Lake Store 的输出文件。 由于作业写入的输出标题为 GUID 格式，包括前缀将帮助识别写入的输出。 如果想要将日期和时间戳作为前缀的一部分包含在内，请确保前缀模式中包含 `{date}/{time}`。 如果已包含此前缀模式，则会启用**日期格式**和**时间格式**字段，可以选择所需的格式。

     单击向前箭头。
5. 在“序列化设置”页上，将序列化格式设置为 **CSV**，分隔符为 **tab**，编码为 **UTF8**，然后单击“复选标记”。

    ![指定输出格式](./media/data-lake-store-stream-analytics/create.output.4.png "Specify the output format")
6. 完成该向导后，在“输出”选项卡下添加 Data Lake Store，在“诊断”列会显示“确定”。 通过使用底部的“测试连接” 按钮，也可显式地测试输出连接。

## <a name="run-the-stream-analytics-job"></a>运行流分析作业
若要运行流分析作业，必须从查询选项卡运行查询。 对于本教程，通过将占位符替换为作业输入和输出别名，可运行示例查询，如下面的屏幕截图所示。

![运行查询](./media/data-lake-store-stream-analytics/run.query.png "Run query")

单击屏幕底部的“保存”，然后再单击“启动”。 从对话框中，选择“自定义时间”，然后选择一个过去的日期，如**2016 年 1 月 1 日**。 请单击复选标记来启动作业。 启动作业可能会占用几分钟。

![设置作业时间](./media/data-lake-store-stream-analytics/run.query.2.png "Set job time")

启动作业后，单击“监视器”选项卡以查看数据处理方式。

![监视作业](./media/data-lake-store-stream-analytics/run.query.3.png "Monitor job")

最后，可使用 [Azure 门户](https://portal.azure.com)打开 Data Lake Store 帐户，并验证是否已成功将数据写入到其中。

![验证输出](./media/data-lake-store-stream-analytics/run.query.4.png "Verify output")

在“数据资源管理器”窗格中，请注意：根据 Data Lake Store 中的指定，输出已写入文件夹(`streamanalytics/job/output/{date}/{time}`)。  

## <a name="see-also"></a>另请参阅
* [创建 HDInsight 群集以使用 Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Nov16_HO3-->


