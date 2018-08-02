---
title: 教程：使用 Azure Databricks 执行 ETL 操作
description: 了解如何将数据从 Data Lake Store 提取到 Azure Databricks 中，对数据进行转换，然后将数据加载到 Azure SQL 数据仓库中。
services: azure-databricks
author: nitinme
ms.author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 07/26/2018
ms.openlocfilehash: 11046089bd25e1ca9e117d5d8908471858450e6d
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308790"
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>教程：使用 Azure Databricks 提取、转换和加载数据

本教程使用 Azure Databricks 执行 ETL（提取、转换和加载数据）操作。 将数据从 Azure Data Lake Store 提取到 Azure Databricks 中，在 Azure Databricks 中对数据运行转换操作，然后将转换的数据加载到 Azure SQL 数据仓库中。 

本教程中的步骤使用 Azure Databricks 的 SQL 数据仓库连接器将数据传输到 Azure Databricks。 而此连接器又使用 Azure Blob 存储来临时存储在 Azure Databricks 群集和 Azure SQL 数据仓库之间传输的数据。

下图演示了应用程序流：

![Azure Databricks 与 Data Lake Store 和 SQL 数据仓库](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks 与 Data Lake Store 和 SQL 数据仓库")

本教程涵盖以下任务： 

> [!div class="checklist"]
> * 创建 Azure Databricks 工作区
> * 在 Azure Databricks 中创建 Spark 群集
> * 创建 Azure Data Lake Store 帐户
> * 将数据上传到 Azure Data Lake Store
> * 在 Azure Databricks 中创建 Notebook
> * 从 Data Lake Store 提取数据
> * 在 Azure Databricks 中转换数据
> * 将数据加载到 Azure SQL 数据仓库

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，请确保满足以下要求：
- 创建 Azure SQL 数据仓库、创建服务器级防火墙规则并以服务器管理员身份连接到服务器。按[快速入门：创建 Azure SQL 数据仓库](../sql-data-warehouse/create-data-warehouse-portal.md)中的说明操作
- 为 Azure SQL 数据仓库创建数据库主密钥。 按[创建数据库主密钥](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key)中的说明操作。
- 创建 Azure Blob 存储帐户并在其中创建容器。 另外，请检索用于访问该存储帐户的访问密钥。 按[快速入门：创建 Azure Blob 存储帐户](../storage/blobs/storage-quickstart-blobs-portal.md)中的说明操作。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

在本部分，使用 Azure 门户创建 Azure Databricks 工作区。 

1. 在 Azure 门户中，选择“创建资源” > “数据 + 分析” > “Azure Databricks”。

    ![Azure 门户上的 Databricks](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Azure 门户上的 Databricks")

3. 在“Azure Databricks 服务”下，提供所需的值以创建 Databricks 工作区。

    ![创建 Azure Databricks 工作区](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "创建 Azure Databricks 工作区")

    提供以下值： 
     
    |属性  |说明  |
    |---------|---------|
    |**工作区名称**     | 提供 Databricks 工作区的名称        |
    |**订阅**     | 从下拉列表中选择自己的 Azure 订阅。        |
    |**资源组**     | 指定是要创建新的资源组还是使用现有的资源组。 资源组是用于保存 Azure 解决方案相关资源的容器。 有关详细信息，请参阅 [Azure 资源组概述](../azure-resource-manager/resource-group-overview.md)。 |
    |**位置**     | 选择“美国东部 2”。 有关其他可用区域，请参阅[各区域推出的 Azure 服务](https://azure.microsoft.com/regions/services/)。        |
    |**定价层**     |  选择“标准”或“高级”。 有关这些层的详细信息，请参阅 [Databricks 价格页](https://azure.microsoft.com/pricing/details/databricks/)。       |

    选择“固定到仪表板”，然后选择“创建”。

4. 创建帐户需要几分钟时间。 在创建帐户过程中，门户会在右侧显示“正在提交 Azure Databricks 的部署”磁贴。 可能需要在仪表板上向右滚动才能看到此磁贴。 另外，还会在屏幕顶部附近显示进度条。 你可以查看任一区域来了解进度。

    ![Databricks 部署磁贴](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Databricks 部署磁贴")

## <a name="create-a-spark-cluster-in-databricks"></a>在 Databricks 中创建 Spark 群集

1. 在 Azure 门户中，转到所创建的 Databricks 工作区，然后选择“启动工作区”。

2. 随后将会重定向到 Azure Databricks 门户。 在门户中选择“群集”。

    ![Azure 上的 Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Azure 上的 Databricks")

3. 在“新建群集”页中，提供用于创建群集的值。

    ![在 Azure 上创建 Databricks Spark 群集](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "在 Azure 上创建 Databricks Spark 群集")

    除以下值外，接受其他所有默认值：

    * 输入群集的名称。
    * 在本文中，请创建运行时为 **4.0** 的群集。 
    * 请务必选中**在不活动超过 ____ 分钟后终止**复选框。 提供一个持续时间（以分钟为单位），如果群集在这段时间内一直未被使用，则会将其终止。
    
    选择“创建群集”。 群集运行后，可将笔记本附加到该群集，并运行 Spark 作业。

## <a name="create-an-azure-data-lake-store-account"></a>创建 Azure Data Lake Store 帐户

在此部分，请创建 Azure Data Lake Store 帐户并将 Azure Active Directory 服务主体与之相关联。 本教程的后面部分将在 Azure Databricks 中使用该服务主体访问 Azure Data Lake Store。 

1. 在 [Azure 门户](https://portal.azure.com)中选择“创建资源” > “存储” > “Data Lake Store”。
3. 在“新建 Data Lake Store”边栏选项卡中，提供以下屏幕截图中所示的值：
   
    ![创建新的 Azure Data Lake Store 帐户](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "创建新的 Azure Data Lake 帐户")

    提供以下值： 
     
    |属性  |说明  |
    |---------|---------|
    |**Name**     | 输入 Data Lake Store 帐户的唯一名称。        |
    |**订阅**     | 从下拉列表中选择自己的 Azure 订阅。        |
    |**资源组**     | 对于本教程，请选择创建 Azure Databricks 工作区时所使用的资源组。  |
    |**位置**     | 选择“美国东部 2”。  |
    |**定价包**     |  选择“即用即付”。 |
    | **加密设置** | 保留默认设置。 |

    选择“固定到仪表板”，然后选择“创建”。

现在创建 Azure Active Directory 服务主体并将其与所创建的 Data Lake Store 帐户相关联。

### <a name="create-an-azure-active-directory-service-principal"></a>创建 Azure Active Directory 服务主体
   
1. 在 [Azure 门户](https://portal.azure.com)中选择“所有服务”，然后搜索“Azure Active Directory”。

2. 选择“应用注册”。

   ![选择应用注册](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. 选择“新建应用程序注册”。

   ![添加应用](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. 为应用提供名称和 URL。 选择“Web 应用/API”作为要创建的应用类型。 提供登录 URL，然后选择“创建”。

   ![命名应用程序](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

若要从 Azure Databricks 访问 Data Lake Store 帐户，必须提供所创建的 Azure Active Directory 服务主体的以下值：
- 应用程序 ID
- 身份验证密钥
- 租户 ID

在以下部分，请检索此前创建的 Azure Active Directory 服务主体的这些值。

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>获取服务主体的应用程序 ID 和身份验证密钥

以编程方式登录时，需要使用应用程序的 ID 和身份验证密钥。 若要获取这些值，请使用以下步骤：

1. 从 Azure Active Directory 中的“应用注册”，选择应用程序。

   ![选择应用程序](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. 复制“应用程序 ID”并将其存储在应用程序代码中。 某些[示例应用程序](#log-in-as-the-application)将此值作为客户端 ID。

   ![客户端 ID](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. 若要生成身份验证密钥，请选择“设置”。

   ![选择“设置”](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. 若要生成身份验证密钥，请选择“密钥”。

   ![选择密钥](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. 提供密钥说明和密钥持续时间。 完成后，选择“保存”。

   ![保存密钥](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   保存密钥后, 会显示密钥的值。 复制此值，因为稍后不能检索密钥。 提供密钥值及应用程序 ID 登录为该应用程序。 将密钥值存储在应用程序可检索的位置。

   ![保存的密钥](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>获取租户 ID

以编程方式登录时，需要随身份验证请求传递租户 ID。

1. 选择“Azure Active Directory”。

   ![选择 azure active directory](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. 若要获取租户 ID，请选择 Azure AD 租户的“属性”。

   ![选择 Azure AD 属性](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. 复制“目录 ID”。 此值即为租户 ID。

   ![租户 ID](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png) 

## <a name="upload-data-to-data-lake-store"></a>将数据上传到 Data Lake Store

在此部分，请将示例数据文件上传到 Data Lake Store。 稍后需在 Azure Databricks 中使用此文件来运行一些转换操作。 本教程所使用的示例数据 (**small_radio_json.json**) 在此 [Github 存储库](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json)中提供。

1. 在 [Azure 门户](https://portal.azure.com)中选择所创建的 Data Lake Store 帐户。

2. 在“概览”选项卡中，单击“数据资源管理器”。

    ![打开数据资源管理器](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "打开数据资源管理器")

3. 在“数据资源管理器”中，单击“上传”。

    ![上传选项](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "上传选项")

4. 在“上传文件”中，导航到示例数据文件的位置，然后选择“添加所选文件”。

    ![上传选项](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "上传选项")

5. 在本教程中，你已经将数据文件上传到 Data Lake Store 的根目录。 因此，该文件现在位于 `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json`。

## <a name="associate-service-principal-with-azure-data-lake-store"></a>将服务主体与 Azure Data Lake Store 相关联

在本部分中，将 Azure Data Lake Store 帐户中的数据与已创建的 Azure Active Directory 服务主体相关联。 这样可确保能够从 Azure Databricks 访问 Data Lake Store 帐户。 对于本文中的场景，你将读取 Data Lake Store 中的数据来填充 SQL 数据仓库中的表。 根据 [Data Lake Store 中的访问控制概述](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)，要想对 Data Lake Store 中的某个文件具有读取访问权限，必须：

- 对文件夹结构中直至该文件的所有文件夹具有**执行**权限。
- 对该文件本身具有**读取**权限。

可以执行以下步骤来授予这些权限。

1. 在 [Azure 门户](https://portal.azure.com)中，选择你创建的 Data Lake Store 帐户，然后选择“数据资源管理器”。

    ![启动数据资源管理器](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-data-explorer.png "启动数据资源管理器")

2. 在此场景中，因为示例数据文件位于文件夹结构的根目录中，因此只需要在文件夹根目录处分配**执行**权限。 若要执行此操作，请从数据资源管理器的根节点选择“访问权限”。

    ![为文件夹添加 ACL](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-1.png "为文件夹添加 ACL")

3. 在“访问权限”下，选择“添加”。

    ![为文件夹添加 ACL](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-2.png "为文件夹添加 ACL")

4. 在“分配权限”下，单击“选择用户或组”并搜索你之前创建的 Azure Active Directory 服务主体。

    ![添加 Data Lake Store 访问权限](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "添加 Data Lake Store 访问权限")

    选择要分配的 AAD 服务主体并单击“选择”。

5. 在“分配权限”下，单击“选择权限” > “执行”。 依次在“选择权限”下和“分配权限”下保留其他默认值并选择“确定”。

    ![添加 Data Lake Store 访问权限](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-4.png "添加 Data Lake Store 访问权限")

6. 返回到数据资源管理器，现在单击要分配读取权限的文件。 在“文件预览”下，选择“访问权限”。

    ![添加 Data Lake Store 访问权限](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-1.png "添加 Data Lake Store 访问权限")

7. 在“访问权限”下，选择“添加”。 在“分配权限”下，单击“选择用户或组”并搜索你之前创建的 Azure Active Directory 服务主体。

    ![添加 Data Lake Store 访问权限](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "添加 Data Lake Store 访问权限")

    选择要分配的 AAD 服务主体并单击“选择”。

8. 在“分配权限”下，单击“选择权限” > “读取”。 依次在“选择权限”下和“分配权限”下选择“确定”。

    ![添加 Data Lake Store 访问权限](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-2.png "添加 Data Lake Store 访问权限")

    现在，服务主体具有足够的权限来从 Azure Data Lake Store 读取示例数据文件。

## <a name="extract-data-from-data-lake-store"></a>从 Data Lake Store 提取数据

在此部分，请先在 Azure Databricks 工作区中创建一个 Notebook，然后运行代码片段，以便将数据从 Data Lake Store 提取到 Azure Databricks 中。

1. 在 [Azure 门户](https://portal.azure.com)中，转到所创建的 Azure Databricks 工作区，然后选择“启动工作区”。

2. 在左窗格中选择“工作区”。 在**工作区**下拉列表中，选择**创建** > **笔记本**。

    ![在 Databricks 中创建笔记本](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "在 Databricks 中创建笔记本")

2. 在“创建 Notebook”对话框中，输入 Notebook 的名称。 选择“Scala”作为语言，然后选择前面创建的 Spark 群集。

    ![在 Databricks 中创建笔记本](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "在 Databricks 中创建笔记本")

    选择**创建**。

3. 将以下代码片段添加到空的代码单元中，将占位符值替换为此前为 Azure Active Directory 服务主体保存的值。

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    按 **SHIFT + ENTER** 运行代码单元。

4. 现在可以将示例 json 文件加载到 Data Lake Store 中，充当 Azure Databricks 中的数据帧。 将以下代码片段粘贴到新的代码单元中，替换占位符值，然后按 **SHIFT + ENTER**。

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. 运行以下代码片段，查看数据帧的内容。

        df.show()

    会显示类似于以下代码片段的输出：

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

现在已将数据从 Azure Data Lake Store 提取到 Azure Databricks 中。

## <a name="transform-data-in-azure-databricks"></a>在 Azure Databricks 中转换数据

原始示例数据 **small_radio_json.json** 捕获某个电台的听众，有多个不同的列。 在此部分，请对该数据进行转换，仅检索数据集中的特定列。 

1. 一开始仅从已创建的数据帧检索 *firstName*、*lastName*、*gender*、*location* 和 *level* 列。

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
        specificColumnsDf.show()

    所获得的输出如以下代码片段所示：

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

2.  可以进一步转换该数据，将 **level** 列重命名为 **subscription_type**。

        val renamedColumnsDf = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDf.show()

    所获得的输出如以下代码片段所示。

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

## <a name="load-data-into-azure-sql-data-warehouse"></a>将数据加载到 Azure SQL 数据仓库

在此部分，请将转换的数据上传到 Azure SQL 数据仓库中。 可以使用适用于 Azure Databricks 的 Azure SQL 数据仓库连接器直接上传数据帧，在 SQL 数据仓库中作为表来存储。

如前所述，SQL 数据仓库连接器使用 Azure Blob 存储作为临时存储位置，以便将数据从 Azure Databricks 上传到 Azure SQL 数据仓库。 因此，一开始请提供连接到存储帐户所需的配置。 必须已经按照本文先决条件部分的要求创建帐户。

1. 提供从 Azure Databricks 访问 Azure 存储帐户所需的配置。

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey =  "<ACCESS KEY>"

2. 指定一个在 Azure Databricks 和 Azure SQL 数据仓库之间移动数据时需要使用的临时文件夹。

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. 运行以下代码片段，以便在配置中存储 Azure Blob 存储访问密钥。 这样可确保不需将访问密钥以纯文本形式存储在 Notebook 中。

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. 提供连接到 Azure SQL 数据仓库实例所需的值。 必须已经按先决条件的要求创建 SQL 数据仓库。

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>" 
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort =  "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. 运行以下代码片段来加载转换的数据帧 **renamedColumnsDf**，在 SQL 数据仓库中作为表来存储。 此代码片段在 SQL 数据库中创建名为 **SampleTable** 的表。 请注意，Azure SQL 数据仓库需要一个主密钥。  可以通过在 SQL Server Management Studio 中执行“CREATE MASTER KEY”命令来创建主密钥。

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
        
        renamedColumnsDf.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall) 
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. 连接到 SQL 数据库，验证是否看到 **SampleTable**。

    ![验证示例表](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "验证示例表")

7. 运行一个 select 查询，验证表的内容。 该表的数据应该与 **renamedColumnsDf** 数据帧相同。

    ![验证示例表内容](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "验证示例表内容")

## <a name="clean-up-resources"></a>清理资源

运行完本教程后，可以终止群集。 为此，请在 Azure Databricks 工作区的左窗格中选择“群集”。 针对想要终止的群集，将光标移到“操作”列下面的省略号上，选择“终止”图标。

![停止 Databricks 群集](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "停止 Databricks 群集")

如果不手动终止群集，但在创建群集时选中了“在不活动 __ 分钟后终止”复选框，则该群集会自动停止。 在这种情况下，群集在处于非活动状态一定时间后会自动停止。

## <a name="next-steps"></a>后续步骤 
本教程介绍了如何：

> [!div class="checklist"]
> * 创建 Azure Databricks 工作区
> * 在 Azure Databricks 中创建 Spark 群集
> * 创建 Azure Data Lake Store 帐户
> * 将数据上传到 Azure Data Lake Store
> * 在 Azure Databricks 中创建 Notebook
> * 从 Data Lake Store 提取数据
> * 在 Azure Databricks 中转换数据
> * 将数据加载到 Azure SQL 数据仓库

请继续学习下一教程，了解如何使用 Azure 事件中心将实时数据流式传输到 Azure Databricks 中。

> [!div class="nextstepaction"]
>[使用事件中心将数据流式传输到 Azure Databricks](databricks-stream-from-eventhubs.md)
