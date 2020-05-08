---
title: 使用 Azure Databricks 进行转换
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
ms.date: 04/27/2020
ms.openlocfilehash: 2503c26ac0348739bbf117c3538af797833ce8b8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857639"
---
# <a name="transformation-with-azure-databricks"></a>使用 Azure Databricks 进行转换

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教程中，将创建一个端到端管道，其中包含 Azure 数据工厂中的**验证**、**复制数据**和**笔记本**活动。

- **验证**可确保你的源数据集可供下游消耗，然后才能触发复制和分析作业。

- **复制数据**会将源数据集复制到在 Azure Databricks 笔记本中作为 DBFS 安装的接收器存储。 这样，Spark 就可以直接使用数据集。

- **笔记本**触发转换数据集的 Databricks 笔记本。 它还将数据集添加到已处理的文件夹或 Azure SQL 数据仓库。

为简单起见，本教程中的模板不会创建计划的触发器。 如果需要，可以添加一个。

![管道的关系图](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>必备条件

- 一个 Azure Blob 存储帐户，其中有一个`sinkdata`名为的容器，用作接收器。

  记下 "存储帐户名称"、"容器名称" 和 "访问密钥"。 稍后，模板中将需要这些值。

- Azure Databricks 工作区。

## <a name="import-a-notebook-for-transformation"></a>导入要转换的笔记本

将**转换**笔记本导入到 Databricks 工作区：

1. 登录到 Azure Databricks 工作区，然后选择 "**导入**"。
       ![用于导入工作区](media/solution-template-Databricks-notebook/import-notebook.png)的菜单命令你的工作区路径可以不同于显示的项，但请记住它。
1. 选择 "**导入源： URL**"。 在文本框中，输入`https://adflabstaging1.blob.core.windows.net/share/Transformations.html`。

   ![导入笔记本的选择](media/solution-template-Databricks-notebook/import-from-url.png)

1. 现在，让我们用存储连接信息更新**转换**笔记本。

   在导入的笔记本中，按照以下代码片段所示，中转到**命令 5** 。

   - 将`<storage name>`和`<access key>`替换为你自己的存储连接信息。
   - 使用具有`sinkdata`容器的存储帐户。

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
   1. 在 Databricks 工作区中，选择右上角的 "用户配置文件" 图标。
   1. 选择 "**用户设置**"。
    ![用户设置的菜单命令](media/solution-template-Databricks-notebook/user-setting.png)
   1. 在 "**访问令牌**" 选项卡下选择 "**生成新令牌**"。
   1. 然后选择“生成”****。

    !["生成" 按钮](media/solution-template-Databricks-notebook/generate-new-token.png)

   *保存访问令牌*供以后用于创建 Databricks 链接服务。 访问令牌类似于`dapi32db32cbb4w6eee18b7d87e45exxxxxx`。

## <a name="how-to-use-this-template"></a>如何使用此模板

1. 转到 " **Azure Databricks 模板转换**，并为以下连接创建新的链接服务。

   ![连接设置](media/solution-template-Databricks-notebook/connections-preview.png)

    - **源 Blob 连接**-用于访问源数据。

       对于本练习，可以使用包含源文件的公共 blob 存储。 引用此配置的以下屏幕截图。 使用以下**SAS URL**连接到源存储（只读访问）：

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![选择身份验证方法和 SAS URL](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **目标 Blob 连接**-用于存储复制的数据。

       在 "**新建链接服务**" 窗口中，选择接收器存储 blob。

       ![将存储 blob 作为新的链接服务接收器](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** -连接到 Databricks 群集。

        使用之前生成的访问密钥创建 Databricks 链接服务。 如果有一个*交互式群集*，可以选择将其选中。 此示例使用**新的作业群集**选项。

        ![用于连接到群集的选项](media/solution-template-Databricks-notebook/databricks-connection.png)

1. 选择“使用此模板”  。 你将看到已创建的管道。

    ![创建管道](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>管道简介和配置

在新管道中，大多数设置都是用默认值自动配置的。 查看管道的配置，并进行任何必要的更改。

1. 在 "**验证**活动**可用性" 标志**中，验证 "源**数据集**" 值`SourceAvailabilityDataset`是否设置为之前创建的。

   ![源数据集值](media/solution-template-Databricks-notebook/validation-settings.png)

1. 在 "**将数据**活动**文件复制到 blob**" 中，检查 "**源**" 和 "**接收器**" 选项卡。 如有必要，请更改设置。

   - **源**选项![卡源选项卡](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **接收器**选项![卡接收器选项卡](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. 在**笔记本**活动**转换**中，根据需要查看并更新路径和设置。

   应使用上一步中的值预填充**Databricks 链接服务**，如下所示： ![Databricks 链接服务的填充值](media/solution-template-Databricks-notebook/notebook-activity.png)

   检查**笔记本**设置：
  
    1. 选择 "**设置**" 选项卡。对于**笔记本路径**，请验证默认路径是否正确。 你可能需要浏览并选择正确的笔记本路径。

       ![笔记本路径](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. 展开 "**基参数**" 选择器，并验证参数是否与以下屏幕截图中显示的内容相匹配。 这些参数将从数据工厂传递到 Databricks 笔记本。

       ![基本参数](media/solution-template-Databricks-notebook/base-parameters.png)

1. 验证**管道参数**是否与以下屏幕截图中显示的内容匹配： ![管道参数](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. 连接到数据集。

    >[!NOTE]
    >在下面的数据集内，已在模板中自动指定文件路径。 如果需要进行任何更改，请确保为**容器**和**目录**指定路径，以防出现任何连接错误。

   - **SourceAvailabilityDataset** -检查源数据是否可用。

     ![SourceAvailabilityDataset 的链接服务和文件路径的选择](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** -访问源数据。

       ![SourceFilesDataset 的链接服务和文件路径的选择](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** -将数据复制到接收器目标位置。 使用以下值：

     - **Linked service** - `sinkBlob_LS`在上一步中创建的链接服务。

     - **文件路径** - `sinkdata/staged_sink`。

       ![DestinationFilesDataset 的链接服务和文件路径的选择](media/solution-template-Databricks-notebook/destination-dataset.png)

1. 选择 "**调试**" 以运行管道。 可以在 Databricks 日志中找到有关更详细的 Spark 日志的链接。

    ![从输出链接到 Databricks 日志](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    你还可以使用 Azure 存储资源管理器验证数据文件。

    > [!NOTE]
    > 为了与数据工厂管道运行相关联，此示例将数据工厂中的管道运行 ID 追加到输出文件夹。 这有助于跟踪每次运行生成的文件。
    > ![追加的管道运行 ID](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
