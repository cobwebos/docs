---
title: 快速入门：使用 Azure 门户在 Azure Databricks 上运行 Spark 作业 | Microsoft Docs
description: 本快速入门介绍如何使用 Azure 门户创建 Azure Databricks 工作区和 Apache Spark 群集，以及如何运行 Spark 作业。
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/23/2018
ms.author: nitinme
ms.custom: mvc
ms.openlocfilehash: 19dcdeefe4a65f5c0fab06766a0fa40838df8b08
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
ms.locfileid: "30232426"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>快速入门：使用 Azure 门户在 Azure Databricks 上运行 Spark 作业

本快速入门介绍如何创建一个 Azure Databricks 工作区，并在该工作区中创建一个 Apache Spark 群集。 最后，介绍如何在 Databricks 群集中运行 Spark 作业。 有关 Azure Databricks 的详细信息，请参阅[什么是 Azure Databricks？](what-is-azure-databricks.md)

在运行本快速入门所述的 Spark 作业期间，我们将分析收音机频道订阅数据，以根据人口统计信息洞察免费/付费节目的使用情况。 

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)。

## <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

在本部分，使用 Azure 门户创建 Azure Databricks 工作区。 

1. 在 Azure 门户中，选择“创建资源” > “数据 + 分析” > “Azure Databricks”。 

    ![Azure 门户上的 Databricks](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Azure 门户上的 Databricks")

3. 在“Azure Databricks 服务”下，提供所需的值以创建 Databricks 工作区。

    ![创建 Azure Databricks 工作区](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "创建 Azure Databricks 工作区")

    提供以下值： 
     
    |属性  |说明  |
    |---------|---------|
    |**工作区名称**     | 提供 Databricks 工作区的名称        |
    |**订阅**     | 从下拉列表中选择自己的 Azure 订阅。        |
    |**资源组**     | 指定是要创建新的资源组还是使用现有的资源组。 资源组是用于保存 Azure 解决方案相关资源的容器。 有关详细信息，请参阅 [Azure 资源组概述](../azure-resource-manager/resource-group-overview.md)。 |
    |**位置**     | 选择“美国东部 2”。 有关其他可用区域，请参阅[各区域推出的 Azure 服务](https://azure.microsoft.com/regions/services/)。        |
    |**定价层**     |  选择“标准”或“高级”。 有关这些层的详细信息，请参阅 [Databricks 价格页](https://azure.microsoft.com/pricing/details/databricks/)。       |

    选择“固定到仪表板”，并单击“创建”。

4. 创建工作区需要几分钟时间。 在创建工作区过程中，门户会在右侧显示“正在提交 Azure Databricks 的部署”磁贴。 可能需要在仪表板上向右滚动才能看到此磁贴。 另外，还会在屏幕顶部附近显示进度条。 你可以查看任一区域来了解进度。

    ![Databricks 部署磁贴](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Databricks 部署磁贴")

## <a name="create-a-spark-cluster-in-databricks"></a>在 Databricks 中创建 Spark 群集

1. 在 Azure 门户中，转到所创建的 Databricks 工作区，然后单击“启动工作区”。

2. 随后将会重定向到 Azure Databricks 门户。 在该门户中，单击“群集”。

    ![Azure 上的 Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Azure 上的 Databricks")

3. 在“新建群集”页中，提供用于创建群集的值。

    ![在 Azure 上创建 Databricks Spark 群集](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "在 Azure 上创建 Databricks Spark 群集")

    除以下值外，接受其他所有默认值：

    * 输入群集的名称。
    * 在本文中，请创建运行时为 **4.0** 的群集。 
    * 请务必选中**在不活动超过 ____ 分钟后终止**复选框。 提供一个持续时间（以分钟为单位），如果群集在这段时间内一直未被使用，则会将其终止。
    
    选择“创建群集”。 群集运行后，可将笔记本附加到该群集，并运行 Spark 作业。 

有关创建群集的详细信息，请参阅[在 Azure Databricks 中创建 Spark 群集](https://docs.azuredatabricks.net/user-guide/clusters/create.html)。

## <a name="run-a-spark-sql-job"></a>运行 Spark SQL 作业

开始学习本部分之前，必须完成以下先决条件：

* [创建 Azure Blob 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account) 
* [从 Github](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json) 下载示例 JSON 文件。 
* 将示例 JSON 文件上传到所创建的 Azure Blob 存储帐户。 可以使用 [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)上传该文件。

执行以下任务在 Databricks 中创建笔记本、将该笔记本配置为从 Azure Blob 存储帐户读取数据，然后针对这些数据运行 Spark SQL 作业。

1. 在左窗格中，单击“工作区”。 在“工作区”下拉列表中，依次单击“创建”、“笔记本”。

    ![在 Databricks 中创建笔记本](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "在 Databricks 中创建笔记本")

2. 在“创建笔记本”对话框中输入一个名称，选择“Scala”作为语言，并选择前面创建的 Spark 群集。

    ![在 Databricks 中创建笔记本](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "在 Databricks 中创建笔记本")

    单击“创建”。

3. 在此步骤中，请将 Azure 存储帐户与 Databricks Spark 群集相关联。 可通过两种方法来实现此关联。 可将 Azure 存储帐户装载到 Databricks 文件系统 (DBFS)，或者直接从创建的应用程序访问 Azure 存储帐户。  

    > [!IMPORTANT]
    >本文使用**通过 DBFS 装载存储**的方式。 此方式可确保装载的存储与群集文件系统本身相关联。 因此，任何访问群集的应用程序也都可以使用关联的存储。 直接访问方式仅限通过其配置访问权限的应用程序。
    >
    > 若要使用装载方式，必须创建一个 Databricks 运行时版本为 **4.0** 的 Spark 群集，该群集是本文中选择的。

    在以下代码片段中，将 `{YOUR CONTAINER NAME}`、`{YOUR STORAGE ACCOUNT NAME}`、`{YOUR STORAGE ACCOUNT ACCESS KEY}` 替换为你的 Azure 存储帐户的相应值。 在笔记本上的某个空白单元中粘贴该代码片段，并按 SHIFT + ENTER 运行该代码单元。

    * **使用 DBFS 装载存储帐户（推荐）**。 在此代码片段中，Azure 存储帐户路径装载到 `/mnt/mypath`。 因此，在将来访问 Azure 存储帐户时，不需要提供完整路径。 可以直接使用 `/mnt/mypath`。

          dbutils.fs.mount(
            source = "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/",
            mountPoint = "/mnt/mypath",
            extraConfigs = Map("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net" -> "{YOUR STORAGE ACCOUNT ACCESS KEY}"))

    * **直接访问存储帐户**

          spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")

    有关如何检索存储帐户密钥的说明，请参阅[管理存储访问密钥](../storage/common/storage-create-storage-account.md#manage-your-storage-account)。

    > [!NOTE]
    > 还可以对 Azure Databricks 中的 Spark 群集使用 Azure Data Lake Store。 有关说明，请参阅[对 Azure Databricks 使用 Data Lake Store](https://go.microsoft.com/fwlink/?linkid=864084)。

4. 运行一条 SQL 语句，以使用示例 JSON 数据文件 **small_radio_json.json** 中的数据创建一个临时表。 在以下代码片段中，请将占位符值替换为容器名称和存储帐户名称。 在笔记本上的某个空白单元中粘贴该代码片段，并按 SHIFT + ENTER。 在该代码片段中，`path` 表示已上传到 Azure 存储帐户的示例 JSON 文件所在的位置。

    ```sql
    %sql 
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path "/mnt/mypath/small_radio_json.json"
    )
    ```

    成功完成命令后，Databricks 群集中将以表的形式列出 JSON 文件中的所有数据。

    使用 `%sql` 语言魔法 (magic) 命令可从笔记本运行 SQL 代码，即使该笔记本采用另一种类型。 有关详细信息，请参阅[在笔记本中混合使用语言](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook)。

5. 让我们看看示例 JSON 数据的快照，以便更好地了解运行的查询。 将以下代码片段粘贴到代码单元中，并按 **SHIFT + ENTER**。

    ```sql
    %sql 
    SELECT * from radio_sample_data
    ```

6. 随后将会看到以下屏幕截图中所示的表格输出（此处只显示了一部分列）：

    ![示例 JSON 数据](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "示例 JSON 数据")

    在其他详细信息中，示例数据捕获了无线电频道的听众的性别（列名为“性别”），以及这些听众的订阅是免费还是付费的（列名为“级别”）。

7. 现在创建这些数据的可视表示形式，以显示每种性别、有多少用户使用免费帐户和多少用户是付费的订户。 在表格输出的底部，单击“条形图”图标，再单击“绘图选项”。

    ![创建条形图](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "创建条形图")

8. 在“自定义绘图”中，按屏幕截图中所示拖放值。

    ![自定义条形图](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "自定义条形图")

    * 将“键”设置为“性别”。
    * 将“序列分组”设置为“级别”。
    * 将“值”设置为“级别”。
    * 将“聚合”设置为“计数”。

    单击“应用” 。

9. 输出将显示以下屏幕截图中所示的可视表示形式：

     ![自定义条形图](./media/quickstart-create-databricks-workspace-portal/databricks-sql-query-output-bar-chart.png "自定义条形图")

## <a name="clean-up-resources"></a>清理资源

完成本文后，可以终止群集。 为此，请在 Azure Databricks 工作区的左窗格中选择“群集”。 针对想要终止的群集，将光标移到“操作”列下面的省略号上，选择“终止”图标。

![停止 Databricks 群集](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "停止 Databricks 群集")

如果不手动终止群集，但在创建群集时选中了“在不活动 __ 分钟后终止”复选框，则该群集会自动停止。 在这种情况下，如果群集保持非活动状态超过指定的时间，则会自动停止。

## <a name="next-steps"></a>后续步骤

在本文中，我们已在 Azure Databricks 中创建一个 Spark 群集，并使用 Azure 存储中的数据运行了一个 Spark 作业。 我们还可以查看 [Spark 数据源](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)，了解如何将其他数据源中的数据导入 Azure Databricks。 请继续学习下一篇文章，了解如何使用 Azure Databricks 执行 ETL（提取、转换和加载数据）操作。

> [!div class="nextstepaction"]
>[使用 Azure Databricks 提取、转换和加载数据](databricks-extract-load-sql-data-warehouse.md)
