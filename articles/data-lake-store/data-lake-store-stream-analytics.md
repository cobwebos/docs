---
title: 将数据从流分析流式传输到 Data Lake Storage Gen1-Azure
description: 使用 Azure 流分析将数据流式传输到 Azure Data Lake Storage Gen1。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: f1740d167bedd20f51ad5bf24a56b7e7e787f754
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690980"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>使用 Azure 流分析将 Azure 存储 Blob 中的数据流式传输至 Azure Data Lake Storage Gen1 中
本文介绍如何使用 Azure Data Lake Storage Gen1 作为 Azure 流分析作业的输出。 本文演示了一个简单的方案：从 Azure 存储 blob（输入）读取数据并将数据写入 Data Lake Storage Gen1（输出）。

## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，必须具有：

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure 存储帐户**。 将用此帐户的 blob 容器来输入流分析作业的数据。 在本教程中，假设有一个名为 **storageforasa** 的存储帐户，并且在该帐户中有一个名为 **storageforasacontainer** 的容器。 创建容器后，将示例数据文件上传到其中。 
  
* **Data Lake Storage Gen1 帐户**。 请遵循[通过 Azure 门户开始使用 Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) 中的说明进行操作。 假设有名为“myadlsg1”的 Data Lake Storage Gen1 帐户****。 

## <a name="create-a-stream-analytics-job"></a>创建流分析作业
首先创建流分析作业，其中包括输入源和输出目标。 在本教程中，源是 Azure blob 容器，目标是 Data Lake Storage Gen1。

1. 登录到[Azure 门户](https://portal.azure.com)。

2. 在左窗格中，单击“流分析作业”****，并单击“添加”****。

    ![创建流分析作业](./media/data-lake-store-stream-analytics/create.job.png "创建流分析作业")

    > [!NOTE]
    > 请确保在存储帐户所在的同一区域中创建作业，否则将产生在区域之间移动数据的额外成本。
    >

## <a name="create-a-blob-input-for-the-job"></a>创建作业的 Blob 输入

1. 打开流分析作业页，在左窗格中单击“输入”**** 选项卡，并单击“添加”****。

    ![添加输入到作业](./media/data-lake-store-stream-analytics/create.input.1.png "添加输入到作业")

2. 在“新建输入”**** 边栏选项卡中，提供以下值。

    ![添加输入到作业](./media/data-lake-store-stream-analytics/create.input.2.png "添加输入到作业")

   * 对于“输入别名”****，输入此作业输入的唯一名称。
   * 对于“源类型”****，选择“数据流”****。
   * 对于“源”****，选择“Blob 存储”****。
   * 对于“订阅”****，选择“使用当前订阅中的 blob 存储”****。
   * 对于“存储帐户”****，选择在“先决条件”部分中创建的存储帐户。 
   * 对于“容器”****，选择在选定的存储帐户中创建的容器。
   * 对于“事件序列化格式”****，选择“CSV”****。
   * 对于“分隔符”****，选择“制表符”****。
   * 对于“编码”****，选择“UTF-8”****。

     单击 **“创建”** 。 现在门户将添加该输入并测试与其的连接。


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>为作业创建 Data Lake Storage Gen1 输出

1. 打开流分析作业页面，单击“输出”选项卡，单击“添加”，然后选择“Data Lake Storage Gen1”************。

    ![向作业添加输出](./media/data-lake-store-stream-analytics/create.output.1.png "向作业添加输出")

2. 在“新建输出”**** 边栏选项卡中，提供以下值。

    ![向作业添加输出](./media/data-lake-store-stream-analytics/create.output.2.png "向作业添加输出")

    * 对于“输出别名”****，输入此作业输出的唯一名称。 该名称是在查询中使用的友好名称，用于将查询输出定向到此 Data Lake Storage Gen1 帐户。
    * 系统会提示授权访问 Data Lake Storage Gen1 帐户。 单击“授权”****。

3. 在“新建输出”**** 边栏选项卡中，继续提供以下值。

    ![向作业添加输出](./media/data-lake-store-stream-analytics/create.output.3.png "向作业添加输出")

   * 在“帐户名称”处，选择已创建的要将作业输出发送到的 Data Lake Storage Gen1 帐户****。
   * 对于“路径前缀模式”，输入用于在指定的 Data Lake Storage Gen1 帐户中写入文件的文件路径****。
   * 对于“日期格式”****，如果在前缀路径中使用日期标记，可以选择组织文件所采用的日期格式。
   * 对于“时间格式”****，如果在前缀路径中使用时间标记，请指定组织文件所采用的时间格式。
   * 对于“事件序列化格式”****，选择“CSV”****。
   * 对于“分隔符”****，选择“制表符”****。
   * 对于“编码”****，选择“UTF-8”****。
    
     单击 **“创建”** 。 现在门户将添加该输出并测试与其的连接。
    
## <a name="run-the-stream-analytics-job"></a>运行流分析作业

1. 若要运行流分析作业，必须从 "**查询**" 选项卡运行查询。对于本教程，可以通过将占位符替换为作业输入和输出别名来运行示例查询，如下面的屏幕截图中所示。

    ![运行查询](./media/data-lake-store-stream-analytics/run.query.png "运行查询")

2. 单击屏幕顶部的“保存”****，并在“概述”**** 选项卡上，单击“启动”****。 从对话框中选择“自定义时间”****，并设置当前日期和时间。

    ![设置作业时间](./media/data-lake-store-stream-analytics/run.query.2.png "设置作业时间")

    单击“启动”以启动作业****。 启动作业可能会占用几分钟。

3. 要触发作业以从 blob 中选取数据，请将示例数据文件复制到 blob 容器。 可以从 [存储库](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt)获取示例数据文件。 在本教程中，让我们复制文件 **vehicle1_09142014.csv**。 可以使用各种客户端（[Azure 存储资源管理器](https://storageexplorer.com/)将数据上传到 blob 容器。

4. 在“概述”**** 选项卡上的“监视”**** 下，查看如何处理数据。

    ![监视作业](./media/data-lake-store-stream-analytics/run.query.3.png "监视作业")

5. 最后，可以验证作业输出数据是否在 Data Lake Storage Gen1 帐户中可用。 

    ![验证输出](./media/data-lake-store-stream-analytics/run.query.4.png "验证输出")

    在“数据资源管理器”窗格中，可以看到输出已写入到 Data Lake Storage Gen1 输出设置中指定的文件夹路径 (`streamanalytics/job/output/{date}/{time}`)。  

## <a name="see-also"></a>另请参阅
* [创建使用 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)
