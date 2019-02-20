---
title: 在 Azure 数据工厂中使用 Databricks 转换数据 | Microsoft Docs
description: 了解如何使用解决方案模板，通过在 Azure 数据工厂中使用 Databricks 笔记本转换数据。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 3d7fd9b3e7ab8e5f193fd3b34a804b9ed0780d66
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105682"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>在 Azure 数据工厂中使用 Databricks 转换数据

在本教程中，你将在数据工厂中创建包含“查找”、“复制”和“Databricks 笔记本”活动的端到端管道。

-   “查找”或 GetMetadata 活动用于在触发复制和分析作业之前确保源数据集已准备好供下游使用。

-   “复制活动”将源文件/数据集复制到接收器存储。 接收器存储作为 DBFS 装载在 Databricks笔记本中，因此 Spark 可以直接使用数据集。

-   “Databricks 笔记本活动”触发转换数据集的 Databricks 笔记本，并将它添加到已处理的文件夹/SQL DW 中。

模板不会创建计划的触发器，以确保此模板简单好用。 如有必要，可添加触发器。

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>先决条件

1.  创建一个“blob 存储帐户”和一个将用作“接收器”的名为 `sinkdata` 的容器。 记下“存储帐户名称”、“容器名称”和“访问密钥”，因为稍后将在模板中引用它们。

2.  确保已拥有“Azure Databricks 工作区”或创建一个新工作区。

1.  导入用于 ETL 的笔记本。 将下面的转换笔记本导入 Databricks 工作区。 （它不必位于如下所示的相同位置，请记住你选择的路径供稍后使用。）在 URL 字段中输入此 URL，从以下 URL 导入笔记本：`https://Data Factorylabstaging1.blob.core.windows.net/share/Transformations.html`。 选择“导入”。

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  现在，使用“存储连接信息”（名称和访问密钥）更新“转换”笔记本。 转到上面导入的笔记本中的“命令 5”，在替换突出显示的值后将它替换为以下代码片段。 确保此帐户就是先前创建的存储帐户，并确保它包含 `sinkdata` 容器。

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

1.  为数据工厂生成“Databricks 访问令牌”以访问 Databricks。 保存访问令牌供稍后用于创建 Databricks 链接服务，该令牌类似于“dapi32db32cbb4w6eee18b7d87e45exxxxxx”

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>创建链接的服务和数据集

1.  通过转到“连接链接服务 + 新建”，在数据工厂 UI 中创建新的“链接服务”

    1.  **源** - 用于访问源数据。 可以使用包含此示例的源文件的公共 Blob 存储。

        选择“Blob 存储”，使用以下“SAS URI”连接到源存储（只读访问）。

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **接收器** - 将数据复制到其中。

        在接收器链接服务中选择先决条件 1 中创建的存储。

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** - 用于连接 Databricks 群集

        使用先决条件 2.c 中生成的访问密钥创建 Databricks 链接服务。 如果有交互式群集，可以选择该群集。 （此示例使用“新建作业群集”选项。）

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  创建“数据集”

    1.  创建“sourceAvailability_Dataset”检查源数据是否可用

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **源数据集** - 用于复制源数据（使用二进制文件副本）

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **接收器数据集** - 用于复制到接收器/目标位置

        1.  链接的服务 - 选择在 1.b 中创建的“sinkBlob_LS”

        2.  文件路径 -“sinkdata/staged_sink”

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>创建活动

1.  创建查找活动“可用性标记”以执行源可用性检查（可以使用 Lookup 或 GetMetadata）。 选择 2.a 中创建的“sourceAvailability_Dataset”。

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  创建复制活动“file-to-blob”，用于将数据集从源复制到接收器。 在这种情况下，数据是二进制文件。 请参阅以下屏幕截图，了解复制活动中的源和接收器配置。

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  定义“管道参数”

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  创建“Databricks 活动”

    选择在上一步中创建的链接服务。

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    配置“设置”。 如屏幕截图所示创建“基本参数”，并创建要从数据工厂传递到 Databricks 笔记本的参数。 浏览并选择在“先决条件 2”中上传的正确笔记本路径。

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  运行管道。 可以找到 Databricks 日志的链接以获取更详细的 Spark 日志。

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    还可以使用存储资源管理器验证数据文件。 （为了与数据工厂管道运行相关联，此示例将管道运行 ID 从数据工厂附加到输出文件夹。 这样，就可以追溯每次运行生成的文件。）

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>后续步骤

- [Azure 数据工厂简介](introduction.md)
