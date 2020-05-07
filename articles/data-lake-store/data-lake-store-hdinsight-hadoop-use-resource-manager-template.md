---
title: 模板-具有 Data Lake Storage Gen1 的 HDInsight 群集
description: 使用 Azure 资源管理器模板创建和使用 Azure Data Lake Storage Gen1 的 Azure HDInsight 群集。
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 486809201db45e0f5bbeed870e24b1f63770e319
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692029"
---
# <a name="create-an-hdinsight-cluster-with-azure-data-lake-storage-gen1-using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板创建包含 Azure Data Lake Storage Gen1 的 HDInsight 群集
> [!div class="op_single_selector"]
> * [使用门户](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [使用 PowerShell（对于默认存储）](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [使用 PowerShell（对于其他存储）](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [使用 Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

了解如何使用 Azure PowerShell 来配置包含 Azure Data Lake Storage Gen1（用作额外存储）的 HDInsight 群集****。

对于支持的群集类型，可以将 Data Lake Storage Gen1 用作默认存储或其他存储帐户。 在 Data Lake Storage Gen1 用作额外存储时，该群集的默认存储帐户仍将是 Azure 存储 Blob (WASB)，与群集相关的文件（例如日志等）仍会写入到默认存储，而要处理的数据可以存储在 Data Lake Storage Gen1 帐户中。 使用 Data Lake Storage Gen1 作为额外存储帐户不会影响读/写到此群集的存储的性能或能力。

## <a name="using-data-lake-storage-gen1-for-hdinsight-cluster-storage"></a>将 Data Lake Storage Gen1 用于 HDInsight 群集存储

下面是结合使用 HDInsight 和 Data Lake Storage Gen1 的一些重要注意事项：

* HDInsight 版本 3.5 和 3.6 提供创建 HDInsight 群集（可访问作为默认存储的 Data Lake Storage Gen1）的选项。

* HDInsight 版本 3.2、3.4、3.5 和 3.6 提供创建 HDInsight 群集（可访问作为额外存储的 Data Lake Storage Gen1）的选项。

本文中，我们使用 Data Lake Storage Gen1 作为额外存储来预配 Hadoop 集群。 有关如何使用 Data Lake Storage Gen1 作为默认存储创建 Hadoop 群集的说明，请参阅[使用 Azure 门户创建具有 Data Lake Storage Gen1 的 HDInsight 群集](data-lake-store-hdinsight-hadoop-use-portal.md)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在开始阅读本教程前，必须具有：

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更高版本**。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。
* **Azure Active Directory 服务主体**。 本教程中的步骤用于指导如何在 Azure AD 中创建服务主体。 但是，只有 Azure AD 管理员才能创建服务主体。 Azure AD 管理员可以跳过此先决条件，继续阅读本教程。

    **如果不是 Azure AD 管理员**，将无法执行创建服务主体所需的步骤。 在这种情况下，Azure AD 管理员必须先创建服务主体，然后才能创建包含 Data Lake Storage Gen1 的 HDInsight 群集。 此外，必须使用证书创建服务主体，如[使用证书创建服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-certificate-from-certificate-authority)中所述。

## <a name="create-an-hdinsight-cluster-with-data-lake-storage-gen1"></a>创建包含 Data Lake Storage Gen1 的 HDInsight 群集
资源管理器模板以及使用模板的先决条件可在 GitHub 上[使用新的 Data Lake Storage Gen1 部署 HDInsight Linux 集群](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)中获得。 按照此链接提供的说明创建一个 HDInsight 群集，其中包含 Data Lake Storage Gen1 作为额外存储。

上面提及的链接中的说明需要 PowerShell。 按这些步骤开始前，请确保登录到 Azure 帐户。 在桌面上，打开一个新的 Azure PowerShell 窗口，并输入下面的代码段。 当系统提示登录时，请确保以订阅管理员/所有者身份登录：

```
# Log in to your Azure account
Connect-AzAccount

# List all the subscriptions associated to your account
Get-AzSubscription

# Select a subscription
Set-AzContext -SubscriptionId <subscription ID>
```

模板将部署以下资源类型：

* [Microsoft.DataLakeStore/accounts](/azure/templates/microsoft.datalakestore/accounts)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/clusters)

## <a name="upload-sample-data-to-data-lake-storage-gen1"></a>向 Data Lake Storage Gen1 上传示例数据
该资源管理器模板模板会创建一个新的 Data Lake Storage Gen1 帐户，并将其与此 HDInsight 群集关联。 现在必须要将示例数据上传到 Data Lake Storage Gen1。 此教程中之后需要这些数据从访问 Data Lake Storage Gen1 帐户中数据的 HDInsight 群集运行作业。 有关如何上传数据的说明，请参阅[上传文件到 Data Lake Storage Gen1 帐户](data-lake-store-get-started-portal.md#uploaddata)。 如果正在查找一些示例数据进行上传，可以从 **Azure Data Lake Git 存储库** 获取 [Ambulance Data](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)文件夹。

## <a name="set-relevant-acls-on-the-sample-data"></a>对示例数据设置相关 ACL
若要确保上传的示例数据可从 HDInsight 群集访问，必须确保用于在 HDInsight 群集和 Data Lake Store Gen1 之间建立标识的 Azure AD 应用程序可以访问你尝试访问的文件/文件夹。 为此，请执行以下步骤。

1. 找到与 HDInsight 群集和 Data Lake Store Gen1 帐户关联的 Azure AD 应用程序的名称。 查找该名称的一个方式是打开使用 Resource Manager 模板创建的 HDInsight 群集的边栏选项卡，单击“群集 AAD 标识”**** 选项卡，并查找“服务主体显示名称”**** 的值。
2. 现在，在要从该 HDInsight 群集访问的文件/文件夹上向此 Azure AD 应用程序提供访问权限。 若要对 Data Lake Storage Gen1 中的文件/文件夹设置正确的 ACL，请参阅[保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md#filepermissions)。

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-data-lake-storage-gen1"></a>在 HDInsight 群集上运行测试作业以使用 Data Lake Storage Gen1
配置 HDInsight 群集后，可在该群集上运行测试作业来测试该 HDInsight 群集是否可访问 Data Lake Storage Gen1。 为此，我们将运行示例 Hive 作业，该作业使用先前已上传至 Data Lake Storage Gen1 帐户的示例数据创建一个表。

在本部分中，将通过 SSH 连接到 HDInsight Linux 群集并运行示例 Hive 查询。 如果正在使用 Windows 客户端，建议使用 PuTTY，它可从 [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 下载****。

有关使用 PuTTY 的详细信息，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。

1. 连接后，请使用以下命令启动 Hive 命令行界面 (CLI)。

   ```
   hive
   ```
2. 使用该 CLI 输入以下语句，通过使用 Data Lake Storage Gen1 中的示例数据创建一个名为 vehicles 的新表****：

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestoragegen1>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   会得到类似于下面的输出：

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```


## <a name="access-data-lake-storage-gen1-using-hdfs-commands"></a>使用 HDFS 命令访问 Data Lake Storage Gen1
将 HDInsight 群集配置为使用 Data Lake Storage Gen1 后，可使用 HDFS shell 命令访问此存储。

在本部分中，将通过 SSH 连接到 HDInsight Linux 群集，并运行 HDFS 命令。 如果正在使用 Windows 客户端，建议使用 PuTTY，它可从 [https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 下载****。

有关使用 PuTTY 的详细信息，请参阅[在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。

连接后，使用以下 HDFS 文件系统命令列出 Data Lake Storage Gen1 帐户中的文件。

```
hdfs dfs -ls adl://<Data Lake Storage Gen1 account name>.azuredatalakestore.net:443/
```

这会列出先前上传到 Data Lake Storage Gen1 中的文件。

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestoragegen1.azuredatalakestore.net:443/mynewfolder
```

可以使用 `hdfs dfs -put` 命令将一些文件上传到 Data Lake Storage Gen1，然后使用 `hdfs dfs -ls` 验证是否已成功上传这些文件。


## <a name="next-steps"></a>后续步骤
* [将数据从 Azure 存储 Blob 复制到 Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [将 Data Lake Storage Gen1 与 Azure HDInsight 群集配合使用](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
