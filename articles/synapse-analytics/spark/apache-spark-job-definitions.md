---
title: 教程：在 Synapse Studio 中创建 Apache Spark 作业定义
description: 教程 - 使用 Azure Synapse Analytics 创建 Spark 作业定义，并将定义提交到 Apache Spark for Azure Synapse Analytics 池。
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: 38678c795b0ce7534de0ca8602c1198bc35f0e05
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206185"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>教程：在 Synapse Studio 中创建 Apache Spark 作业定义

本教程演示如何使用 Azure Synapse Studio 创建 Apache Spark 作业定义，然后将其提交到 Apache Spark 池。

本教程涵盖以下任务：

* 为 PySpark (Python) 创建 Apache Spark 作业定义
* 为 Spark(Scala) 创建 Apache Spark 作业定义
* 为 .NET Spark(C#/F#) 创建 Apache Spark 作业定义
* 以批处理作业形式提交 Apache Spark 作业定义
* 将 Apache Spark 作业定义添加到管道

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，请确保满足以下要求：

* 一个 Azure Synapse Analytics 工作区。 有关说明，请参阅[创建 Azure Synapse Analytics 工作区](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)。
* Apache Spark 池
* ADLS Gen2 存储帐户。 你必须是要使用的 ADLS Gen2 文件系统的存储 Blob 数据所有者。 如果还不是该所有者，则需要手动添加权限。

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>为 PySpark (Python) 创建 Apache Spark 作业定义

在本部分中，为 PySpark (Python) 创建 Apache Spark 作业定义。

1. 打开 [Azure Synapse Studio](https://web.azuresynapse.net/)。

2. 可转到[用于创建 Apache Spark 作业定义的示例文件](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python)，下载 wordcount.py 和 shakespear.txt。 然后，将这些文件上传到 Azure 存储：单击“数据”，选择“存储帐户”，并将相关文件上传到 ADLS Gen2 文件系统 。 如果文件已在 Azure 存储中，请跳过此步骤。 

     ![上传 Python 文件](./media/apache-spark-job-definitions/upload-python-file.png)

3. 单击“开发”中心，在左窗格中选择“Spark 作业定义”，单击“Spark 作业定义”旁边的“...”操作节点，然后在上下文菜单中选择“新建 Spark 作业定义”   。

     ![为 Python 新建定义](./media/apache-spark-job-definitions/create-new-definition.png)

4. 从 Apache Spark 作业定义主窗口中的“语言”下拉列表中选择“PySpark (Python)”。

5. 填写 Apache Spark 作业定义的信息。 可复制示例信息。

     |  properties   | 说明   |  
     | ----- | ----- |  
     |作业定义名称| 输入 Apache Spark 作业定义的名称。 在发布作业定义之前，随时可以更新此名称。 示例： `job definition sample`|
     |主定义文件| 用于作业的主文件。 从存储中选择一个 PY 文件。 可以选择“上传文件”以将文件上传到存储帐户。 示例： `abfss://…/path/to/wordcount.py`|
     |命令行参数| 作业的可选参数。 示例： `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |引用文件| 用于主定义文件中的引用的其他文件。 可以选择“上传文件”以将文件上传到存储帐户。 |
     |Spark 池| 作业将提交到选定的 Apache Spark 池。|
     |Spark 版本| 正在运行 Apache Spark 池的 Apache Spark 版本。|
     |执行程序| 要在作业的指定 Apache Spark 池中提供的执行程序数目。|
     |执行程序大小| 作业的指定 Apache Spark 池中提供的执行程序要使用的核心数和内存量。|  
     |驱动程序大小| 作业的指定 Apache Spark 池中提供的驱动程序要使用的核心数和内存量。|

     ![为 Python 设置 Spark 作业定义的值](./media/apache-spark-job-definitions/create-py-definition.png)

6. 选择“发布”以保存 Apache Spark 作业定义。

     ![发布 py 定义](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>为 Apache Spark(Scala) 创建 Apache Spark 作业定义

在本部分中，为 Apache Spark(Scala) 创建 Apache Spark 作业定义。

 1. 打开 [Azure Synapse Studio](https://web.azuresynapse.net/)。

 2. 可转到[用于创建 Apache Spark 作业定义的示例文件](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala)，下载 wordcount.jar 和 shakespear.txt 。 然后，将这些文件上传到 Azure 存储：单击“数据”，选择“存储帐户”，并将相关文件上传到 ADLS Gen2 文件系统 。 如果文件已在 Azure 存储中，请跳过此步骤。 
 
     ![准备 scala 结构](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. 单击“开发”中心，在左窗格中选择“Spark 作业定义”，单击“Spark 作业定义”旁边的“...”操作节点，然后在上下文菜单中选择“新建 Spark 作业定义”   。
     ![为 scala 新建定义](./media/apache-spark-job-definitions/create-new-definition.png)

 4. 从 Apache Spark 作业定义主窗口中的“语言”下拉列表中选择“Spark(Scala)”。

 5. 填写 Apache Spark 作业定义的信息。 可复制示例信息。

     |  属性   | 说明   |  
     | ----- | ----- |  
     |作业定义名称| 输入 Apache Spark 作业定义的名称。 在发布作业定义之前，随时可以更新此名称。 示例： `job definition sample`|
     |主定义文件| 用于作业的主文件。 从存储中选择一个 JAR 文件。 可以选择“上传文件”以将文件上传到存储帐户。 示例： `abfss://…/path/to/wordcount.jar`|
     |主类名| 主定义文件中的完全限定标识符或主类。 示例： `WordCount`|
     |命令行参数| 作业的可选参数。 示例： `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |引用文件| 用于主定义文件中的引用的其他文件。 可以选择“上传文件”以将文件上传到存储帐户。|
     |Spark 池| 作业将提交到选定的 Apache Spark 池。|
     |Spark 版本| 正在运行 Apache Spark 池的 Apache Spark 版本。|
     |执行程序| 要在作业的指定 Apache Spark 池中提供的执行程序数目。|  
     |执行程序大小| 作业的指定 Apache Spark 池中提供的执行程序要使用的核心数和内存量。|
     |驱动程序大小| 作业的指定 Apache Spark 池中提供的驱动程序要使用的核心数和内存量。|

     ![为 scala 设置 Spark 作业定义的值](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. 选择“发布”以保存 Apache Spark 作业定义。

     ![发布 scala 定义](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>为 .NET Spark(C#/F#) 创建 Apache Spark 作业定义

在本部分中，为 .NET Spark(C#/F#) 创建 Apache Spark 作业定义。
 1. 打开 [Azure Synapse Studio](https://web.azuresynapse.net/)。

 2. 可转到[用于创建 Apache Spark 作业定义的示例文件](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET)，下载 wordcount.zip 和 shakespear.txt 。 然后，将这些文件上传到 Azure 存储：单击“数据”，选择“存储帐户”，并将相关文件上传到 ADLS Gen2 文件系统 。 如果文件已在 Azure 存储中，请跳过此步骤。 

     ![准备 dotnet 结构](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. 单击“开发”中心，在左窗格中选择“Spark 作业定义”，单击“Spark 作业定义”旁边的“...”操作节点，然后在上下文菜单中选择“新建 Spark 作业定义”   。

     ![为 dotnet 新建定义](./media/apache-spark-job-definitions/create-new-definition.png)

 4. 从 Apache Spark 作业定义主窗口中的“语言”下拉列表中选择“.NET Spark(C#/F#)”。

 5. 填写 Apache Spark 作业定义的信息。 可复制示例信息。
    
     |  属性   | 说明   |  
     | ----- | ----- |  
     |作业定义名称| 输入 Apache Spark 作业定义的名称。 在发布作业定义之前，随时可以更新此名称。 示例： `job definition sample`|
     |主定义文件| 用于作业的主文件。 从存储中选择包含 .NET for Apache Spark 应用程序的 ZIP 文件（即，主可执行文件、包含用户定义的函数的 DLL，以及其他所需文件）。 可以选择“上传文件”以将文件上传到存储帐户。 示例： `abfss://…/path/to/wordcount.zip`|
     |主可执行文件| 主定义 ZIP 文件中的主可执行文件。 示例： `WordCount`|
     |命令行参数| 作业的可选参数。 示例： `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |引用文件| 工作器节点所需的其他文件（即，依赖的 jar、其他用户定义的函数 DLL，以及其他配置文件），用于执行主定义 ZIP 文件中不包含的 .NET for Apache Spark 应用程序。 可选择“上传文件”，将文件上传到存储帐户。|
     |Spark 池| 作业将提交到选定的 Apache Spark 池。|
     |Spark 版本| 正在运行 Apache Spark 池的 Apache Spark 版本。|
     |执行程序| 要在作业的指定 Apache Spark 池中提供的执行程序数目。|  
     |执行程序大小| 作业的指定 Apache Spark 池中提供的执行程序要使用的核心数和内存量。|
     |驱动程序大小| 作业的指定 Apache Spark 池中提供的驱动程序要使用的核心数和内存量。|

     ![为 dotnet 设置 Spark 作业定义的值](./media/apache-spark-job-definitions/create-net-definition.png)

 6. 选择“发布”以保存 Apache Spark 作业定义。

      ![发布 dotnet 定义](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>以批处理作业形式提交 Apache Spark 作业定义

创建 Apache Spark 作业定义后，可将其提交到 Apache Spark 池。 请确保你是所要使用的 ADLS Gen2 文件系统的存储 Blob 数据所有者。 如果还不是该所有者，则需要手动添加权限。

### <a name="scenario-1-submit-apache-spark-job-definition"></a>应用场景 1：提交 Apache Spark 作业定义
 1. 单击 Apache Spark 作业定义来打开其窗口。

      ![打开要提交的 Spark 作业定义 ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. 单击“提交”图标，将项目提交到选定的 Apache Spark 池。 可单击“Spark 监视 URL”选项卡，查看 Apache Spark 应用程序的 LogQuery。

    ![单击“提交”按钮提交 Spark 作业定义](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![“Spark 提交”对话框](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>应用场景 2：查看 Apache Spark 作业运行进度

 1. 单击“监视”，然后选择“Spark 应用程序”选项。 可找到已提交的 Apache Spark 应用程序。

     ![查看 Spark 应用程序](./media/apache-spark-job-definitions/view-spark-application.png)

 2. 然后单击该 Apache Spark 应用程序，此时会显示“LogQuery”窗口。 可以在“LogQuery”中查看作业执行进度。
     
     ![查看 Spark 应用程序 - LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>应用场景 3：检查输出文件

 1. 单击“数据”，然后选择“存储帐户”。 成功运行后，可以转到 ADLS Gen2 存储，并检查是否生成了输出。

     ![查看输出文件](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>将 Apache Spark 作业定义添加到管道

在本部分中，将 Apache Spark 作业定义添加到管道。

 1. 打开现有的 Apache Spark 作业定义。

 2. 单击 Apache Spark 作业定义右上方的图标，选择“现有管道”或“新建管道” 。 有关详细信息，请参阅“管道”页面。

     ![添加到管道](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![添加到管道](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>后续步骤

本教程演示如何使用 Azure Synapse Studio 创建 Apache Spark 作业定义，然后将其提交到 Apache Spark 池。 接下来，可使用 Azure Synapse Studio 创建 Power BI 数据集并管理 Power BI 数据。

