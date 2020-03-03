---
title: 与 Azure Databricks 转换
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
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227614"
---
# <a name="transformation-with-azure-databricks"></a>与 Azure Databricks 转换

在本教程中，将创建一个端到端管道，其中包含数据工厂中的**验证**、**复制**和**笔记本**活动。

-   **验证**活动用于确保源数据集可供下游消耗，在触发复制和分析作业之前。

-   **复制**活动将源文件/数据集复制到接收器存储。 接收器存储作为 DBFS 装载在 Databricks笔记本中，因此 Spark 可以直接使用数据集。

-   **Databricks 笔记本**活动触发转换数据集的 Databricks 笔记本，并将其添加到已处理的文件夹/SQL DW。

模板不会创建计划的触发器，以确保此模板简单好用。 如有必要，可添加触发器。

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>必备条件

1. 创建一个“blob 存储帐户”和一个将用作“接收器”的名为  **的容器**`sinkdata`。 记下“存储帐户名称”、“容器名称”和“访问密钥”，因为稍后将在模板中引用它们。

2. 确保已拥有“Azure Databricks 工作区”或创建一个新工作区。

3. **导入笔记本以进行转换**。 
    1. 在 Azure Databricks 中，请参考以下屏幕截图，将**转换**笔记本导入到 Databricks 工作区。 它不必与下面的位置相同，但请记住你稍后选择的路径。
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. 选择 "导入源： **URL**"，然后在文本框中输入以下 URL：
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. 现在，让我们用存储连接信息更新**转换**笔记本。 在上面导入的笔记本中，切换到**命令 5** （如以下代码片段所示），并将 `<storage name>`和 `<access key>` 替换为你自己的存储连接信息。 确保此帐户就是先前创建的存储帐户，并确保它包含 `sinkdata` 容器。

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

5.  为数据工厂生成“Databricks 访问令牌”以访问 Databricks。 **保存访问令牌**供以后用于创建 Databricks 链接服务，其外观类似于 "dapi32db32cbb4w6eee18b7d87e45exxxxxx"。

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>如何使用此模板

1.  转到 "**转换" Azure Databricks**模板。 为以下连接创建新的链接服务。 
    
    ![连接设置](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **源 Blob 连接**–用于访问源数据。 
        
        可以使用包含此示例的源文件的公共 Blob 存储。 参考下面的配置屏幕截图。 使用以下**SAS URL**连接到源存储（只读访问）： 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **目标 Blob 连接**-用于将数据复制到中。 
        
        在接收器链接服务中，选择在**必备组件**1 中创建的存储。

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** –用于连接到 Databricks 群集。

        使用在**必备组件**中生成的访问密钥创建 Databricks 链接服务。 如果有交互式群集，可以选择该群集。 （此示例使用“新建作业群集”选项。）

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. 选择 "**使用此模板**"，将会看到创建的管道，如下所示：
    
    ![创建管道](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>管道简介和配置

在创建的新管道中，大多数设置都是使用默认值自动配置的。 查看配置并根据需要进行更新，以适合自己的设置。 有关详细信息，请查看以下说明和屏幕截图以了解参考。

1.  为执行源可用性检查而创建了验证活动**可用性标志**。 在上一步中创建的*SourceAvailabilityDataset*被选为数据集。

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  将创建复制活动**文件到 blob** ，以将数据集从源复制到接收器。 请参阅以下屏幕截图，了解复制活动中的源和接收器配置。

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  将创建一个笔记本活动**转换**，并选择在上一步中创建的链接服务。
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. 选择 "**设置**" 选项卡。对于*笔记本路径*，模板默认情况下会定义一个路径。 你可能需要浏览并选择在**必备组件**2 中上传的正确笔记本路径。 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. 查看创建的*基本参数*，如屏幕截图中所示。 它们将从数据工厂传递到 Databricks 笔记本。 

         ![基本参数](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **管道参数**的定义如下。

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. 设置数据集。
    1.  创建**SourceAvailabilityDataset**以检查源数据是否可用。

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **SourceFilesDataset** -用于复制源数据。

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **DestinationFilesDataset** -用于复制到接收器/目标位置。

        1.  链接服务- *sinkBlob_LS*在上一步中创建的。

        2.  文件路径- *sinkdata/staged_sink*。

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  选择 "**调试**" 以运行管道。 可以找到 Databricks 日志的链接以获取更详细的 Spark 日志。

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    还可以使用存储资源管理器验证数据文件。 （为了与数据工厂管道运行相关联，此示例将管道运行 ID 从数据工厂附加到输出文件夹。 这样，就可以追溯每次运行生成的文件。）

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
