---
title: 使用共享访问签名限制访问 - Azure HDInsight
description: 了解如何使用共享访问签名限制对 Azure 存储 Blob 中存储的数据进行 HDInsight 访问。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: hrasheed
ms.openlocfilehash: 031498119eb4f9feb92046d7d7a86cfd77f8f368
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498122"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>使用 Azure 存储共享访问签名来限制访问 HDInsight 中的数据

HDInsight 对群集关联的 Azure 存储帐户中的数据拥有完全访问权限。 可以在 Blob 容器中使用共享访问签名来限制对数据的访问。 共享访问签名 (SAS) 是可用于限制数据访问权限的一项 Azure 存储帐户功能。 例如，它可以提供对数据的只读访问。

> [!IMPORTANT]  
> 对于使用 Apache Ranger 的解决方案，请考虑使用已加入域的 HDInsight。 有关详细信息，请参阅[配置已加入域的 HDInsight](./domain-joined/apache-domain-joined-configure.md) 文档。

> [!WARNING]  
> HDInsight 必须对群集的默认存储拥有完全访问权限。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](./hdinsight-hadoop-linux-use-ssh-unix.md)。

* 一个现有的[存储容器](../storage/blobs/storage-quickstart-blobs-portal.md)。  

* 如果使用 PowerShell，你将需要 [Az 模块](https://docs.microsoft.com/powershell/azure/overview)。

* 若要使用 Azure CLI 但尚未安装它，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

* 如果使用 [Python](https://www.python.org/downloads/)，请安装 2.7 或更高版本。

* 如果使用 C#，Visual Studio 的版本必须是 2013 或更高。

* 存储帐户的 [URI 方案](./hdinsight-hadoop-linux-information.md#URI-and-scheme)。 对于 Azure 存储，这将是 `wasb://`，对于 Azure Data Lake Storage Gen2，这将是 `abfs://`，对于 Azure Data Lake Storage Gen1，这将是 `adl://`。 如果为 Azure 存储启用安全传输，则将 `wasbs://`URI。 另请参阅[安全传输](../storage/common/storage-require-secure-transfer.md)。

* 共享访问签名要添加到的现有 HDInsight 群集。 如果没有，则可以使用 Azure PowerShell 创建群集，并在创建群集期间添加共享访问签名。

* 来自 [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) 的示例文件。 此存储库包含以下项：

  * Visual Studio 项目，可以创建存储容器、存储策略，以及配合 HDInsight 使用的 SAS
  * Python 脚本，可以创建存储容器、存储策略，以及配合 HDInsight 使用的 SAS
  * PowerShell 脚本，可以创建 HDInsight 群集并将其配置为使用 SAS。 下面进一步使用更新的版本。
  * 示例文件：`hdinsight-dotnet-python-azure-storage-shared-access-signature-master\sampledata\sample.log`

## <a name="shared-access-signatures"></a>共享访问签名

共享访问签名有两种形式：

* 即席：针对该 SAS 的开始时间、到期时间和权限全都在 SAS URI 上指定。

* 存储访问策略：存储访问策略在资源容器（例如 Blob 容器）中定义。 可以使用策略来管理一个或多个共享访问签名的约束。 在将某一 SAS 与一个存储访问策略相关联时，该 SAS 将继承对该存储访问策略定义的约束：开始时间、到期时间和权限。

这两种形式之间的差异对于一个关键情形而言十分重要：吊销。 一个 SAS 就是一个 URL，因此，获取该 SAS 的任何人都可以使用它，而与是谁请求它以便开始的无关。 如果某一 SAS 是公开发布的，则世界上的任何人都可以使用它。 在发生以下四种情况之一前分发的 SAS 是有效的：

1. 达到了对该 SAS 指定的到期时间。

2. 达到了对该 SAS 引用的存储访问策略指定的到期时间。 以下方案导致达到了到期时间：

    * 时间间隔已过。
    * 将存储访问策略修改为具有过去的到期时间。 更改到期时间是撤销 SAS 的一种方法。

3. 删除了该 SAS 引用的存储访问策略，这是用于吊销 SAS 的另一种方法。 如果重新创建同名的存储访问策略，以前策略的所有 SAS 令牌都将有效（如果 SAS 的到期时间尚未过）。 如果想要撤销 SAS，请确保使用不同名称（如果使用将来的到期时间重新创建该访问策略）。

4. 将重新生成用于创建 SAS 的帐户密钥。 重新生成密钥会导致使用以前密钥的所有应用程序身份验证失败。 将所有组件更新为使用新密钥。

> [!IMPORTANT]  
> 共享访问签名 URI 与用于创建签名的帐户密钥和关联的存储访问策略（如果有）相关联。 如果未指定存储访问策略，则吊销共享访问签名的唯一方法是更改帐户密钥。

建议始终使用存储访问策略。 使用存储策略时，可以根据需要撤销签名或延长过期日期。 本文档中的步骤使用存储访问策略生成 SAS。

有关共享访问签名的详细信息，请参阅[了解 SAS 模型](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

## <a name="create-a-stored-policy-and-sas"></a>创建存储策略和 SAS

保存每个方法结束时生成的 SAS 令牌。 令牌如下所示：

```output
?sv=2018-03-28&sr=c&si=myPolicyPS&sig=NAxefF%2BrR2ubjZtyUtuAvLQgt%2FJIN5aHJMj6OsDwyy4%3D
```

### <a name="using-powershell"></a>使用 PowerShell

请将 `RESOURCEGROUP`、`STORAGEACCOUNT` 和 `STORAGECONTAINER` 替换为现有存储容器的相应值。 将目录更改为 `hdinsight-dotnet-python-azure-storage-shared-access-signature-master`，或修改 `-File` 参数以包含 `Set-AzStorageblobcontent` 的绝对路径。 输入以下 PowerShell 命令：

```PowerShell
$resourceGroupName = "RESOURCEGROUP"
$storageAccountName = "STORAGEACCOUNT"
$containerName = "STORAGECONTAINER"
$policy = "myPolicyPS"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get the access key for the Azure Storage account
$storageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$storageContext = New-AzStorageContext `
                                -StorageAccountName $storageAccountName `
                                -StorageAccountKey $storageAccountKey

# Create a stored access policy for the Azure storage container
New-AzStorageContainerStoredAccessPolicy `
   -Container $containerName `
   -Policy $policy `
   -Permission "rl" `
   -ExpiryTime "12/31/2025 08:00:00" `
   -Context $storageContext

# Get the stored access policy or policies for the Azure storage container
Get-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Context $storageContext

# Generates an SAS token for the Azure storage container
New-AzStorageContainerSASToken `
    -Name $containerName `
    -Policy $policy `
    -Context $storageContext

<# Removes a stored access policy from the Azure storage container
Remove-AzStorageContainerStoredAccessPolicy `
    -Container $containerName `
    -Policy $policy `
    -Context $storageContext
#>

# upload a file for a later example
Set-AzStorageblobcontent `
    -File "./sampledata/sample.log" `
    -Container $containerName `
    -Blob "samplePS.log" `
    -Context $storageContext
```

### <a name="using-azure-cli"></a>使用 Azure CLI

本部分使用的变量基于 Windows 环境。 对于 bash 或其他环境，需要做出轻微的改动。

1. 请将 `STORAGEACCOUNT` 和 `STORAGECONTAINER` 替换为现有存储容器的相应值。

    ```azurecli
    # set variables
    set AZURE_STORAGE_ACCOUNT=STORAGEACCOUNT
    set AZURE_STORAGE_CONTAINER=STORAGECONTAINER

    #Login
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription SUBSCRIPTION

    # Retrieve the primary key for the storage account
    az storage account keys list --account-name %AZURE_STORAGE_ACCOUNT% --query "[0].{PrimaryKey:value}" --output table
    ```

2. 将检索到的主键设置为某个变量，供稍后使用。 请将 `PRIMARYKEY` 替换为在前一步骤中检索到的值，然后输入以下命令：

    ```azurecli
    #set variable for primary key
    set AZURE_STORAGE_KEY=PRIMARYKEY
    ```

3. 将目录更改为 `hdinsight-dotnet-python-azure-storage-shared-access-signature-master`，或修改 `--file` 参数以包含 `az storage blob upload` 的绝对路径。 执行剩余的命令：

    ```azurecli
    # Create stored access policy on the containing object
    az storage container policy create --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --expiry 2025-12-31 --permissions rl

    # List stored access policies on a containing object
    az storage container policy list --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Generate a shared access signature for the container
    az storage container generate-sas --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # Reversal
    # az storage container policy delete --container-name %AZURE_STORAGE_CONTAINER% --name myPolicyCLI --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT%

    # upload a file for a later example
    az storage blob upload --container-name %AZURE_STORAGE_CONTAINER% --account-key %AZURE_STORAGE_KEY% --account-name %AZURE_STORAGE_ACCOUNT% --name sampleCLI.log --file "./sampledata/sample.log"
    ```

### <a name="using-python"></a>使用 Python

打开 `SASToken.py` 文件，将 `storage_account_name`、`storage_account_key` 和 `storage_container_name` 替换为现有存储容器的相应值，然后运行该脚本。

如果收到错误消息 `pip install --upgrade azure-storage`，可能需要执行 `ImportError: No module named azure.storage`。

### <a name="using-c"></a>使用 C#

1. 在 Visual Studio 中打开解决方案。

2. 在解决方案资源管理器中，右键单击“SASExample”项目并选择“属性”。

3. 选择“设置”，并添加以下条目的值：

   * StorageConnectionString：想要为其创建存储策略和 SAS 的存储帐户的连接字符串。 格式应为 `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`，其中 `myaccount` 是存储帐户名称，`mykey` 是存储帐户密钥。

   * ContainerName：想要限制访问的存储帐户中的容器。

   * SASPolicyName：要创建的存储策略所用的名称。

   * FileToUpload：上传到容器的文件的路径。

4. 运行该项目。 保存 SAS 策略令牌、存储帐户名称和容器名称。 将存储帐户与 HDInsight 群集关联时，将使用这些值。

## <a name="use-the-sas-with-hdinsight"></a>将 SAS 与 HDInsight 配合使用

创建 HDInsight 群集时，必须指定主存储帐户，可以选择性地指定其他存储帐户。 这两种添加存储的方法都需要对所用存储帐户和容器拥有完全访问权限。

要使用共享访问签名来限制对容器的访问，请将一个自定义条目添加到群集的 **core-site** 配置中。 可以在创建群集期间使用 PowerShell 添加该条目，或者在创建群集之后使用 Ambari 添加该条目。

### <a name="create-a-cluster-that-uses-the-sas"></a>创建使用 SAS 的群集

将 `CLUSTERNAME`、`RESOURCEGROUP`、`DEFAULTSTORAGEACCOUNT`、`STORAGECONTAINER`、`STORAGEACCOUNT` 和 `TOKEN` 替换为相应的值。 输入 PowerShell 命令：

```powershell

$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RESOURCEGROUP'

# Replace with the Azure data center you want to the cluster to live in
$location = 'eastus'

# Replace with the name of the default storage account TO BE CREATED
$defaultStorageAccountName = 'DEFAULTSTORAGEACCOUNT'

# Replace with the name of the SAS container CREATED EARLIER
$SASContainerName = 'STORAGECONTAINER'

# Replace with the name of the SAS storage account CREATED EARLIER
$SASStorageAccountName = 'STORAGEACCOUNT'

# Replace with the SAS token generated earlier
$SASToken = 'TOKEN'

# Default cluster size (# of worker nodes), version, and type
$clusterSizeInNodes = "4"
$clusterVersion = "3.6"
$clusterType = "Hadoop"

# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Create an Azure Storage account and container
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey


# Create a blob container. This holds the default data store for the cluster.
New-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext 

# Cluster login is used to secure HTTPS services hosted on the cluster
$httpCredential = Get-Credential `
    -Message "Enter Cluster login credentials" `
    -UserName "admin"

# SSH user is used to remotely connect to the cluster using SSH clients
$sshCredential = Get-Credential `
    -Message "Enter SSH user credentials" `
    -UserName "sshuser"

# Create the configuration for the cluster
$config = New-AzHDInsightClusterConfig 

$config = $config | Add-AzHDInsightConfigValues `
    -Spark2Defaults @{} `
    -Core @{"fs.azure.sas.$SASContainerName.$SASStorageAccountName.blob.core.windows.net"=$SASToken}

# Create the HDInsight cluster
New-AzHDInsightCluster `
    -Config $config `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType $clusterType `
    -OSType Linux `
    -Version $clusterVersion `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $clusterName

<# REVERSAL
Remove-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $clusterName

Remove-AzStorageContainer `
    -Name $clusterName `
    -Context $defaultStorageContext

Remove-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName

Remove-AzResourceGroup `
    -Name $resourceGroupName
#>
```

> [!IMPORTANT]  
> 出现输入 HTTP/s 或 SSH 用户名和密码的提示时，必须提供符合以下条件的密码：
>
> * 长度必须至少为 10 个字符。
> * 必须至少包含一个数字。
> * 必须至少包含一个非字母数字字符。
> * 必须至少包含一个大写或小写字母。

需要等待一段时间让此脚本完成，通常大约是 15 分钟。 如果脚本完成且没有发生任何错误，则群集创建完毕。

### <a name="use-the-sas-with-an-existing-cluster"></a>对现有群集使用 SAS

如果已有一个群集，可使用以下步骤将 SAS 添加到 **core-site** 配置：

1. 打开群集的 Ambari Web UI。 此页的地址是 `https://YOURCLUSTERNAME.azurehdinsight.net`。 出现提示时，请使用在创建群集时使用的管理员名称 (admin) 和密码对群集进行身份验证。

2. 从 Ambari Web UI 的左侧，选择“HDFS”，并在页面的中间选择“配置”选项卡。

3. 选择“高级”选项卡，并向下滚动，找到“自定义 core-site”部分。

4. 展开“自定义 core-site”部分，并滚动到底部，选择“添加属性...”链接。 在“密钥”和“值”字段中使用以下值：

   * **键**：`fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net`
   * **值**：之前执行的一种方法返回的 SAS。

     将 `CONTAINERNAME` 替换为用于 C# 或 SAS 应用程序的容器名称。 将 `STORAGEACCOUNTNAME` 替换为所用的存储帐户名称。

5. 单击“添加”按钮以保存此密钥和值，并单击“保存”按钮以保存配置更改。 出现提示时，请添加更改的说明（例如，“添加 SAS 存储访问”），并单击“保存”。

    更改完成后，单击“确定” 。

   > [!IMPORTANT]  
   > 必须重启几个服务才能使更改生效。

6. 在 Ambari Web UI 中，从左侧的列表中选择“HDFS”，并从右侧的“服务操作”下拉列表中选择“重启所有受影响项”。 出现提示时，选择“确认全部重启”。

    对 MapReduce2 和 YARN 重复此过程。

7. 重启这些服务后，请选择每个服务，并从“服务操作”下拉列表中禁用维护模式。

## <a name="test-restricted-access"></a>测试限制的访问

使用以下步骤验证是否只能读取和列出 SAS 存储帐户中的项。

1. 连接到群集。 将 `CLUSTERNAME` 替换为群集的名称，然后输入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 要列出容器的内容，请在提示符下使用以下命令：

    ```bash
    hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    将 `SASCONTAINER` 替换为针对 SAS 存储帐户创建的容器名称。 将 `SASACCOUNTNAME` 替换为用于 SAS 的存储帐户名称。

    该列表包含创建容器和 SAS 时上传的文件。

3. 使用以下命令验证是否可以读取文件的内容。 按上一步骤中所述替换 `SASCONTAINER` 和 `SASACCOUNTNAME`。 将 `sample.log` 替换为前一个命令中显示的名称：

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log
    ```

    此命令列出文件的内容。

4. 使用以下命令将文件下载到本地文件系统：

    ```bash
    hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/sample.log testfile.txt
    ```

    此命令会将该文件下载到名为 **testfile.txt**的本地文件中。

5. 使用以下命令将本地文件上传到 SAS 存储上名为 testupload.txt 的新文件中：

    ```bash
    hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    将收到类似于以下文本的消息：

        put: java.io.IOException

    发生此错误的原因是存储位置是只读+仅限列出的。 使用以下命令将数据放在群集的可写默认存储中：

    ```bash
    hdfs dfs -put testfile.txt wasbs:///testupload.txt
    ```

    这一次操作应会成功完成。

## <a name="next-steps"></a>后续步骤

现在你已了解如何将访问受限的存储添加到 HDInsight 群集，接下来请了解在群集上处理数据的其他方法：

* [将 Apache Hive 和 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Apache Pig 和 HDInsight 配合使用](hadoop/hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

