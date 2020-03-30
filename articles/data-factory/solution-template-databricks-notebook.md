---
title: 使用 Azure 数据块进行转换
description: 了解如何使用解决方案模板，通过在 Azure 数据工厂中使用 Databricks 笔记本转换数据。
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 9a05b09f958d741fa56c586fbc7f5c5908dbbce6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384375"
---
# <a name="transformation-with-azure-databricks"></a>使用 Azure 数据块进行转换

在本教程中，您将创建一个端到端管道，其中包含 Azure 数据工厂中的**验证**、**复制数据和****笔记本**活动。

- **在**触发复制和分析作业之前，验证可确保源数据集已准备好用于下游使用。

- **复制数据**将源数据集复制到接收器存储，该存储作为 DBFS 装载在 Azure 数据块笔记本中。 这样，Spark 可以直接使用数据集。

- **笔记本**将触发转换数据集的数据砖笔记本。 它还将数据集添加到已处理的文件夹或 Azure SQL 数据仓库。

为简单起见，本教程中的模板不会创建计划触发器。 如有必要，可以添加一个。

![管道图](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>先决条件

- 具有用作`sinkdata`接收器的容器的 Azure Blob 存储帐户。

  记下存储帐户名称、容器名称和访问密钥。 稍后将需要在模板中使用这些值。

- Azure 数据块工作区。

## <a name="import-a-notebook-for-transformation"></a>导入用于转换的笔记本

要将**转换**笔记本导入数据砖块工作区，请：

1. 登录到 Azure 数据块工作区，然后选择 **"导入**"。
       ![用于导入工作区](media/solution-template-Databricks-notebook/import-notebook.png)的菜单命令您的工作区路径可能不同于显示的路径，但请稍后记住它。
1. 选择**从：URL**导入。 在文本框中，输入`https://adflabstaging1.blob.core.windows.net/share/Transformations.html`。

   ![导入笔记本的选择](media/solution-template-Databricks-notebook/import-from-url.png)

1. 现在，让我们使用存储连接信息更新**转换**笔记本。

   在导入的笔记本中，转到**命令 5，** 如以下代码段所示。

   - 替换`<storage name>`并`<access key>`使用您自己的存储连接信息。
   - 将存储帐户与容器一`sinkdata`起使用。

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. 为数据工厂生成“Databricks 访问令牌”以访问 Databricks****。
   1. 在 DataBRICKS 工作区中，选择右上角的用户配置文件图标。
   1. 选择**用户设置**。
    ![用户设置的菜单命令](media/solution-template-Databricks-notebook/user-setting.png)
   1. 在 **"访问令牌"** 选项卡下选择 **"生成新令牌**"。
   1. 然后选择“生成”****。

    !["生成"按钮](media/solution-template-Databricks-notebook/generate-new-token.png)

   *保存访问令牌*以供以后用于创建数据砖块链接服务。 访问令牌类似于`dapi32db32cbb4w6eee18b7d87e45exxxxxx`。

## <a name="how-to-use-this-template"></a>如何使用此模板

1. 转到使用**Azure 数据砖块模板的转换**，并创建用于以下连接的新链接服务。

   ![连接设置](media/solution-template-Databricks-notebook/connections-preview.png)

    - **源 Blob 连接**- 访问源数据。

       在本练习中，可以使用包含源文件的公共 Blob 存储。 参考以下配置屏幕截图。 使用以下**SAS URL**连接到源存储（只读访问）：

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![身份验证方法和 SAS URL 的选择](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **目标 Blob 连接**- 存储复制的数据。

       在 **"新建链接服务"** 窗口中，选择接收器存储 Blob。

       ![将存储 Blob 作为新的链接服务](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure 数据块**- 连接到数据砖群集。

        使用以前生成的访问密钥创建与 Databricks 链接的服务。 如果您有*交互式群集*，可以选择选择交互式群集。 此示例使用 **"新建作业群集"** 选项。

        ![连接到群集的选择](media/solution-template-Databricks-notebook/databricks-connection.png)

1. 选择“使用此模板”****。 您将看到已创建的管道。

    ![创建管道](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>管道介绍和配置

在新管道中，大多数设置都自动配置默认值。 查看管道的配置并进行任何必要的更改。

1. 在**验证**活动**可用性标志**中，验证源**数据集**值是否设置为`SourceAvailabilityDataset`您之前创建的。

   ![源数据集值](media/solution-template-Databricks-notebook/validation-settings.png)

1. 在 **"将数据**活动**文件复制到 blob"** 中，检查 **"源**"和"**接收器"** 选项卡。 如有必要，更改设置。

   - **源**选项卡!["源"选项卡](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **接收器**选项卡!["接收器"选项卡](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. 在**笔记本**活动**转换中**，根据需要查看和更新路径和设置。

   **数据砖链接服务**应预填充上一步的值，如图所示：![数据砖块链接服务的填充值](media/solution-template-Databricks-notebook/notebook-activity.png)

   要检查**笔记本**设置：
  
    1. 选择 **"设置"** 选项卡。对于**笔记本路径**，验证默认路径是否正确。 您可能需要浏览并选择正确的笔记本路径。

       ![笔记本路径](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. 展开**基本参数**选择器并验证参数是否与以下屏幕截图中显示的参数匹配。 这些参数从数据工厂传递到数据砖块笔记本。

       ![基本参数](media/solution-template-Databricks-notebook/base-parameters.png)

1. 验证**管道参数**与以下屏幕截图中显示的内容匹配：![管道参数](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. 连接到数据集。

   - **源可用性数据集**- 检查源数据是否可用。

     ![源可用性数据集的链接服务和文件路径的选择](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **源文件数据集**- 访问源数据。

       ![源文件数据集的链接服务和文件路径的选择](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **目标文件数据集**- 将数据复制到接收器目标位置。 使用以下值：

     - **链接服务** - `sinkBlob_LS`，在上一步中创建。

     - **文件路径** - `sinkdata/staged_sink`。

       ![目标文件数据集的链接服务和文件路径的选择](media/solution-template-Databricks-notebook/destination-dataset.png)

1. 选择**调试**以运行管道。 您可以找到指向 Databricks 日志的链接，以获取更详细的 Spark 日志。

    ![从输出链接到数据砖块日志](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    您还可以使用 Azure 存储资源管理器验证数据文件。

    > [!NOTE]
    > 对于与数据工厂管道运行相关的操作，此示例将管道运行 ID 从数据工厂追加到输出文件夹。 这有助于跟踪每次运行生成的文件。
    > ![附加管道运行 ID](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
