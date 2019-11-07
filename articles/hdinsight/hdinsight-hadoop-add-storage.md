---
title: 将其他 Azure 存储帐户添加到 HDInsight
description: 了解如何将其他 Azure 存储帐户添加到现有 HDInsight 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e29041942157e720cce3414f7b6e6904667c1894
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665478"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>将其他存储帐户添加到 HDInsight

了解如何使用脚本操作，将其他 Azure 存储帐户添加到 HDInsight。 本文档中的步骤会将存储帐户添加到基于 Linux 的现有 HDInsight 群集。 本文适用于存储*帐户*（而不是默认群集存储帐户），而不适用于其他存储，例如[Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md)和[Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md)。

> [!IMPORTANT]  
> 本文档中的信息介绍了如何在创建群集后将其他存储帐户添加到群集。 有关如何在创建群集期间添加存储帐户的信息，请参阅[使用 Apache Hadoop、Apache Spark、Apache Kafka 等设置 HDInsight 中的群集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](./hadoop/apache-hadoop-linux-tutorial-get-started.md)。
* 存储帐户名和密钥。 请参阅[在 Azure 门户中管理存储帐户设置](../storage/common/storage-account-manage.md)。
* [具有正确大小写格式的群集名称](hdinsight-hadoop-manage-ambari-rest-api.md#identify-correctly-cased-cluster-name)。
* 如果使用 PowerShell，则需 AZ 模块。  请参阅 [Azure PowerShell 概述](https://docs.microsoft.com/powershell/azure/overview)。
* 如果尚未安装 Azure CLI，请参阅 [Azure 命令行界面 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。
* 如果使用 Bash 或 Windows 命令提示符，则还需命令行 JSON 处理器 **jq**。  请参阅 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。 有关 Windows 10 上的 Bash on Ubuntu，请参阅[适用于 Linux 的 Windows 子系统安装指南 - Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)。

## <a name="how-it-works"></a>工作原理

此脚本采用以下参数：

* __Azure 存储帐户名称__：要添加到 HDInsight 群集的存储器帐户的名称。 运行脚本后，HDInsight 能读取和写入存储在此存储帐户中的数据。

* __Azure 存储帐户密钥__：授予对存储帐户的访问权限的密钥。

* __-p__ （可选）：如果指定，则密钥不会加密，并以纯文本形式存储在 core-site.xml 文件中。

处理过程中，此脚本执行以下操作：

* 如果存储帐户已存在于群集的 core-site.xml 配置中，则脚本将退出，且不执行进一步操作。

* 使用密钥验证该存储帐户是否存在并且是否可以访问。

* 使用群集凭据对密钥进行加密。

* 将存储帐户添加到 core-site.xml 文件中。

* 停止并重启 [Apache Oozie](https://oozie.apache.org/)、[Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)、[Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) 和 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) 服务。 通过停止和重启这些服务来使用新的存储帐户。

> [!WARNING]  
> 不支持在 HDInsight 群集之外的其他位置使用存储帐户。

## <a name="the-script"></a>脚本

__脚本位置__：[https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__要求__：脚本必须应用在__头节点__上。 无需将此脚本标记为__持久化__，因为它直接更新群集的 Ambari 配置。

## <a name="to-use-the-script"></a>使用脚本

可以通过 Azure PowerShell、Azure CLI 或 Azure 门户使用此脚本。

### <a name="powershell"></a>PowerShell

使用 [Submit-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/submit-azhdinsightscriptaction)。 将 `CLUSTERNAME`、`ACCOUNTNAME`、`ACCOUNTKEY` 替换为相应的值。

```powershell
# Update these parameters
$clusterName = "CLUSTERNAME"
$parameters = "ACCOUNTNAME ACCOUNTKEY"

$scriptActionName = "addStorage"
$scriptActionUri = "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh"

# Execute script
Submit-AzHDInsightScriptAction `
    -ClusterName $clusterName `
    -Name $scriptActionName `
    -Uri $scriptActionUri `
    -NodeTypes "headnode" `
    -Parameters $parameters
```

### <a name="azure-cli"></a>Azure CLI

使用 [az hdinsight script-action execute](https://docs.microsoft.com/cli/azure/hdinsight/script-action?view=azure-cli-latest#az-hdinsight-script-action-execute)。  将 `CLUSTERNAME`、`RESOURCEGROUP`、`ACCOUNTNAME`、`ACCOUNTKEY` 替换为相应的值。

```cli
az hdinsight script-action execute ^
    --name CLUSTERNAME ^
    --resource-group RESOURCEGROUP ^
    --roles headnode ^
    --script-action-name addStorage ^
    --script-uri "https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh" ^
    --script-parameters "ACCOUNTNAME ACCOUNTKEY"
```

### <a name="azure-portal"></a>Azure 门户

请参阅[将脚本操作应用到正在运行的群集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)。

## <a name="known-issues"></a>已知问题

### <a name="storage-firewall"></a>存储防火墙

如果选择在“选定网络”上通过“防火墙和虚拟网络”限制来保护存储帐户的安全，请务必启用例外“允许受信任的 Microsoft 服务...”，这样 HDInsight 就能访问存储帐户。

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>存储帐户未显示在 Azure 门户或工具中

查看 Azure 门户中的 HDInsight 群集时，选择 "__属性__" 下的 "__存储帐户__" 条目不会显示通过此脚本操作添加的存储帐户。 Azure PowerShell 和 Azure CLI 不显示其他存储帐户。

存储信息未显示是因为该脚本只修改群集的 core-site.xml 配置。 使用 Azure 管理 Api 检索群集信息时，不使用此信息。

若要查看使用此脚本添加到群集的存储帐户信息，请使用 Ambari REST API。 使用以下命令检索此群集信息：

### <a name="powershell"></a>PowerShell

将 `CLUSTERNAME` 替换为大小写正确的群集名称。 将 `ACCOUNTNAME` 替换为实际名称。 出现提示时，输入群集登录名和密码。

```powershell
# Update values
$clusterName = "CLUSTERNAME"
$accountName = "ACCOUNTNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$accountName.blob.core.windows.net"
```

### <a name="bash"></a>bash

将 `CLUSTERNAME` 替换为大小写正确的群集名称。 将 `PASSWORD` 替换为群集管理员密码。 将 `STORAGEACCOUNT` 替换为实际的存储帐户名称。

```bash
export clusterName="CLUSTERNAME"
export password='PASSWORD'
export storageAccount="STORAGEACCOUNT"

export ACCOUNTNAME='"'fs.azure.account.key.$storageAccount.blob.core.windows.net'"'

export configVersion=$(curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_service_config_versions/HDFS" \
| jq ".Clusters.desired_service_config_versions.HDFS[].service_config_version")

curl --silent -u admin:$password -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" \
| jq ".items[].configurations[].properties[$ACCOUNTNAME] | select(. != null)"
```

### <a name="cmd"></a>cmd

在两个脚本中将 `CLUSTERNAME` 替换为大小写正确的群集名称。 首先输入以下命令，确定正在使用的服务配置版本：

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_service_config_versions/HDFS" | ^
jq-win64 ".Clusters.desired_service_config_versions.HDFS[].service_config_version"
```

将 `ACCOUNTNAME` 替换为实际的存储帐户名称。 然后将 `4` 替换为实际的服务配置版本并输入以下命令：

```cmd
curl --silent -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=4" | ^
jq-win64 ".items[].configurations[].properties["""fs.azure.account.key.ACCOUNTNAME.blob.core.windows.net"""] | select(. != null)"
```

---

此命令返回类似于以下文本的信息：

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

此文本是加密密钥示例，用于访问存储帐户。

### <a name="unable-to-access-storage-after-changing-key"></a>更改密钥后，无法访问存储

如果更改存储帐户的密钥，则 HDInsight 不再能够访问该存储帐户。 HDInsight 将 core-site.xml 中密钥的缓存副本用于群集。 必须更新此缓存的副本，使之匹配新的密钥。

重新运行脚本操作__不会__更新密钥，因为该脚本会检查存储帐户的某个条目是否已存在。 如果某个条目已存在，则不进行任何更改。

若要解决此问题，必须删除存储帐户的现有条目。 使用以下步骤删除现有条目：

> [!IMPORTANT]  
> 对于附加到群集的主存储帐户，不支持轮换使用存储密钥。

1. 在 Web 浏览器中，打开 HDInsight 群集的 Ambari Web UI。 该 URI 为 `https://CLUSTERNAME.azurehdinsight.net`。 将 `CLUSTERNAME` 替换为群集的名称。

    出现提示时，请输入群集的 HTTP 登录用户和密码。

2. 从页面左侧的服务列表中选择“HDFS”。 然后，在页面中心选择 __“配置”__ 选项卡。

3. 在“筛选...”字段中，输入值 __fs.azure.account__。 这会返回已添加到群集的任何其他存储帐户的条目。 条目的类型为两种：__keyprovider__ 和 __key__。 这两种类型都包含存储帐户名称，作为密钥名称的一部分。

    以下是名为 __mystorage__ 的存储帐户的示例条目：

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. 确定要删除的存储帐户的密钥后，使用条目右侧的红色 "-" 图标将其删除。 然后使用 __“保存”__ 按钮保存更改。

5. 保存更改后，使用脚本操作将存储帐户和新的密钥值添加到群集。

### <a name="poor-performance"></a>性能低下

如果存储帐户与 HDInsight 群集位于不同的区域，则可能会遇到性能低下问题。 访问不同区域中的数据会在区域 Azure 数据中心外部跨公共 Internet 发送网络流量，从而会导致延迟。

### <a name="additional-charges"></a>额外费用

如果存储帐户与 HDInsight 群集位于不同的区域，则可能会在 Azure 帐单上发现额外的出口费用。 当数据离开区域数据中心时，将收取出口费用。 即使流量发往另一区域中的另一个 Azure 数据中心，也将收取此费用。

## <a name="next-steps"></a>后续步骤

已了解如何将其他存储帐户添加到现有 HDInsight 群集。 有关脚本操作的详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)
