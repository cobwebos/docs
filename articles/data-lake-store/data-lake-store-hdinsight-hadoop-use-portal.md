---
title: "使用 Azure 门户创建包含 Data Lake Store 的 HDInsight 群集 | Microsoft 文档"
description: "在 Azure 门户中创建和使用包含 Azure Data Lake Store 的 HDInsight 群集"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: 2aa5542dc095beaf951bad2b0361a3acc4468f2d
ms.lasthandoff: 04/05/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>使用 Azure 门户创建包含 Data Lake Store 的 HDInsight 群集
> [!div class="op_single_selector"]
> * [使用 Azure 门户](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell（对于默认存储）](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell（对于附加存储）](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用 Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

本文介绍如何使用 Azure 门户创建有权访问 Azure Data Lake Store 的 HDInsight 群集。 对于支持的群集类型，可将 Data Lake Store 用作默认存储或附加的存储帐户。

将 Data Lake Store 用作附加存储时，群集的默认存储帐户仍是 Azure Blob 存储，与群集相关的文件（例如日志）仍会写入默认存储。 但是，要处理的数据可存储在 Data Lake Store 帐户中。 使用 Data Lake Store 作为附加存储帐户不会影响到在群集存储中读取或写入数据的性能或能力。

下面是结合使用 HDInsight 和 Data Lake Store 的一些重要注意事项：

* HDInsight 版本 3.5 提供创建 HDInsight 群集（可访问作为默认存储的 Data Lake Store）的选项。

* 创建 HDInsight 群集（可访问作为默认存储的 Data Lake Store）的选项*不可用于* HDInsight Premium 群集。

* HDInsight 版本 3.2、3.4 和 3.5 提供创建 HDInsight 群集（可访问作为其他存储的 Data Lake Store）的选项。

* 对于 HBase 群集（Windows 和 Linux），*不支持*将 Data Lake Store 默认或附加存储选项。

* 对于 Storm 群集（Windows 和 Linux），可以使用 Data Lake Store 从 Storm 拓扑写入数据。 对于可由 Storm 拓扑读取的引用数据，也可以使用 Data Lake Store。 有关详细信息，请参阅[在 Storm 拓扑中使用 Data Lake Store](#use-data-lake-store-in-a-storm-topology)。


## <a name="prerequisites"></a>先决条件
在开始学习本教程之前，请确保满足以下要求：

* **一个 Azure 订阅**。 转到[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Store 帐户**。 遵循[通过 Azure 门户开始使用 Azure Data Lake Store](data-lake-store-get-started-portal.md) 中的说明。

* **一个 Azure Active Directory 服务主体**。 本教程提供了有关如何在 Azure Active Directory (Azure AD) 中创建服务主体的说明。 但是，只有 Azure AD 管理员才能创建服务主体。 管理员可以跳过此先决条件部分，继续阅读本教程。

    >[!NOTE]
    >仅当你是 Azure AD 管理员时，才能创建服务主体。 Azure AD 管理员必须先创建服务主体，然后才能创建包含 Data Lake Store 的 HDInsight 群集。 此外，必须根据[使用证书创建服务主体](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority)中所述，使用证书创建服务主体。
    >

## <a name="create-an-hdinsight-cluster-with-access-to-a-data-lake-store"></a>创建有权访问 Data Lake Store 的 HDInsight 群集
在本部分，将创建使用 Data Lake Store 作为默认存储或附加存储的 HDInsight Hadoop 群集。

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>创建具有作为默认存储的 Data Lake Store 的群集

>[!NOTE]
>可将此选项仅用于 HDInsight 3.5 群集（标准版）。 在 HDInsight 3.5 群集中，此选项不适用于 HBase 群集类型。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 若要开始预配 HDInsight 群集，请遵循[在 HDInsight 中创建 Hadoop群集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)中的说明。

3. 在“存储”边栏选项卡的“主存储类型”下，单击“Data Lake Store”。

4. 选择现有 Data Lake Store 帐户，然后输入群集特定的文件要存储到的根文件夹路径。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "将服务主体添加到 HDInsight 群集")


    在以上屏幕截图中，根文件夹路径为 /clusters/myhdiadlcluster，其中 *myhdiadlcluster* 是要创建的群集的名称。 在这种情况下，请确保 */clusters* 文件夹在 Data Lake Store 帐户中存在。 将在创建群集期间创建 *myhdiadlcluster* 文件夹。 同样，如果根路径设置为 */hdinsight/clusters/data/myhdiadlcluster*，请确保 */hdinsight/clusters/data/* 在 Data Lake Store 帐户中存在。

5. 单击“Data Lake Store 访问”，配置 Data Lake Store 帐户和 HDInsight 群集之间的访问。 相关说明，请参阅[配置 HDInsight 群集和 Data Lake Store 之间的访问](#configure-access-between-hdinsight-cluster-and-data-lake-store)。


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>创建具有作为附加存储的 Data Lake Store 的群集

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 若要开始预配 HDInsight 群集，请遵循[在 HDInsight 中创建 Hadoop群集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)中的说明。

3. 在“存储”边栏选项卡的“主存储类型”下，选择“Azure 存储”。

4. 在“选择方法”下，执行以下任一操作：

    * 若要指定属于 Azure 订阅的存储帐户，请选择“我的订阅”，然后选择存储帐户。

    * 若要指定不属于 Azure 订阅的存储帐户，请选择“访问密钥”，然后提供该外部存储帐户的信息。

5. 在“默认容器”下，保留门户建议的默认容器名称或自行指定名称。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "将服务主体添加到 HDInsight 群集")

6. 将 Blob 存储用作默认存储时，仍可将 Data Lake Store 用作群集的附加存储。 为此，请单击“Data Lake Store 访问”以配置 Data Lake Store 帐户和 HDInsight 群集之间的访问。 相关说明，请参阅[配置 HDInsight 群集和 Data Lake Store 之间的访问](#configure-access-between-hdinsight-cluster-and-data-lake-store)。

## <a name="configure-access-between-hdinsight-cluster-and-data-lake-store"></a>配置 HDInsight 群集和 Data Lake Store 之间的访问

在本部分中，将使用 Azure Active Directory 服务主体配置 HDInsight 群集和 Data Lake Store 之间的访问

1. 在“Data Lake Store 访问”边栏选项卡上，指定要使用新服务主体还是现有服务主体。 此步骤将创建新的服务主体。 若要使用现有服务主体，请跳到下一步。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“Data Lake Store 访问”边栏选项卡中单击“新建”，然后单击“服务主体”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“创建服务主体”边栏选项卡中输入所需的值。  

    c. 单击“创建” 。 系统会自动创建证书和 Azure AD 应用程序。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "将服务主体添加到 HDInsight 群集")

    还可单击“下载证书”下载与已创建的服务主体关联的证书。 如要想要在创建其他 HDInsight 群集时使用相同的服务主体，下载证书的做法将非常有用。 单击“选择”。

2. 若要使用现有服务主体，请执行下面提供的步骤。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“Data Lake Store 访问”边栏选项卡中单击“使用现有”，然后单击“服务主体”。

    b. 在“选择服务主体”边栏选项卡中搜索现有的服务主体。 单击要使用的服务主体，然后单击“选择”。

    c. 在“Data Lake Store 访问”边栏选项卡中，上载与所选服务主体关联的证书（.pfx 文件），然后输入证书密码。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "将服务主体添加到 HDInsight 群集")

7. 在“Data Lake Store 访问”边栏选项卡中，单击“访问”。 默认情况下会打开“选择文件权限”边栏选项卡。 其中列出了你订阅中的所有 Data Lake Store 帐户。

8. 选择要与群集关联的 Data Lake Store 帐户。

    **如果使用 Data Lake Store 作为默认存储**，需分配两个级别的权限。

    a. 首先，应分配 Data Lake Store 帐户的根级别的权限。 为此，请将鼠标悬停（不要单击）在 Data Lake Store 帐户的名称上，使复选框可见。 选择此复选框。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "将服务主体添加到 HDInsight 群集")

    b. 然后，应将权限分配给 HDInsight 群集存储根。 根据之前的屏幕截图，群集存储根是选择 Data Lake Store 作为默认存储时指定的 **/clusters** 文件夹。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.add.png "将服务主体添加到 HDInsight 群集")

    **如果使用 Data Lake Store 作为附加存储**，则必须仅为要从 HDInsight 群集访问的文件夹分配权限。 例如，在以下屏幕截图中，仅提供对 Data Lake Store 帐户中 **hdiaddonstorage** 文件夹的访问权限。

    ![将服务主体权限分配给 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "将服务主体权限分配给 HDInsight 群集")

9. 对于选定的所有帐户和路径，请选择权限（读取、写入或执行），指定是否同时将权限以递归方式应用于子项目，然后单击“选择”。

11. 在“分配选定的权限”窗口中，若要为 Azure Active Directory 服务主体分配对所选帐户、文件或文件夹的权限，请单击“运行”。

    ![将服务主体权限分配给 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "将服务主体权限分配给 HDInsight 群集")

12. 成功分配权限后，在打开的每个边栏选项卡中单击“完成”，返回“Data Lake Store 访问”边栏选项卡。

13. 在“Data Lake Store 访问”中单击“选择”，然后根据[在 HDInsight 中创建 Hadoop 群集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)中所述继续创建群集。

## <a name="verify-cluster-set-up"></a>验证群集设置

群集设置完成后，请在群集边栏选项卡中通过执行以下一项或两项操作来验证结果：

* 若要验证群集的关联存储是否为指定的 Data Lake Store 帐户，请在左窗格中单击“存储帐户”。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "将服务主体添加到 HDInsight 群集")

* 若要验证服务主体是否已正确关联到 HDInsight 群集，请在左窗格中单击“Data Lake Store 访问”。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "将服务主体添加到 HDInsight 群集")


## <a name="examples"></a>示例

将包含 Data Lake Store 的群集设置为存储后，请参考以下示例，了解如何使用 HDInsight 群集来分析 Data Lake Store 中存储的数据。

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>针对 Data Lake Store（用作主存储）中的数据运行 Hive 查询

若要运行 Hive 查询，请在 Ambari 门户中使用 Hive 视图界面。 有关 Ambari Hive 视图用法的说明，请参阅[将 Hive 视图与 HDInsight 中的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md)。

处理 Data Lake Store 中的数据时，需要更改几个字符串。

例如，如果将创建的、包含 Data Lake Store 的群集用作主存储，则数据的路径是：*adl://<Data Lake Store 帐户名>/azuredatalakestore.net/path/to/file*。 基于 Data Lake Store 帐户中存储的示例数据创建表的 Hive 查询如下所示：

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

说明：
* `adl://hdiadlstorage.azuredatalakestore.net/` 是 Data Lake Store 帐户的根。
* `/clusters/myhdiadlcluster` 是在创建群集时指定的群集数据的根。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` 是查询中所用示例文件的位置。

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>针对 Data Lake Store（用作附加存储）中的数据运行 Hive 查询

如果创建的群集使用 Blob 存储作为默认存储，示例数据将不会包含在用作附加存储的 Azure Data Lake Store 帐户中。 在这种情况下，请先将数据从 Blob 存储传输到 Data Lake Store，然后按前面的示例中所示运行查询。

有关如何将数据从 Blob 存储复制到 Data Lake Store 的信息，请参阅以下文章：

* [使用 Distcp 在 Azure 存储 Blob 与 Data Lake Store 之间复制数据](data-lake-store-copy-data-wasb-distcp.md)
* [使用 AdlCopy 将数据从 Azure 存储 Blob 复制到 Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>在 Spark 群集中使用 Data Lake Store
可以使用 Spark 群集对存储在 Data Lake Store 中的数据运行 Spark 作业。 有关详细信息，请参阅[使用 HDInsight Spark 群集分析 Data Lake Store 中的数据](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md)。


### <a name="use-data-lake-store-in-a-storm-topology"></a>在 Storm 拓扑中使用 Data Lake Store
可使用 Data Lake Store 从 Storm 拓扑写入数据。 有关如何实现此方案的说明，请参阅[将 Azure Data Lake 存储与 HDInsight 上的 Apache Storm 配合使用](../hdinsight/hdinsight-storm-write-data-lake-store.md)。

## <a name="see-also"></a>另请参阅
* [PowerShell：创建 HDInsight 群集以使用 Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

