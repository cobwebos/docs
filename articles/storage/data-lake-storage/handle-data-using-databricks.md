---
title: 学习使用 Azure Databricks 执行提取、加载和转换操作
description: 学习将数据从 Azure Data Lake Storage Gen2 预览版提取到 Azure Databricks 中，对数据进行转换，然后将数据加载到 Azure SQL 数据仓库中。
services: azure-databricks
author: jamesbak
manager: jahogg
ms.component: data-lake-storage-gen2
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: jamesbak
ms.openlocfilehash: 10aad06d4ac8d76dc023648e8d6c0366bff859e6
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344688"
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>教程：使用 Azure Databricks 提取、转换和加载数据

在本教程中，你将使用 Azure Databricks 执行 ETL（提取、转换并加载数据）操作来将数据从 Azure Data Lake Storage Gen2 预览版移动到 Azure SQL 数据仓库中。

下图演示了应用程序流：

![Azure Databricks 与 Data Lake Storage Gen2 和 SQL 数据仓库](./media/handle-data-using-databricks/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks 与 Data Lake Storage Gen2 和 SQL 数据仓库")

本教程涵盖以下任务：

> [!div class="checklist"]
> * 创建 Azure Databricks 工作区
> * 在 Azure Databricks 中创建 Spark 群集
> * 创建支持 Azure Data Lake Storage Gen2 的帐户
> * 将数据上传到 Azure Data Lake Storage Gen2
> * 在 Azure Databricks 中创建 Notebook
> * 从 Data Lake Storage Gen2 提取数据
> * 在 Azure Databricks 中转换数据
> * 将数据加载到 Azure SQL 数据仓库

如果没有 Azure 订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

完成本教程：

* 创建 Azure SQL 数据仓库、创建服务器级防火墙规则并以服务器管理员身份连接到服务器。按[快速入门：创建 Azure SQL 数据仓库](../../sql-data-warehouse/create-data-warehouse-portal.md)中的说明操作
* 创建适用于 Azure SQL 数据仓库的数据库主密钥。 按[创建数据库主密钥](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key)中的说明操作。
* [创建 Azure Data Lake Storage Gen2 帐户](quickstart-create-account.md)

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

在本部分，使用 Azure 门户创建 Azure Databricks 工作区。 

1. 在 Azure 门户中，选择“创建资源” > “分析” > “Azure Databricks”。

    ![Azure 门户上的 Databricks](./media/handle-data-using-databricks/azure-databricks-on-portal.png "Azure 门户上的 Databricks")

2. 在“Azure Databricks 服务”下，提供所需的值以创建 Databricks 工作区。

    ![创建 Azure Databricks 工作区](./media/handle-data-using-databricks/create-databricks-workspace.png "创建 Azure Databricks 工作区")

    提供以下值：

    |属性  |说明  |
    |---------|---------|
    |**工作区名称**     | 为 Databricks 工作区提供一个名称。        |
    |**订阅**     | 从下拉列表中选择自己的 Azure 订阅。        |
    |**资源组**     | 指定是要创建新的资源组还是使用现有的资源组。 资源组是用于保存 Azure 解决方案相关资源的容器。 有关详细信息，请参阅 [Azure 资源组概述](../../azure-resource-manager/resource-group-overview.md)。 |
    |**位置**     | 选择“美国西部 2”。 有关其他可用区域，请参阅[各区域推出的 Azure 服务](https://azure.microsoft.com/regions/services/)。        |
    |**定价层**     |  选择“标准”或“高级”。 有关这些层的详细信息，请参阅 [Databricks 价格页](https://azure.microsoft.com/pricing/details/databricks/)。       |

    选择“固定到仪表板”，然后选择“创建”。

3. 创建帐户需要几分钟时间。 在创建帐户过程中，门户会在右侧显示“正在提交 Azure Databricks 的部署”磁贴。 可能需要在仪表板上向右滚动才能看到此磁贴。 另外，还会在屏幕顶部附近显示进度条。 你可以查看任一区域来了解进度。

    ![Databricks 部署磁贴](./media/handle-data-using-databricks/databricks-deployment-tile.png "Databricks 部署磁贴")

## <a name="create-a-spark-cluster-in-databricks"></a>在 Databricks 中创建 Spark 群集

1. 在 Azure 门户中，转到所创建的 Databricks 工作区，然后选择“启动工作区”。

2. 随后将会重定向到 Azure Databricks 门户。 在门户中选择“群集”。

    ![Azure 上的 Databricks](./media/handle-data-using-databricks/databricks-on-azure.png "Azure 上的 Databricks")

3. 在“新建群集”页中，提供用于创建群集的值。

    ![在 Azure 上创建 Databricks Spark 群集](./media/handle-data-using-databricks/create-databricks-spark-cluster.png "在 Azure 上创建 Databricks Spark 群集")

    填写以下字段的值，对于其他字段接受默认值：

    * 输入群集的名称。
    * 在本文中，请创建采用 **4.2** 版运行时的群集。
    * 请务必选中**在不活动超过 ____ 分钟后终止**复选框。 提供一个持续时间（以分钟为单位），如果群集在这段时间内一直未被使用，则会将其终止。

    选择“创建群集”。 群集运行后，可将笔记本附加到该群集，并运行 Spark 作业。

## <a name="create-storage-account-file-system"></a>创建存储帐户文件系统

在本部分中，你将在 Azure Databricks 工作区中创建一个 Notebook，然后运行代码片段来配置存储帐户。

1. 在 [Azure 门户](https://portal.azure.com)中，转到所创建的 Azure Databricks 工作区，然后选择“启动工作区”。

2. 在左窗格中选择“工作区”。 在**工作区**下拉列表中，选择**创建** > **笔记本**。

    ![在 Databricks 中创建笔记本](./media/handle-data-using-databricks/databricks-create-notebook.png "在 Databricks 中创建笔记本")

3. 在“创建 Notebook”对话框中，输入 Notebook 的名称。 选择“Scala”作为语言，然后选择前面创建的 Spark 群集。

    ![在 Databricks 中创建笔记本](./media/handle-data-using-databricks/databricks-notebook-details.png "在 Databricks 中创建笔记本")

    选择“创建”。

4. 在第一个单元格中输入以下代码并执行该代码：

    ```scala
    spark.conf.set("fs.azure.account.key.<ACCOUNT_NAME>.dfs.core.windows.net", "<ACCOUNT_KEY>") 
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
    dbutils.fs.ls("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/")
    spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false") 
    ```

    按 **SHIFT + ENTER** 运行代码单元。

    现在，已经为存储帐户创建了文件系统。

## <a name="upload-data-to-the-storage-account"></a>将数据上传到存储帐户

接下来的步骤是将示例数据文件上传到存储帐户以便稍后在 Azure Databricks 中进行转换。 

> [!NOTE]
> 如果没有支持 Azure Data Lake Storage Gen2 的帐户，请遵循[快速入门创建一个帐户](./quickstart-create-account.md)。

1. 从 [U-SQL 示例和问题跟踪](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json)存储库下载 (**small_radio_json.json**) 并记下文件的保存路径。

2. 接下来，将示例数据上传到存储帐户中。 用来将数据上传到存储帐户的方法根据是否启用了分层命名空间而有所不同。

    如果在为 Gen2 帐户创建的 Azure 存储帐户中启用了分层命名空间，则可以使用 Azure 数据工厂、distp 或 AzCopy（版本 10）来处理上传。 AzCopy 版本 10 仅可供预览版客户使用。 若要使用 AzCopy，请将以下代码粘贴到命令窗口中：

    ```bash
    set ACCOUNT_NAME=<ACCOUNT_NAME>
    set ACCOUNT_KEY=<ACCOUNT_KEY>
    azcopy cp "<DOWNLOAD_PATH>\small_radio_json.json" https://<ACCOUNT_NAME>.dfs.core.windows.net/data --recursive 
    ```
    
## <a name="extract-data-from-azure-storage"></a>从 Azure 存储中提取数据

返回到 DataBricks Notebook，并在一个新单元格中输入下面的代码：

1. 将以下代码片段添加到一个空的代码单元格中，将占位符值替换为之前在存储帐户中保存的值。

    ```scala
    dbutils.widgets.text("storage_account_name", "STORAGE_ACCOUNT_NAME", "<YOUR_STORAGE_ACCOUNT_NAME>")
    dbutils.widgets.text("storage_account_access_key", "YOUR_ACCESS_KEY", "<YOUR_STORAGE_ACCOUNT_SHARED_KEY>")
    ```

    按 **SHIFT + ENTER** 运行代码单元。

2. 现在可以将示例 json 文件加载为 Azure Databricks 中的数据帧。 将以下代码粘贴到一个新单元格中，然后按 **SHIFT + ENTER**（确保替换占位符值）：

    ```scala
    val df = spark.read.json("abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/data/small_radio_json.json")
    ```

3. 运行以下代码来查看数据帧的内容。

    ```scala
    df.show()
    ```

    会显示类似于以下代码片段的输出：

    ```bash
    +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
    |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
    +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
    | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
    | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
    | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
    ...
    ...
    ```

现在，你已将数据从 Azure Data Lake Storage Gen2 提取到 Azure Databricks 中。

## <a name="transform-data-in-azure-databricks"></a>在 Azure Databricks 中转换数据

原始示例数据 **small_radio_json.json** 捕获某个电台的听众，有多个不同的列。 在此部分，请对该数据进行转换，仅检索数据集中的特定列。

1. 一开始仅从已创建的数据帧检索 *firstName*、*lastName*、*gender*、*location* 和 *level* 列。

    ```scala
    val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
    ```

    所获得的输出如以下代码片段所示：

    ```bash
    +---------+----------+------+--------------------+-----+
    |firstname|  lastname|gender|            location|level|
    +---------+----------+------+--------------------+-----+
    | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
    |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
    |     Liam|     Watts|     M|New York-Newark-J...| paid|
    |     Tess|  Townsend|     F|Nashville-Davidso...| free|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
    |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    |     Tess|  Townsend|     F|Nashville-Davidso...| free|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |     Liam|     Watts|     M|New York-Newark-J...| paid|
    |     Liam|     Watts|     M|New York-Newark-J...| paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    |     Alan|     Morse|     M|Chicago-Napervill...| paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
    +---------+----------+------+--------------------+-----+
    ```

2.  可以进一步转换该数据，将 **level** 列重命名为 **subscription_type**。

    ```scala
    val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
    renamedColumnsDF.show()
    ```

    所获得的输出如以下代码片段所示。

    ```bash
    +---------+----------+------+--------------------+-----------------+
    |firstname|  lastname|gender|            location|subscription_type|
    +---------+----------+------+--------------------+-----------------+
    | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
    |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
    |     Liam|     Watts|     M|New York-Newark-J...|             paid|
    |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
    |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |     Liam|     Watts|     M|New York-Newark-J...|             paid|
    |     Liam|     Watts|     M|New York-Newark-J...|             paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
    |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
    |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
    +---------+----------+------+--------------------+-----------------+
    ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>将数据加载到 Azure SQL 数据仓库

在此部分，请将转换的数据上传到 Azure SQL 数据仓库中。 可以使用适用于 Azure Databricks 的 Azure SQL 数据仓库连接器直接上传数据帧，在 SQL 数据仓库中作为表来存储。

如前所述，SQL 数据仓库连接器使用 Azure Blob 存储作为临时存储，以便将数据从 Azure Databricks 上传到 Azure SQL 数据仓库。 因此，一开始请提供连接到存储帐户所需的配置。 必须已经按照本文先决条件部分的要求创建帐户。

1. 提供从 Azure Databricks 访问 Azure 存储帐户所需的配置。

    ```scala
    val storageURI = "<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net"
    val fileSystemName = "<FILE_SYSTEM_NAME>"
    val accessKey =  "<ACCESS_KEY>"
    ```

2. 指定一个在 Azure Databricks 和 Azure SQL 数据仓库之间移动数据时需要使用的临时文件夹。

    ```scala
    val tempDir = "abfs://" + fileSystemName + "@" + storageURI +"/tempDirs"
    ```

3. 运行以下代码片段，以便在配置中存储 Azure Blob 存储访问密钥。 这样可确保不需将访问密钥以纯文本形式存储在 Notebook 中。

    ```scala
    val acntInfo = "fs.azure.account.key."+ storageURI
    sc.hadoopConfiguration.set(acntInfo, accessKey)
    ```

4. 提供连接到 Azure SQL 数据仓库实例所需的值。 必须已经按先决条件的要求创建 SQL 数据仓库。

    ```scala
    //SQL Data Warehouse related settings
    val dwDatabase = "<DATABASE NAME>"
    val dwServer = "<DATABASE SERVER NAME>" 
    val dwUser = "<USER NAME>"
    val dwPass = "<PASSWORD>"
    val dwJdbcPort =  "1433"
    val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
    val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
    val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
    ```

5. 运行以下代码片段来加载转换的数据帧 **renamedColumnsDF**，在 SQL 数据仓库中作为表来存储。 此代码片段在 SQL 数据库中创建名为 **SampleTable** 的表。

    ```scala
    spark.conf.set(
        "spark.sql.parquet.writeLegacyFormat",
        "true")
    
    renamedColumnsDF.write
        .format("com.databricks.spark.sqldw")
        .option("url", sqlDwUrlSmall) 
        .option("dbtable", "SampleTable")
        .option( "forward_spark_azure_storage_credentials","True")
        .option("tempdir", tempDir)
        .mode("overwrite")
        .save()
    ```

6. 连接到 SQL 数据库，验证是否看到 **SampleTable**。

    ![验证示例表](./media/handle-data-using-databricks/verify-sample-table.png "验证示例表")

7. 运行一个 select 查询，验证表的内容。 该表的数据应该与 **renamedColumnsDF** 数据帧相同。

    ![验证示例表内容](./media/handle-data-using-databricks/verify-sample-table-content.png "验证示例表内容")

## <a name="clean-up-resources"></a>清理资源

运行完本教程后，可以终止群集。 为此，请在 Azure Databricks 工作区的左窗格中选择“群集”。 针对想要终止的群集，将光标移到“操作”列下面的省略号上，选择“终止”图标。

![停止 Databricks 群集](./media/handle-data-using-databricks/terminate-databricks-cluster.png "停止 Databricks 群集")

如果不手动终止群集，但在创建群集时选中了“在不活动 __ 分钟后终止”复选框，则该群集会自动停止。 在这种情况下，群集在处于非活动状态一定时间后会自动停止。

## <a name="next-steps"></a>后续步骤

本教程介绍了以下操作：

> [!div class="checklist"]
> * 创建 Azure Databricks 工作区
> * 在 Azure Databricks 中创建 Spark 群集
> * 创建支持 Azure Data Lake Storage Gen2 的帐户
> * 将数据上传到 Azure Data Lake Storage Gen2
> * 在 Azure Databricks 中创建 Notebook
> * 从 Data Lake Storage Gen2 提取数据
> * 在 Azure Databricks 中转换数据
> * 将数据加载到 Azure SQL 数据仓库

请继续学习下一教程，了解如何使用 Azure 事件中心将实时数据流式传输到 Azure Databricks 中。

> [!div class="nextstepaction"]
>[使用事件中心将数据流式传输到 Azure Databricks](../../azure-databricks/databricks-stream-from-eventhubs.md)
