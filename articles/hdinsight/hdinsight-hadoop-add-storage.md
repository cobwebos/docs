---
title: "将其他 Azure 存储帐户添加到 HDInsight | Microsoft Docs"
description: "了解如何将其他 Azure 存储帐户添加到现有 HDInsight 群集。"
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/24/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 68229487f45c3246500875303105b0ebcc8079cb
ms.sourcegitcommit: 4d90200f49cc60d63015bada2f3fc4445b34d4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>将其他存储帐户添加到 HDInsight

了解如何使用脚本操作，将其他 Azure 存储帐户添加到 HDInsight。 本文档中的步骤会将存储帐户添加到基于 Linux 的现有 HDInsight 群集。

> [!IMPORTANT]
> 本文档中的信息介绍在创建群集后，如何将其他存储添加到此群集。 有关在创建群集期间添加存储帐户的信息，请参阅[使用 Hadoop、Spark、Kafka 等设置 HDInsight 中的群集](hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="how-it-works"></a>工作原理

此脚本采用以下参数：

* __Azure 存储帐户名称__：要添加到 HDInsight 群集的存储器帐户的名称。 运行脚本后，HDInsight 能读取和写入存储在此存储帐户中的数据。

* __Azure 存储帐户密钥__：授予对存储帐户的访问权限的密钥。

* __-p__（可选）：如果指定此参数，则密钥不会加密，并以纯文本形式存储在 core-site.xml 文件中。

处理过程中，此脚本执行以下操作：

* 如果存储帐户已存在于群集的 core-site.xml 配置中，则脚本将退出，且不执行进一步操作。

* 验证存储帐户是否存在，以及是否可以使用密钥访问。

* 使用群集凭据对密钥进行加密。

* 将存储帐户添加到 core-site.xml 文件中。

* 停止并重启 Oozie、YARN、MapReduce2 和 HDFS 服务。 通过停止和重启这些服务来使用新的存储帐户。

> [!WARNING]
> 不支持在 HDInsight 群集之外的其他位置使用存储帐户。

## <a name="the-script"></a>脚本

__脚本位置__：[https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)

__要求__：

* 该脚本必须应用在__头节点__上。

## <a name="to-use-the-script"></a>使用脚本

可以通过 Azure 门户、Azure PowerShell 或 Azure CLI 1.0 使用此脚本。 有关详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)文档。

> [!IMPORTANT]
> 当使用自定义文档中所提供的步骤时，请使用以下信息来应用此脚本：
>
> * 将任意示例脚本操作 URI 替换为此脚本的 URI (https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh)。
> * 使用要添加到群集的存储帐户的 Azure 存储帐户名称和密钥替换任何示例参数。 如果使用 Azure 门户，则必须以空格来分隔这些参数。
> * 无需将此脚本标记为“持久化”，因为它直接更新群集的 Ambari 配置。

## <a name="known-issues"></a>已知问题

### <a name="storage-accounts-not-displayed-in-azure-portal-or-tools"></a>存储帐户未显示在 Azure 门户或工具中

在 Azure 门户中查看 HDInsight 群集时，选择“属性”下的“存储帐户”项，则不会显示通过此脚本操作添加的存储帐户。 Azure PowerShell 和 Azure CLI 也不会显示其他存储帐户。

存储信息未显示是因为该脚本只修改群集的 core-site.xml 配置。 使用 Azure 管理 API 检索群集信息时，不使用此信息。

若要查看使用此脚本添加到群集的存储帐户信息，请使用 Ambari REST API。 使用以下命令检索此群集信息：

```PowerShell
$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties."fs.azure.account.key.$storageAccountName.blob.core.windows.net"
```

> [!NOTE]
> 将 `$clusterName` 设置为 HDInsight 群集的名称。 将 `$storageAccountName` 设置为存储帐户的名称。 出现提示时，输入群集登录名 (admin) 和密码。

```Bash
curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.azure.account.key.$STORAGEACCOUNTNAME.blob.core.windows.net"] | select(. != null)'
```

> [!NOTE]
> 将 `$PASSWORD` 设置为群集登录（管理员）帐户密码。 将 `$CLUSTERNAME` 设置为 HDInsight 群集的名称。 将 `$STORAGEACCOUNTNAME` 设置为存储帐户的名称。
>
> 此示例使用 [curl (http://curl.haxx.se/)](http://curl.haxx.se/) 和 [jq (https://stedolan.github.io/jq/)](https://stedolan.github.io/jq/) 来检索和分析 JSON 数据。

使用此命令时，将 __CLUSTERNAME__ 替换为 HDInsight 群集的名称。 将 __PASSWORD__ 替换为群集的 HTTP 登录密码。 将 __STORAGEACCOUNT__ 替换为使用脚本操作添加的存储帐户的名称。 此命令返回类似于以下文本的信息：

    "MIIB+gYJKoZIhvcNAQcDoIIB6zCCAecCAQAxggFaMIIBVgIBADA+MCoxKDAmBgNVBAMTH2RiZW5jcnlwdGlvbi5henVyZWhkaW5zaWdodC5uZXQCEA6GDZMW1oiESKFHFOOEgjcwDQYJKoZIhvcNAQEBBQAEggEATIuO8MJ45KEQAYBQld7WaRkJOWqaCLwFub9zNpscrquA2f3o0emy9Vr6vu5cD3GTt7PmaAF0pvssbKVMf/Z8yRpHmeezSco2y7e9Qd7xJKRLYtRHm80fsjiBHSW9CYkQwxHaOqdR7DBhZyhnj+DHhODsIO2FGM8MxWk4fgBRVO6CZ5eTmZ6KVR8wYbFLi8YZXb7GkUEeSn2PsjrKGiQjtpXw1RAyanCagr5vlg8CicZg1HuhCHWf/RYFWM3EBbVz+uFZPR3BqTgbvBhWYXRJaISwssvxotppe0ikevnEgaBYrflB2P+PVrwPTZ7f36HQcn4ifY1WRJQ4qRaUxdYEfzCBgwYJKoZIhvcNAQcBMBQGCCqGSIb3DQMHBAhRdscgRV3wmYBg3j/T1aEnO3wLWCRpgZa16MWqmfQPuansKHjLwbZjTpeirqUAQpZVyXdK/w4gKlK+t1heNsNo1Wwqu+Y47bSAX1k9Ud7+Ed2oETDI7724IJ213YeGxvu4Ngcf2eHW+FRK"

此文本是加密密钥示例，用于访问存储帐户。

### <a name="unable-to-access-storage-after-changing-key"></a>更改密钥后，无法访问存储

如果更改存储帐户的密钥，则 HDInsight 不再能够访问该存储帐户。 HDInsight 将 core-site.xml 中密钥的缓存副本用于群集。 必须更新此缓存的副本，使之匹配新的密钥。

重新运行脚本操作__不会__更新密钥，因为该脚本会检查存储帐户的某个条目是否已存在。 如果已存在条目，则不进行任何更改。

若要解决此问题，必须删除存储帐户的现有条目。 按以下步骤删除现有条目：

1. 在 Web 浏览器中，打开 HDInsight 群集的 Ambari Web UI。 该 URI 是 https://CLUSTERNAME.azurehdinsight.net。 将 __CLUSTERNAME__ 替换为群集名称。

    出现提示时，请输入群集的 HTTP 登录用户和密码。

2. 从页面左侧的服务列表中选择“HDFS”。 然后，在页面中心选择__“配置”__选项卡。

3. 在“筛选...”字段中，输入值 __fs.azure.account__。 这会返回已添加到群集的任何其他存储帐户的条目。 条目的类型为两种：__keyprovider__ 和 __key__。 这两种类型都包含存储帐户名称，作为密钥名称的一部分。

    以下是名为 __mystorage__ 的存储帐户的示例条目：

        fs.azure.account.keyprovider.mystorage.blob.core.windows.net
        fs.azure.account.key.mystorage.blob.core.windows.net

4. 确定需要删除的存储帐户密钥后，使用条目右侧的红色“-”图标将其删除。 然后使用__“保存”__按钮保存更改。

5. 保存更改后，使用脚本操作将存储帐户和新的密钥值添加到群集。

### <a name="poor-performance"></a>性能低下

如果存储帐户与 HDInsight 群集位于不同的区域，则可能会遇到性能低下问题。 访问不同区域中的数据会在区域 Azure 数据中心之外和公共 Internet 上发送网络流量，这会导致延迟。

> [!WARNING]
> 不支持在 HDInsight 群集之外的其他区域使用存储帐户。

### <a name="additional-charges"></a>额外费用

如果存储帐户与 HDInsight 群集位于不同的区域，则可能会在 Azure 帐单上发现额外的出口费用。 当数据离开区域数据中心时，将收取出口费用。 即使流量发往另一区域中的另一个 Azure 数据中心，也将收取此费用。

> [!WARNING]
> 不支持在 HDInsight 群集之外的其他区域使用存储帐户。

## <a name="next-steps"></a>后续步骤

你已了解如何将其他存储帐户添加到现有 HDInsight 群集。 有关脚本操作的详细信息，请参阅[使用脚本操作自定义基于 Linux 的 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)
