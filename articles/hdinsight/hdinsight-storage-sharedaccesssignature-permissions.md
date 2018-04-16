---
title: 使用共享访问签名限制访问 - Azure HDInsight | Microsoft Docs
description: 了解如何使用共享访问签名限制对 Azure 存储 Blob 中存储的数据进行 HDInsight 访问。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 7bcad2dd-edea-467c-9130-44cffc005ff3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/25/2018
ms.author: larryfr
ms.openlocfilehash: d3e37ba0f590cf0572b84a53bdd407af63a19d36
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>使用 Azure 存储共享访问签名来限制访问 HDInsight 中的数据

HDInsight 对群集关联的 Azure 存储帐户中的数据拥有完全访问权限。 可以在 Blob 容器中使用共享访问签名来限制对数据的访问。 例如，提供对数据的只读访问权限。 共享访问签名 (SAS) 是可用于限制数据访问权限的一项 Azure 存储帐户功能。 例如，它可以提供对数据的只读访问。

> [!IMPORTANT]
> 对于使用 Apache Ranger 的解决方案，请考虑使用已加入域的 HDInsight。 有关详细信息，请参阅[配置已加入域的 HDInsight](./domain-joined/apache-domain-joined-configure.md) 文档。

> [!WARNING]
> HDInsight 必须对群集的默认存储拥有完全访问权限。

## <a name="requirements"></a>要求

* Azure 订阅
* C# 或 Python。 已提供 C# 示例代码作为 Visual Studio 解决方案。

  * Visual Studio 的版本必须是 2013、2015 或 2017
  * Python 的版本必须是 2.7 或更高

* 基于 Linux 的 HDInsight 群集或 [Azure PowerShell][powershell] - 如果拥有现有的基于 Linux 的群集，可以使用 Ambari 将共享访问签名添加到群集。 如果没有，则可以使用 Azure PowerShell 创建群集，并在创建群集期间添加共享访问签名。

    > [!IMPORTANT]
    > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* 来自 [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature) 的示例文件。 此存储库包含以下项：

  * Visual Studio 项目，可以创建存储容器、存储策略，以及配合 HDInsight 使用的 SAS
  * Python 脚本，可以创建存储容器、存储策略，以及配合 HDInsight 使用的 SAS
  * PowerShell 脚本，可以创建 HDInsight 群集并将其配置为使用 SAS。

## <a name="shared-access-signatures"></a>共享访问签名

共享访问签名有两种形式：

* 即席：针对该 SAS 的开始时间、到期时间和权限全都在 SAS URI 上指定。

* 存储访问策略：存储访问策略在资源容器（例如 Blob 容器）中定义。 可以使用策略管理一个或多个共享访问签名的约束。 在将某一 SAS 与一个存储访问策略相关联时，该 SAS 将继承对该存储访问策略定义的约束：开始时间、到期时间和权限。

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

### <a name="create-a-stored-policy-and-sas-using-c"></a>使用 C\# 创建存储策略和 SAS

1. 在 Visual Studio 中打开解决方案。

2. 在解决方案资源管理器中，右键单击 **SASToken** 项目并选择“属性”。

3. 选择“设置”，并添加以下条目的值：

   * StorageConnectionString：想要为其创建存储策略和 SAS 的存储帐户的连接字符串。 格式应为 `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`，其中 `myaccount` 是存储帐户名称，`mykey` 是存储帐户密钥。

   * ContainerName：想要限制访问的存储帐户中的容器。

   * SASPolicyName：要创建的存储策略所用的名称。

   * FileToUpload：上传到容器的文件的路径。

4. 运行该项目。 生成 SAS 之后，会显示如以下文本所示的信息：

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    保存 SAS 策略令牌、存储帐户名称和容器名称。 将存储帐户与 HDInsight 群集关联时，将使用这些值。

### <a name="create-a-stored-policy-and-sas-using-python"></a>使用 Python 创建存储策略和 SAS

1. 打开 SASToken.py 文件并更改以下值：

   * policy\_name：要创建的存储策略所用的名称。

   * storage\_account\_name：存储帐户的名称。

   * storage\_account\_key：存储帐户的密钥。

   * storage\_container\_name：想要限制访问的存储帐户中的容器。

   * example\_file\_path：上传到容器的文件的路径

2. 运行该脚本。 脚本完成后，会显示如以下文本所示的 SAS 令牌：

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    保存 SAS 策略令牌、存储帐户名称和容器名称。 将存储帐户与 HDInsight 群集关联时，将使用这些值。

## <a name="use-the-sas-with-hdinsight"></a>配合 HDInsight 使用 SAS

创建 HDInsight 群集时，必须指定主存储帐户，可以选择性地指定其他存储帐户。 这两种添加存储的方法都需要对所用存储帐户和容器拥有完全访问权限。

要使用共享访问签名来限制对容器的访问，请将一个自定义条目添加到群集的 **core-site** 配置中。

* 对于**基于 Windows** 或**基于 Linux** 的 HDInsight 群集，可以使用 PowerShell 在创建群集期间添加条目。
* 对于**基于 Linux** 的 HDInsight 群集，在创建群集后，可以使用 Ambari 更改配置。

### <a name="create-a-cluster-that-uses-the-sas"></a>创建使用 SAS 的群集

存储库的 `CreateCluster` 目录中包含创建使用 SAS 的 HDInsight 群集的示例。 若要使用该示例，请执行以下步骤：

1. 在文本编辑器中打开 `CreateCluster\HDInsightSAS.ps1` 文件，并修改位于文档开头的以下值。

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    例如，将 `'mycluster'` 更改为要创建的群集的名称。 创建存储帐户和 SAS 令牌时，SAS 值应该与先前步骤中的值匹配。

    更改值之后，请保存该文件。

2. 打开新的 Azure PowerShell 提示符。 如果不熟悉或尚未安装 Azure PowerShell，请参阅[安装和配置 Azure PowerShell][powershell]。

1. 在提示符下使用以下命令对 Azure 订阅进行身份验证：

    ```powershell
    Login-AzureRmAccount
    ```

    出现提示时，请使用 Azure 订阅的帐户登录。

    如果帐户与多个 Azure 订阅关联，可能需要使用 `Select-AzureRmSubscription` 来选择想要使用的订阅。

4. 在提示符下，将目录更改为包含 HDInsightSAS.ps1 文件的 `CreateCluster` 目录。 然后使用以下命令运行该脚本

    ```powershell
    .\HDInsightSAS.ps1
    ```

    当脚本运行时，在创建资源组和存储帐户时，它将记录输出到 PowerShell 提示符。 系统会提示输入 HDInsight 群集的 HTTP 用户。 此帐户用于保护群集的 HTTP/s 访问。

    如果要创建基于 Linux 的群集，系统会提示输入 SSH 用户帐户名称和密码。 此帐户用于远程登录到群集。

   > [!IMPORTANT]
   > 出现输入 HTTP/s 或 SSH 用户名和密码的提示时，必须提供符合以下条件的密码：
   >
   > * 长度必须至少为 10 个字符
   > * 必须至少包含一个数字
   > * 必须至少包含一个非字母数字字符
   > * 必须至少包含一个大写或小写字母

需要等待一段时间让此脚本完成，通常大约是 15 分钟。 如果脚本完成且没有发生任何错误，则会创建群集。

### <a name="use-the-sas-with-an-existing-cluster"></a>对现有群集使用 SAS

如果拥有现有的基于 Linux 的群集，可以通过执行以下步骤将 SAS 添加到 **core-site** 配置：

1. 打开群集的 Ambari Web UI。 此页的地址是 https://YOURCLUSTERNAME.azurehdinsight.net。 出现提示时，请使用在创建群集时使用的管理员名称 (admin) 和密码对群集进行身份验证。

2. 从 Ambari Web UI 的左侧，选择“HDFS”，并在页面的中间选择“配置”选项卡。

3. 选择“高级”选项卡，并向下滚动，找到“自定义 core-site”部分。

4. 展开“自定义 core-site”部分，并滚动到底部，选择“添加属性...”链接。 在“密钥”和“值”字段中使用以下值：

   * **密钥**：fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **值**：之前运行的 C# 或 Python 应用程序所返回的 SAS

     将 **CONTAINERNAME** 替换为用于 C# 或 SAS 应用程序的容器名称。 将 **STORAGEACCOUNTNAME** 替换为使用的存储帐户名。

5. 单击“添加”按钮以保存此密钥和值，并单击“保存”按钮以保存配置更改。 出现提示时，请添加更改的说明（例如，“添加 SAS 存储访问”），并单击“保存”。

    完成更改后，单击“确定”。

   > [!IMPORTANT]
   > 必须重启几个服务才能使更改生效。

6. 在 Ambari Web UI 中，从左侧的列表中选择“HDFS”，并从右侧的“服务操作”下拉列表中选择“重启所有受影响项”。 出现提示时，选择“确认全部重启”。

    对 MapReduce2 和 YARN 重复此过程。

7. 重启这些服务后，请选择每个服务，并从“服务操作”下拉列表中禁用维护模式。

## <a name="test-restricted-access"></a>测试限制的访问

若要验证已限制的访问，请使用以下方法：

* 对于**基于 Windows** 的 HDInsight 群集，请使用远程桌面连接到群集。 有关详细信息，请参阅[使用 RDP 连接到 HDInsight](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。

    连接之后，请使用桌面上的“Hadoop 命令行”图标打开命令提示符。

* 对于**基于 Linux** 的 HDInsight 群集，请使用 SSH 连接到群集。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

连接到群集后，使用以下步骤验证是否只能读取和列出 SAS 存储帐户中的项：

1. 要列出容器的内容，请在提示符下使用以下命令： 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    将 **SASCONTAINER** 替换为针对 SAS 存储帐户创建的容器名称。 将 SASACCOUNTNAME 替换为用于 SAS 的存储帐户名称。

    该列表包含创建容器和 SAS 时上传的文件。

2. 使用以下命令验证是否可以读取文件的内容。 如上一步中所述，替换 **SASCONTAINER** 和 **SASACCOUNTNAME**。 将 **FILENAME** 替换为前一个命令中显示的名称：

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    此命令列出文件的内容。

3. 使用以下命令将文件下载到本地文件系统：

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    此命令会将文件下载到名为 **testfile.txt** 的本地文件中。

4. 使用以下命令将本地文件上传到 SAS 存储上名为 testupload.txt 的新文件中：

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    将收到类似于以下文本的消息：

        put: java.io.IOException

    发生此错误的原因是存储位置是只读+仅限列出的。 使用以下命令将数据放在群集的可写默认存储中：

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    这一次操作应会成功完成。

## <a name="troubleshooting"></a>故障排除

### <a name="a-task-was-canceled"></a>任务已取消

**症状**：使用 PowerShell 脚本创建群集时，可能会收到以下错误消息：

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**原因**：如果使用群集管理员/HTTP 用户的密码，或（对于基于 Linux 的群集）SSH 用户的密码，则可能发生此错误。

**解决方法**：使用符合以下条件的密码：

* 长度必须至少为 10 个字符
* 必须至少包含一个数字
* 必须至少包含一个非字母数字字符
* 必须至少包含一个大写或小写字母

## <a name="next-steps"></a>后续步骤

现在你已了解如何将访问受限的存储添加到 HDInsight 群集，接下来请了解在群集上处理数据的其他方法：

* [将 Hive 与 HDInsight 配合使用](hadoop/hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hadoop/hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
