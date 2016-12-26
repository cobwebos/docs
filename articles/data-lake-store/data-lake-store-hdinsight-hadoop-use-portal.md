---
title: "通过门户创建具有 Azure Data Lake Store 的 HDInsight 群集 | Microsoft Docs"
description: "使用 Azure 门户创建和使用包含 Azure Data Lake Store 的 HDInsight 群集"
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
ms.date: 11/18/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 3a9a4df2d260e8eebd8621b22efdb2927e9f5ecf
ms.openlocfilehash: a4fb47f9f517d66cf0ff9fde039d7bfd8edc29eb


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>使用 Azure 门户创建包含 Data Lake Store 的 HDInsight 群集
> [!div class="op_single_selector"]
> * [使用门户](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用资源管理器](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

了解如何通过 Azure 门户创建具有 Azure Data Lake Store 访问权限的 HDInsight 群集。 对于支持的群集类型，Data Lake Store 用作默认存储或其他存储帐户。 在 Data Lake Store 用作其他存储时，该群集的默认存储帐户仍将是 Azure 存储 Blob (WASB)，与群集相关的文件（例如日志等）仍会写入到默认存储，而要处理的数据可以存储在 Data Lake Store 帐户中。 使用 Data Lake Store 作为其他存储帐户不会影响读/写到此群集的存储的性能或能力。

一些重要注意事项：

* HDInsight 版本 3.5 提供创建 HDInsight 群集（可访问作为默认存储的 Data Lake Store）的选项。

* HDInsight 版本 3.2、3.4 和 3.5 提供创建 HDInsight 群集（可访问作为其他存储的 Data Lake Store）的选项。

* 对于 HBase 群集（Windows 和 Linux），Data Lake Store **不支持**用作存储选项（包括默认存储和其他存储）。

* 对于 Storm 群集（Windows 和 Linux），Data Lake Store 可用于从 Storm 拓扑写入数据。 Data Lake Store 还可用于存储之后会被 Storm 拓扑读取的引用数据。 有关详细信息，请参阅[在 Storm 拓扑中使用 Data Lake Store](#use-data-lake-store-in-a-storm-topology)。


## <a name="prerequisites"></a>先决条件
在开始阅读本教程前，你必须具有：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Store 帐户**。 遵循[使用 Azure 门户，实现 Azure Data Lake Store 入门](data-lake-store-get-started-portal.md) 中的说明。

* **Azure Active Directory 服务主体**。 本教程中的步骤用于指导如何在 Azure AD 中创建服务主体。 但是，只有 Azure AD 管理员才能创建服务主体。 Azure AD 管理员可以跳过此先决条件，继续阅读本教程。

    **如果不是 Azure AD 管理员**，将无法执行创建服务主体所需的步骤。 在这种情况下，Azure AD 管理员必须先创建服务主体，然后才能创建包含 Data Lake Store 的 HDInsight 群集。 此外，必须使用证书创建服务主体，如[使用证书创建服务主体](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)中所述。

## <a name="do-you-learn-faster-with-videos"></a>通过视频学得更快？
观看以下视频，了解如何设置具有 Data Lake Store 访问权限的 HDInsight 群集。

* [Create an HDInsight cluster with access to Data Lake Store](https://mix.office.com/watch/l93xri2yhtp2)（创建具有 Data Lake Store 访问权限的 HDInsight 群集）
* 设置群集后，[Access data in Data Lake Store using Hive and Pig scripts](https://mix.office.com/watch/1n9g5w0fiqv1q)（使用 Hive 和 Pig 脚本访问 Data Lake Store 中的数据）

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>创建具有 Azure Data Lake Store 访问权限的 HDInsight 群集
在此部分中，可创建使用 Data Lake Store 作为其他存储的 HDInsight Hadoop 群集。 此版本中，对于 Hadoop 群集，Data Lake Store 仅可用作此群集的其他存储。 默认存储仍是 Azure storage blobs (WASB)。 因此，首先来创建此群集所需的存储帐户和存储容器。

1. 登录到新的 [Azure 门户](https://portal.azure.com)。

2. 请按照[在 HDInsight 中创建 Hadoop群集](../hdinsight/hdinsight-provision-clusters.md)中的步骤开始设置 HDInsight 群集。

3. 在“数据源”边栏选项卡上，指定是否将 Azure 存储 (WASB) 或 Data Lake Store 作为默认存储。 如果将 Azure Data Lake Store 用作默认存储，请跳到下一步。 

    如果将 Azure 存储 Blob 用作默认存储，则对于“主要存储类型”，请单击“Azure 存储”。 指定存储帐户和存储容器的详细信息，将“位置”指定为“美国东部 2”，然后单击“群集 AAD 标识”。
    
    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Add service principal to HDInsight cluster")


4. 如果将 Azure Data Lake Store 用作默认存储，则对于“主要存储类型”，请单击“Data Lake Store”。 选择已存在的 Data Lake Store 帐户，提供根文件夹路径，将在此路径下存储特定群集的文件（请参阅以下说明），将“位置”指定为“美国东部 2”，然后单击“群集 AAD 标识”。 可将此选项仅用于 HDInsight 3.5 群集。 在 HDInsight 3.5 群集内，此选项不可用于 HBase 群集类型。

    在以下屏幕截图中，根文件夹路径是 /clusters/myhdiadlcluster，其中 **myhdiadlcluster** 是正在创建的群集的名称。 在这种情况下，请确保 Data Lake Store 帐户中已存在 **/clusters** 文件夹。 **myhdiadlcluster** 文件夹将在群集创建过程中创建。 同样，如果根路径设置为 /hdinsight/clusters/data/myhdiadlcluter，必须确保 Data Lake Store 帐户中已存在 **/hdinsight/clusters/data/**。
        
    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Add service principal to HDInsight cluster")

5. 在“群集 AAD 标识”边栏选项卡上，可选择现有服务主体或创建新服务主体。 如果要使用现有服务主体，请跳到下一步。

    如果要创建新的服务主体，请在“群集 AAD 标识”边栏选项卡中，依次单击“新建”、“服务主体”，然后在“创建服务主体”边栏选项卡上，提供值以创建新的服务主体。 作为此操作的一部分，会同时创建证书和 Azure Active Directory 应用程序。 单击“创建” 。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Add service principal to HDInsight cluster")

    还可单击“下载证书”下载与已创建的服务主体关联的证书。 如要在以后创建其他 HDInsight 群集时使用同一服务主体，这将非常有用。 单击“选择”。

6. 如果要使用现有服务主体，请在“群集 AAD 标识”边栏选项卡中，依次单击“使用现有的”、“服务主体”，然后在“选择服务主体”边栏选项卡中，搜索现有的服务主体。 单击一个服务主体名称，然后单击“选择”。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Add service principal to HDInsight cluster")

    在“群集 AAD 标识”边栏选项卡中，上传与选择的服务主体关联的证书 (.pfx)，然后提供证书密码。

6. 在“群集 AAD 标识”边栏选项卡中，单击“管理 ADLS 访问”。 在下一窗格中，已默认选中“选择文件权限”，并列出了订阅中的所有 Data Lake Store 帐户。 单击要与群集相关联的 Data Lake Store 帐户，以列出该帐户中的文件和文件夹。 然后可以分配文件或文件夹级别的权限。 如果要关联帐户根级别的权限，请选择帐户名称旁边的复选框。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Add service principal to HDInsight cluster")

    > [!NOTE]
    > 如果将 Data Lake Store 帐户用作群集的默认存储，则**必须**将该权限分配给 Data Lake Store 帐户中根级别的服务主体。

7. 如果要为帐户内的文件或文件夹分配权限，请选择 Data Lake Store 帐户以查看下一窗格中的文件/文件夹。 选择该文件/文件夹，选择要在其上分配的权限（读取/写入/执行），指定是否同时将该权限以递归方式应用于子项目，然后单击“选择”。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Add service principal to HDInsight cluster")

8. 在下一屏幕中，单击“运行”以分配所选帐户、文件、文件夹上 Azure Active Directory 服务主体的权限。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Add service principal to HDInsight cluster")

9. 成功分配权限后，单击所有边栏选项卡上的“完成”，直到返回“群集 AAD 标识”边栏选项卡。

4. 单击“群集 AAD 标识”边栏选项卡上的“选择”，然后按照[在 HDInsight 中创建 Hadoop 群集](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)中所述，继续创建群集。

10. 设置群集后，可验证服务主体是否与此 HDInsight 群集相关联。 为此，请从群集边栏选项卡单击“群集 AAD 标识”查看关联的服务主体。

    ![将服务主体添加到 HDInsight 群集](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Add service principal to HDInsight cluster")

## <a name="show-me-some-examples"></a>举例说明

将具有 Data Lake Store 的群集预配为存储后，可通过以下示例了解如何使用 HDInsight 群集来分析 Data Lake Store 中存储的数据。

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store"></a>对存储在 Data Lake Store 中的数据运行 Hive 查询

若要运行 Hive 查询，可以使用 Ambari 门户中可用的 Hive 视图界面。 有关 Ambari Hive 视图用法的说明，请参阅[将 Hive 视图与 HDInsight 中的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md)。 处理 Data Lake Store 中的数据时，需要更改以下内容。

* 如果采用之前创建的群集的示例，则数据路径将为 `adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file`。 从存储在 Data Lake Store 帐户中的示例数据创建表的 Hive 查询将如下所示：

        CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

在上述查询中，

* `adl://hdiadlstorage.azuredatalakestore.net` 是 Data Lake Store 帐户的根。
* `/clusters/myhdiadlcluster` 是在创建群集时指定的群集数据的根。
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` 是查询中所用示例文件的位置


### <a name="use-data-lake-store-with-spark-cluster"></a>在 Spark 群集中使用 Data Lake Store
Spark 群集可用于对存储在 Data Lake Store 中的数据运行 Spark 作业。 若要了解相关说明，请参阅[使用 HDInsight Spark 群集分析 Data Lake Store 中的数据](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md)。


### <a name="use-data-lake-store-in-a-storm-topology"></a>在 Storm 拓扑中使用 Data Lake Store
可使用 Data Lake Store 从 Storm 拓扑写入数据。 有关如何实现此方案的说明，请参阅[将 Azure Data Lake 存储与 HDInsight 上的 Apache Storm 配合使用](../hdinsight/hdinsight-storm-write-data-lake-store.md)。

## <a name="see-also"></a>另请参阅
* [PowerShell：创建 HDInsight 群集以使用 Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx



<!--HONumber=Nov16_HO4-->


