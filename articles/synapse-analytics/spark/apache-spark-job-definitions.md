---
title: 教程：在 Synapse Studio 中创建 Apache Spark 作业定义
description: 教程 - 使用 Azure Synapse Analytics 创建 Spark 作业定义，并将定义提交到 Apache Spark for Azure Synapse Analytics 池。
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 04/15/2020
ms.openlocfilehash: 787e9efa8ef11a83b63719cad51f9b26e055620d
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90562043"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>教程：在 Synapse Studio 中创建 Apache Spark 作业定义

本教程演示如何使用 Azure Synapse Studio 创建 Apache Spark 作业定义，然后将其提交到 Apache Spark 池。

本教程涵盖以下任务：
> [!div class="checklist"]
>
> - 为 PySpark (Python) 创建 Apache Spark 作业定义
> - 为 Spark(Scala) 创建 Apache Spark 作业定义
> - 为 .NET Spark(C#/F#) 创建 Apache Spark 作业定义
> - 以批处理作业形式提交 Apache Spark 作业定义
> - 将 Apache Spark 作业定义添加到管道

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，请确保满足以下要求：

* 一个 Azure Synapse Analytics 工作区。 有关说明，请参阅[创建 Azure Synapse Analytics 工作区](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)。
* Apache Spark 池。
* ADLS Gen2 存储帐户。 你需要是要使用的 ADLS Gen2 文件系统的存储 Blob 数据所有者。 如果还不是该所有者，则需要手动添加权限。
* 如果不想使用工作区默认存储，请在 Synapse Studio 中链接所需的 ADLS Gen2 存储帐户。 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>为 PySpark (Python) 创建 Apache Spark 作业定义

在本部分中，为 PySpark (Python) 创建 Apache Spark 作业定义。

1. 打开 [Azure Synapse Studio](https://web.azuresynapse.net/)。

2. 可转到[创建 Apache Spark 作业定义的示例文件](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python)下载 python.zip 的示例文件，然后将压缩包解压缩，并提取 wordcount.py 和 shakespeare.txt 文件  。 

     ![示例文件](./media/apache-spark-job-definitions/sample-files.png)

3. 单击“数据”->“已链接”->“Azure Data Lake Storage Gen2”，然后将 wordcount.py 和 shakespeare.txt 上传到 ADLS Gen2 文件系统。 ****   ****   ****  ****   **** 

     ![上传 Python 文件](./media/apache-spark-job-definitions/upload-python-file.png)

4. 依次单击“开发”中心、“+”图标，然后选择“Spark 作业定义”以新建 Spark 作业定义 。 

     ![为 Python 新建定义](./media/apache-spark-job-definitions/create-new-definition.png)

5. 从 Apache Spark 作业定义主窗口中的“语言”下拉列表中选择“PySpark (Python)”。

     ![选择 python](./media/apache-spark-job-definitions/select-python.png)

6. 填写 Apache Spark 作业定义的信息。 

     |  属性   | 说明   |  
     | ----- | ----- |  
     |作业定义名称| 输入 Apache Spark 作业定义的名称。 在发布作业定义之前，随时可以更新此名称。 <br> 示例： `job definition sample`|
     |主定义文件| 用于作业的主文件。 从存储中选择一个 PY 文件。 可以选择“上传文件”以将文件上传到存储帐户。 <br> 示例： `abfss://…/path/to/wordcount.py`|
     |命令行参数| 作业的可选参数。 <br> 示例： `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *注意：* 示例作业定义的两个参数用空格分隔。|
     |引用文件| 用于主定义文件中的引用的其他文件。 可以选择“上传文件”以将文件上传到存储帐户。 |
     |Spark 池| 作业将提交到选定的 Apache Spark 池。|
     |Spark 版本| 正在运行 Apache Spark 池的 Apache Spark 版本。|
     |执行程序| 要在作业的指定 Apache Spark 池中提供的执行程序数目。|
     |执行程序大小| 作业的指定 Apache Spark 池中提供的执行程序要使用的核心数和内存量。|  
     |驱动程序大小| 作业的指定 Apache Spark 池中提供的驱动程序要使用的核心数和内存量。|

     ![为 Python 设置 Spark 作业定义的值](./media/apache-spark-job-definitions/create-py-definition.png)

7. 单击“发布”以保存 Apache Spark 作业定义。

     ![发布 py 定义](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>为 Apache Spark(Scala) 创建 Apache Spark 作业定义

在本部分中，为 Apache Spark(Scala) 创建 Apache Spark 作业定义。

 1. 打开 [Azure Synapse Studio](https://web.azuresynapse.net/)。

 2. 可转到[创建 Apache Spark 作业定义的示例文件](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala)下载 scala.zip 的示例文件，然后将压缩包解压缩，并提取 wordcount.jar 和 shakespeare.txt 文件  。 
 
     ![示例文件 scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. 单击“数据”->“已链接”->“Azure Data Lake Storage Gen2”，然后将 wordcount.jar 和 shakespeare.txt 上传到 ADLS Gen2 文件系统。 ****   ****   ****  ****   ****
 
     ![准备 scala 结构](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. 依次单击“开发”中心、“+”图标，然后选择“Spark 作业定义”以新建 Spark 作业定义 。 （示例图像与“为 PySpark 创建 Apache Spark 作业定义 (Python)”的步骤 4 相同。）

 5. 从 Apache Spark 作业定义主窗口中的“语言”下拉列表中选择“Spark(Scala)”。

     ![选择 scala](./media/apache-spark-job-definitions/select-scala.png)

 6. 填写 Apache Spark 作业定义的信息。 可复制示例信息。

     |  属性   | 说明   |  
     | ----- | ----- |  
     |作业定义名称| 输入 Apache Spark 作业定义的名称。 在发布作业定义之前，随时可以更新此名称。 <br> 示例： `scala`|
     |主定义文件| 用于作业的主文件。 从存储中选择一个 JAR 文件。 可以选择“上传文件”以将文件上传到存储帐户。 <br> 示例： `abfss://…/path/to/wordcount.jar`|
     |主类名| 主定义文件中的完全限定标识符或主类。 <br> 示例： `WordCount`|
     |命令行参数| 作业的可选参数。 <br> 示例： `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *注意：* 示例作业定义的两个参数用空格分隔。 |
     |引用文件| 用于主定义文件中的引用的其他文件。 可以选择“上传文件”以将文件上传到存储帐户。|
     |Spark 池| 作业将提交到选定的 Apache Spark 池。|
     |Spark 版本| 正在运行 Apache Spark 池的 Apache Spark 版本。|
     |执行程序| 要在作业的指定 Apache Spark 池中提供的执行程序数目。|  
     |执行程序大小| 作业的指定 Apache Spark 池中提供的执行程序要使用的核心数和内存量。|
     |驱动程序大小| 作业的指定 Apache Spark 池中提供的驱动程序要使用的核心数和内存量。|

     ![为 scala 设置 Spark 作业定义的值](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. 单击“发布”以保存 Apache Spark 作业定义。

      ![发布 scala 定义](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>为 .NET Spark(C#/F#) 创建 Apache Spark 作业定义

在本部分中，为 .NET Spark(C#/F#) 创建 Apache Spark 作业定义。
 1. 打开 [Azure Synapse Studio](https://web.azuresynapse.net/)。

 2. 可转到[创建 Apache Spark 作业定义的示例文件](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET)下载 dotnet.zip 的示例文件，然后将压缩包解压缩，并提取 wordcount.zip 和 shakespeare.txt 文件  。 

     ![示例 dotnet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. 单击“数据”->“已链接”->“Azure Data Lake Storage Gen2”，然后将 wordcount.zip 和 shakespeare.txt 上传到 ADLS Gen2 文件系统。 ****   ****   ****  ****   ****
 
     ![准备 dotnet 结构](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. 依次单击“开发”中心、“+”图标，然后选择“Spark 作业定义”以新建 Spark 作业定义 。 （示例图像与“为 PySpark 创建 Apache Spark 作业定义 (Python)”的步骤 4 相同。）

 5. 从 Apache Spark 作业定义主窗口中的“语言”下拉列表中选择“.NET Spark(C#/F#)”。

     ![选择 dotnet](./media/apache-spark-job-definitions/select-dotnet.png)

 6. 填写 Apache Spark 作业定义的信息。 可复制示例信息。
    
     |  属性   | 说明   |  
     | ----- | ----- |  
     |作业定义名称| 输入 Apache Spark 作业定义的名称。 在发布作业定义之前，随时可以更新此名称。 <br> 示例： `dotnet`|
     |主定义文件| 用于作业的主文件。 从存储中选择包含 .NET for Apache Spark 应用程序的 ZIP 文件（即，主可执行文件、包含用户定义的函数的 DLL，以及其他所需文件）。 可以选择“上传文件”以将文件上传到存储帐户。 <br> 示例： `abfss://…/path/to/wordcount.zip`|
     |主可执行文件| 主定义 ZIP 文件中的主可执行文件。 <br> 示例： `WordCount`|
     |命令行参数| 作业的可选参数。 <br> 示例： `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *注意：* 示例作业定义的两个参数用空格分隔。 |
     |引用文件| 工作器节点所需的其他文件（即，依赖的 jar、其他用户定义的函数 DLL，以及其他配置文件），用于执行主定义 ZIP 文件中不包含的 .NET for Apache Spark 应用程序。 可选择“上传文件”，将文件上传到存储帐户。|
     |Spark 池| 作业将提交到选定的 Apache Spark 池。|
     |Spark 版本| 正在运行 Apache Spark 池的 Apache Spark 版本。|
     |执行程序| 要在作业的指定 Apache Spark 池中提供的执行程序数目。|  
     |执行程序大小| 作业的指定 Apache Spark 池中提供的执行程序要使用的核心数和内存量。|
     |驱动程序大小| 作业的指定 Apache Spark 池中提供的驱动程序要使用的核心数和内存量。|

     ![为 dotnet 设置 Spark 作业定义的值](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. 单击“发布”以保存 Apache Spark 作业定义。

      ![发布 dotnet 定义](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>以批处理作业形式提交 Apache Spark 作业定义

创建 Apache Spark 作业定义后，可将其提交到 Apache Spark 池。 请确保你是所要使用的 ADLS Gen2 文件系统的存储 Blob 数据所有者。 如果还不是该所有者，则需要手动添加权限。

### <a name="scenario-1-submit-apache-spark-job-definition"></a>应用场景 1：提交 Apache Spark 作业定义
 1. 单击 Apache Spark 作业定义来打开其窗口。

      ![打开要提交的 Spark 作业定义 ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. 单击“提交”按钮，将项目提交到选定的 Apache Spark 池。 可单击“Spark 监视 URL”选项卡，查看 Apache Spark 应用程序的 LogQuery。

    ![单击“提交”按钮提交 Spark 作业定义](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![“Spark 提交”对话框](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>应用场景 2：查看 Apache Spark 作业运行进度

 1. 单击“监视”，然后选择“Apache Spark 应用程序”选项。  可找到已提交的 Apache Spark 应用程序。

     ![查看 Spark 应用程序](./media/apache-spark-job-definitions/view-spark-application.png)

 2. 然后单击 Apache Spark 应用程序，此时会显示 SparkJobDefinition 作业窗口。 可在此处查看作业执行进度。
     
     ![查看 Spark 应用程序 - LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>应用场景 3：检查输出文件

 1. 单击“数据” -> “已链接” -> “Azure Data Lake Storage Gen2”(hozhaobdbj)，然后打开之前创建的“结果”文件夹，可转到结果文件夹查看是否已生成输出   。

     ![查看输出文件](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>将 Apache Spark 作业定义添加到管道

在本部分中，将 Apache Spark 作业定义添加到管道。

 1. 打开现有的 Apache Spark 作业定义。

 2. 单击 Apache Spark 作业定义右上方的图标，选择“现有管道”或“新建管道” 。 有关详细信息，请参阅“管道”页面。

     ![添加到管道 1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![添加到管道 2](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>后续步骤

本教程演示如何使用 Azure Synapse Studio 创建 Apache Spark 作业定义，然后将其提交到 Apache Spark 池。 接下来，可使用 Azure Synapse Studio 创建 Power BI 数据集并管理 Power BI 数据。

