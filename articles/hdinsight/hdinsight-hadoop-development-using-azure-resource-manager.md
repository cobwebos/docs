---
title: 迁移到适用于 HDInsight 的 Azure 资源管理器工具 | Microsoft Docs
description: 如何迁移到适用于 HDInsight 群集的 Azure 资源管理器开发工具
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: ''
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: d0182afbd1a6beaabadf68f08905316be4ba034f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>迁移到适用于 HDInsight 群集的基于 Azure 资源管理器的开发工具

HDInsight 即将淘汰适用于 HDInsight 的基于 Azure 服务管理器 (ASM) 的工具。 如果一直在使用 Azure PowerShell、Azure CLI 或 HDInsight.NET SDK 来处理 HDInsight 群集，我们建议跟随潮流，使用 Azure 资源管理器的 PowerShell、CLI 和.NET SDK 版本。 本文章提供有关如何迁移到基于资源管理器的新方法的指导。 本文档将重点介绍适用于 HDInsight 的 ASM 与资源管理器方法之间的差异（如果适用）。

> [!IMPORTANT]
> **2017 年 1 月 1 日**，对基于 ASM 的 PowerShell CLI 和 .NET SDK 的支持会终止。
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>将 Azure CLI 迁移到 Azure 资源管理器

> [!IMPORTANT]
> Azure CLI 2.0 未提供针对使用 HDInsight 群集的支持。 因此，仍可通过 Azure CLI 1.0 来使用 HDInsight，但是 Azure CLI 1.0 已弃用。

以下是通过 Azure CLI 1.0 使用 HDInsight 的基本命令：

* `azure hdinsight cluster create` - 创建新的 HDInsight 群集
* `azure hdinsight cluster delete` - 删除现有的 HDInsight 群集
* `azure hdinsight cluster show` - 显示有关现有群集的信息
* `azure hdinsight cluster list` - 列出 Azure 订阅的 HDInsight 群集

使用 `-h` 开关可以检查每个命令可用的参数和开关。

### <a name="new-commands"></a>新命令
可用于 Azure 资源管理器的新命令包括：

* `azure hdinsight cluster resize` - 动态更改群集中的辅助角色节点数目
* `azure hdinsight cluster enable-http-access` - 启用对群集的 HTTPs 访问（默认为打开）
* `azure hdinsight cluster disable-http-access` - 禁用对群集的 HTTPs 访问
* `azure hdinsight script-action` - 在群集上提供用于创建/管理脚本操作的命令
* `azure hdinsight config` - 提供用于创建配置文件的命令，该配置文件可与 `hdinsight cluster create` 命令一起使用以提供配置信息。

### <a name="deprecated-commands"></a>已过时的命令
如果使用 `azure hdinsight job` 命令将作业提交到 HDInsight 群集，则这些命令无法通过资源管理器命令提供。 如果需要以编程方式通过脚本将作业提交到 HDInsight，应改用 HDInsight 提供的 REST API。 有关如何使用 REST API 提交作业的详细信息，请参阅以下文档。

* [使用 cURL 在 HDInsight 上的 Hadoop 上远程运行 MapReduce 作业](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [使用 cURL 对 HDInsight 上的 Hadoop 运行 Hive 查询](hadoop/apache-hadoop-use-hive-curl.md)
* [使用 cURL 配合 HDInsight 上的 Hadoop 运行 Pig 作业](hadoop/apache-hadoop-use-pig-curl.md)

有关以其他交互方式运行 MapReduce、Hive 和 Pig 的信息，请参阅[将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hadoop/hdinsight-use-mapreduce.md)、[将 Hive 与 Hadoop 配合使用](hadoop/hdinsight-use-hive.md)和[将 Pig 与 HDInsight 上的 Hadoop 配合使用](hadoop/hdinsight-use-pig.md)。

### <a name="examples"></a>示例
**创建群集**

* 旧命令 (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* 新命令 - `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**删除群集**

* 旧命令 (ASM) - `azure hdinsight cluster delete myhdicluster`
* 新命令 - `azure hdinsight cluster delete mycluster -g myresourcegroup`

**列出群集**

* 旧命令 (ASM) - `azure hdinsight cluster list`
* 新命令 - `azure hdinsight cluster list`

> [!NOTE]
> 运行 list 命令时，使用 `-g` 指定资源组只会返回指定资源组中的群集。
> 
> 

**显示群集信息**

* 旧命令 (ASM) - `azure hdinsight cluster show myhdicluster`
* 新命令 - `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>将 Azure PowerShell 迁移到 Azure 资源管理器
有关处于 Azure 资源管理器模式的 Azure PowerShell 的一般信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](../powershell-azure-resource-manager.md)。

Azure PowerShell 资源管理器 cmdlet 可与 ASM cmdlet 一同安装。 两种模式下的 cmdlet 可按其名称来区分。  资源管理器模式下的 cmdlet 名称中包含 AzureRmHDInsight，而在 ASM 模式下则包含 AzureHDInsight。  例如，前者为 *New-AzureRmHDInsightCluster*，后者为*New-AzureHDInsightCluster*. 某些参数和开关可能有新名称，并且在使用资源管理器时，有许多新参数可供使用。  例如，多个 cmdlet 需要名为 *-ResourceGroupName* 的新开关。 

在使用这些 HDInsight cmdlet 之前，必须先连接到 Azure 帐户并创建新资源组：

* Connect-AzureRmAccount 或 [Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx)。 请参阅[使用 Azure 资源管理器对服务主体进行身份验证](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>已重命名的 cmdlet
在 Windows PowerShell 控制台中列出 HDInsight ASM cmdlet：

    help *azurermhdinsight*

下表列出了 ASM cmdlet 及其在资源管理器模式下的名称：

| ASM cmdlet | 资源管理器 cmdlet |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>新 cmdlet
以下是只能在资源管理器模式下使用的新 cmdlet。 

**与脚本操作相关的 cmdlet：**

* **Get-AzureRmHDInsightPersistedScriptAction**：获取群集的持久性脚本操作，并按时间顺序列出这些操作，或获取有关指定持久性脚本操作的详细信息。 
* **Get-AzureRmHDInsightScriptActionHistory**：获取群集的脚本操作历史记录，并按时间顺序逆序列出这些操作，或获取有关以前执行的脚本操作的详细信息。 
* **Remove-AzureRmHDInsightPersistedScriptAction**：从 HDInsight 群集中删除持久性脚本操作。
* **Set-AzureRmHDInsightPersistedScriptAction**：以前执行的脚本操作设置为持久性脚本操作。
* **Submit-AzureRmHDInsightScriptAction**：将新的脚本操作提交到 Azure HDInsight 群集。 

有关其他用法信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

**与群集标识相关的 cmdlet：**

* **Add-AzureRmHDInsightClusterIdentity**：将群集标识添加到群集配置对象，使 HDInsight 群集能够访问 Azure Data Lake Store。 请参阅[使用 Azure PowerShell 创建包含 Data Lake Store 的 HDInsight 群集](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)。

### <a name="examples"></a>示例
**创建群集**

旧命令 (ASM)： 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

新命令：

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**删除群集**

旧命令 (ASM)：

    Remove-AzureHDInsightCluster -name $clusterName 

新命令：

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**列出群集**

旧命令 (ASM)：

    Get-AzureHDInsightCluster

新命令：

    Get-AzureRmHDInsightCluster 

**显示群集**

旧命令 (ASM)：

    Get-AzureHDInsightCluster -Name $clusterName

新命令：

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>其他示例
* [创建 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [提交 Hive 作业](hadoop/apache-hadoop-use-hive-powershell.md)
* [提交 Pig 作业](hadoop/apache-hadoop-use-pig-powershell.md)
* [提交 Sqoop 作业](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>迁移到新的 HDInsight .NET SDK
基于 Azure 服务管理 (ASM) 的 [HDInsight.NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) 现已弃用。 建议使用基于 Azure 资源管理器的 [基于资源管理器的 HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt271028.aspx)。 以下基于 ASM 的 HDInsight 包即将过时。

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

本部分提供有关如何使用基于资源管理器的 SDK 来执行特定任务的详细指导。

| 如何...使用基于资源管理器的 HDInsight SDK | 链接 |
| --- | --- |
| 使用 .NET SDK 创建 HDInsight 群集 |请参阅[使用 .NET SDK 创建 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| 配合使用脚本操作与 .NET SDK 来自定义群集 |请参阅[使用脚本操作自定义 HDInsight Linux 群集](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| 配合使用 Azure Active Directory 与 .NET SDK 以交互方式对应用程序进行身份验证 |请参阅[使用 .NET SDK 运行 Hive 查询](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)。 本文中的代码段使用交互式身份验证方法。 |
| 配合使用 Azure Active Directory 与 .NET SDK 以非交互方式对应用程序进行身份验证 |请参阅[为 HDInsight 创建非交互式应用程序](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| 使用 .NET SDK 提交 Hive 作业 |请参阅[提交 Hive 作业](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| 使用 .NET SDK 提交 Pig 作业 |请参阅[提交 Pig 作业](hadoop/apache-hadoop-use-pig-dotnet-sdk.md) |
| 使用 .NET SDK 提交 Sqoop 作业 |请参阅[提交 Sqoop 作业](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| 使用 .NET SDK 列出 HDInsight 群集 |请参阅[列出 HDInsight 群集](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| 使用 .NET SDK 缩放 HDInsight 群集 |请参阅[缩放 HDInsight 群集](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| 使用 .NET SDK 授予/吊销对 HDInsight 群集的访问权限 |请参阅[授予/吊销对 HDInsight 群集的访问权限](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| 使用 .NET SDK 更新 HDInsight 群集的 HTTP 用户凭据 |请参阅[更新 HDInsight 群集的 HTTP 用户凭据](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| 使用 .NET SDK 查找 HDInsight 群集的默认存储帐户 |请参阅[查找 HDInsight 群集的默认存储帐户](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| 使用 .NET SDK 删除 HDInsight 群集 |请参阅[删除 HDInsight 群集](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>示例
以下是有关如何使用基于 ASM 的 SDK 和基于资源管理器的 SDK 的等效代码段来执行操作的一些示例。

**创建群集 CRUD 客户端**

* 旧命令 (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* 新命令（服务主体授权）
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* 新命令（用户授权）
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**创建群集**

* 旧命令 (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* 新命令
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**启用 HTTP 访问**

* 旧命令 (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* 新命令
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**删除群集**

* 旧命令 (ASM)
  
        client.DeleteCluster(dnsName);
* 新命令
  
        client.Clusters.Delete(resourceGroup, dnsname);

