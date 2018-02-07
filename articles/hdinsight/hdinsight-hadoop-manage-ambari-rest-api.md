---
title: "使用 Ambari REST API 监视和管理 Hadoop - Azure HDInsight | Microsoft Docs"
description: "了解如何使用 Ambari 监视和管理 Azure HDInsight 中的 Hadoop 群集。 在本文档中，学习如何使用 HDInsight 群集随附的 Ambari REST API。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2400530f-92b3-47b7-aa48-875f028765ff
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/22/2018
ms.author: larryfr
ms.openlocfilehash: 44066a3b4d5bebc67b089bebc2f3c11d33c77000
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-rest-api"></a>使用 Ambari REST API 管理 HDInsight 群集

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

了解如何使用 Ambari REST API 监视和管理 Azure HDInsight 中的 Hadoop 群集。

Apache Ambari 提供简单易用的 Web UI 和 REST API 来简化 Hadoop 群集的管理和监视。 Ambari 包含在使用 Linux 操作系统的 HDInsight 群集中。 可以使用 Ambari 监视群集和进行配置更改。

## <a id="whatis"></a>什么是 Ambari

[Apache Ambari](http://ambari.apache.org) 提供可用于管理和监视 Hadoop 群集的 Web UI。 开发人员可以使用 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) 在其应用程序中集成这些功能。

基于 Linux 的 HDInsight 群集已按默认提供 Ambari。

## <a name="how-to-use-the-ambari-rest-api"></a>如何使用 Ambari REST API

> [!IMPORTANT]
> 本文档中的信息和示例要求使用以 Linux 作为操作系统的 HDInsight 群集。 有关详细信息，请参阅 [HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

本文档针对 Bourne 外壳 (bash) 和 PowerShell 都提供了示例。 测试 bash 示例时使用的是 GNU bash 版本 4.3.11，但应结合使用其他 Unix shell。 PowerShell 示例进行测试时使用的是 PowerShell 5.0，但应当也可以与 PowerShell 3.0 或更高版本一起工作。

如果使用 __Bourne 外壳__ (Bash)，则必须安装以下各项：

* [cURL](http://curl.haxx.se/)：cURL 是一个可用于从命令行操作 REST API 的实用工具。 在本文档中，将使用它来与 Ambari REST API 通信。

无论是使用 Bash 还是 PowerShell，都还必须安装 [jq](https://stedolan.github.io/jq/)。 Jq 是一个可用于操作 JSON 文档的实用工具。 **所有** Bash 示例都使用了该实用工具，PowerShell 示例中有**一个**使用了该实用工具。

### <a name="base-uri-for-ambari-rest-api"></a>Ambari Rest API 的基本 URI

HDInsight 上 Ambari REST API 的基本 URI 是 https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME，其中 **CLUSTERNAME** 是群集的名称。

> [!IMPORTANT]
> URI 的完全限定域名 (FQDN) 部分 (CLUSTERNAME.azurehdinsight.net) 中的群集名称不区分大小写，但 URI 中的其他部分则区分大小写。 例如，如果群集名称为 `MyCluster`，则有效的 URI 如下：
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/MyCluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/MyCluster`
> 
> 以下 URI 返回错误，因为名称的第二个部分未正确使用大小写。
> 
> `https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster`
>
> `https://MyCluster.azurehdinsight.net/api/v1/clusters/mycluster`

### <a name="authentication"></a>身份验证

连接到 HDInsight 上的 Ambari 需要 HTTPS。 使用在群集创建过程中提供的管理员帐户名称（默认值是 **admin**）和密码。

## <a name="examples-authentication-and-parsing-json"></a>示例：身份验证和解析 JSON

以下示例演示了如何针对基本 Ambari REST API 发出 GET 请求：

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
```

> [!IMPORTANT]
> 本文档中的 Bash 示例采用以下假设：
>
> * 群集的登录名是默认值 `admin`。
> * `$CLUSTERNAME` 包含群集名称。 可以使用 `set CLUSTERNAME='clustername'` 设置此值
> * 出现提示时，输入群集登录（管理员）帐户的密码。

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$resp.Content
```

> [!IMPORTANT]
> 本文档中的 PowerShell 示例采用以下假设：
>
> * `$creds` 是一个凭据对象，包含群集的管理员登录名和密码。 可以通过使用 `$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"` 并在出现提示时提供凭据来设置此值。
> * `$clusterName` 是一个包含群集名称的字符串。 可以使用 `$clusterName="clustername"` 设置此值。

两种示例都返回 JSON 文档，该文档开头部分的信息与以下示例类似：

```json
{
"href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
"Clusters" : {
    "cluster_id" : 2,
    "cluster_name" : "CLUSTERNAME",
    "health_report" : {
    "Host/stale_config" : 0,
    "Host/maintenance_state" : 0,
    "Host/host_state/HEALTHY" : 7,
    "Host/host_state/UNHEALTHY" : 0,
    "Host/host_state/HEARTBEAT_LOST" : 0,
    "Host/host_state/INIT" : 0,
    "Host/host_status/HEALTHY" : 7,
    "Host/host_status/UNHEALTHY" : 0,
    "Host/host_status/UNKNOWN" : 0,
    "Host/host_status/ALERT" : 0
    ...
```

### <a name="parsing-json-data"></a>解析 JSON 数据

以下示例使用 `jq` 来解析 JSON 响应文档并仅显示结果中的 `health_report` 信息。

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME" \
| jq '.Clusters.health_report'
```

PowerShell 3.0 和更高版本提供了 `ConvertFrom-Json` cmdlet，该 cmdlet 可以将 JSON 文档转换为更容易通过 PowerShell 操作的对象。 以下示例使用 `ConvertFrom-Json` 来仅显示结果中的 `health_report` 信息。

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

> [!NOTE]
> 虽然本文档中的大多数示例使用 `ConvertFrom-Json` 来显示响应文档中的元素，但[更新 Ambari 配置](#example-update-ambari-configuration)示例使用了 jq。 Jq 在此示例中用来基于 JSON 响应文档构造一个新模板。

有关 REST API 的完整参考，请参阅 [Ambari API 参考 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

## <a name="example-get-the-fqdn-of-cluster-nodes"></a>示例：获取群集节点的 FQDN

使用 HDInsight 时，可能需要知道群集节点的完全限定域名 (FQDN)。 可以使用以下示例轻松检索群集中各个节点的 FQDN：

* **所有节点**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" \
    | jq '.items[].Hosts.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.Hosts.host_name
    ```

* **头节点**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/NAMENODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **辅助角色节点**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HDFS/components/DATANODE" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

* **Zookeeper 节点**

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
    | jq '.host_components[].HostRoles.host_name'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.host_components.HostRoles.host_name
    ```

## <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a>示例：获取群集节点的内部 IP 地址

> [!IMPORTANT]
> 本部分中的示例返回的 IP 地址不可直接通过 Internet 进行访问。 它们只可在包含 HDInsight 群集的 Azure 虚拟网络内访问。
>
> 有关将 HDInsight 与虚拟网络配合使用的详细信息，请参阅[使用 Azure 虚拟网络扩展 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

要查找 IP 地址，必须知道群集节点的内部完全限定的域名 (FQDN)。 在知悉 FQDN 后，可以获取主机的 IP 地址。 以下示例首先从 Ambari 查询所有主机节点的 FQDN，然后从 Ambari 查询每个主机的 IP 地址。

```bash
for HOSTNAME in $(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$PASSWORD -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```

> [!TIP]
> 前面的示例会提示输入密码。 此示例将多次运行 `curl` 命令，因此将以 `$PASSWORD` 形式提供密码以避免出现多次提示。

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts"
$resp = Invoke-WebRequest -Uri $uri -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

## <a name="example-get-the-default-storage"></a>示例：获取默认存储

创建 HDInsight 群集时，必须使用 Azure 存储帐户或 Data Lake Store 作为群集的默认存储。 创建群集后，可以使用 Ambari 来检索此信息。 例如，当希望从 HDInsight 外部的容器中读取数据或者将数据写入到其中时。

以下示例检索群集的默认存储配置：

```bash
curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]
> 这些示例将返回应用于服务器的第一个配置 (`service_config_version=1`)，其中包含此信息。 如果要检索创建群集后修改的值，可能需要列出配置版本并检索最新版本。

返回值类似于下列示例之一：

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - 此值表示群集使用 Azure 存储帐户作为默认存储。 `ACCOUNTNAME` 值是存储帐户的名称。 `CONTAINER` 部分是存储帐户中的 blob 容器的名称。 该容器是群集的 HDFS 兼容存储的根目录。

* `adl://home` - 此值表示群集使用 Azure Data Lake Store 作为默认存储。

    若要查找 Data Lake Store 帐户名称，请使用以下示例：

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    返回值类似于 `ACCOUNTNAME.azuredatalakestore.net`，其中，`ACCOUNTNAME` 是 Data Lake Store 帐户的名称。

    若要查找 Data Lake Store 中包含群集存储的目录，请使用以下示例：

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    返回值类似于 `/clusters/CLUSTERNAME/`。 此值是 Data Lake Store 帐户中的一个路径。 此路径是群集的 HDFS 兼容文件系统的根目录。 

> [!NOTE]
> [Azure PowerShell](/powershell/azure/overview) 提供的 `Get-AzureRmHDInsightCluster` cmdlet 也返回群集的存储信息。


## <a name="example-get-configuration"></a>示例：获取配置

1. 获取适用于群集的配置。

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    $respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    $respObj.Content
    ```

    此示例将返回一个 JSON 文档，其中包含群集上安装的组件的当前配置（由 *tag* 值标识）。 以下示例是从 Spark 群集类型返回的数据摘录。
   
   ```json
   "spark-metrics-properties" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-fairscheduler" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   },
   "spark-thrift-sparkconf" : {
       "tag" : "INITIAL",
       "user" : "admin",
       "version" : 1
   }
   ```

2. 获取感兴趣的组件的配置。 在以下示例中，将 `INITIAL` 替换为从上一请求返回的标记值。

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=core-site&tag=INITIAL"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=core-site&tag=INITIAL" `
        -Credential $creds
    $resp.Content
    ```

    此示例返回的 JSON 文档包含 `core-site` 组件的当前配置。

## <a name="example-update-configuration"></a>示例：更新配置

1. 获取当前配置，即 Ambari 存储为“所需配置”的配置：

    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME?fields=Clusters/desired_configs"
    ```

    ```powershell
    Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
        -Credential $creds
    ```

    此示例将返回一个 JSON 文档，其中包含群集上安装的组件的当前配置（由 *tag* 值标识）。 以下示例是从 Spark 群集类型返回的数据摘录。
   
    ```json
    "spark-metrics-properties" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-fairscheduler" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    },
    "spark-thrift-sparkconf" : {
        "tag" : "INITIAL",
        "user" : "admin",
        "version" : 1
    }
    ```
   
    需要从此列表中复制组件的名称（例如，**spark\_thrift\_sparkconf** 和 **tag** 值）。

2. 使用以下命令检索组件和标记的配置：
   
    ```bash
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" \
    | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    ```powershell
    $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
    $now = Get-Date
    $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=spark-thrift-sparkconf&tag=INITIAL" `
        -Credential $creds
    $resp.Content | jq --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    ```

    > [!NOTE]
    > 将 **spark-thrift-sparkconf** 和 **INITIAL** 替换为要检索其配置的组件和标记。
   
    Jq 用来将从 HDInsight 检索到的数据转变为新的配置模板。 具体而言，这些示例执行以下操作：
   
    * 创建包含字符串“version”和日期并存储在 `newtag` 中的唯一值。

    * 为新的所需配置创建根文档。

    * 获取 `.items[]` 数组的内容，并将其添加在 **desired_config** 元素下。

    * 删除 `href`、`version` 和 `Config` 元素，因为提交新配置时不需要这些元素。

    * 添加一个值为 `version#################` 的 `tag` 元素。 数字部分基于当前日期。 每个配置必须有唯一的标记。
     
    最后，将数据保存到 `newconfig.json` 文档。 文档结构应该类似于以下示例：
     
     ```json
    {
        "Clusters": {
            "desired_config": {
            "tag": "version1459260185774265400",
            "type": "spark-thrift-sparkconf",
            "properties": {
                ....
            },
            "properties_attributes": {
                ....
            }
        }
    }
    ```

3. 打开 `newconfig.json` 文档并在 `properties` 对象中修改/添加值。 以下示例将 `"spark.yarn.am.memory"` 的值从 `"1g"` 更改为 `"3g"`。 它还添加了值为 `"256m"` 的 `"spark.kryoserializer.buffer.max"`。
   
        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",
   
    完成修改后，保存该文件。

4. 使用以下命令将更新的配置提交到 Ambari。
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```
   
    这些命令将 **newconfig.json** 文件的内容作为新的所需配置提交到群集。 请求将返回一个 JSON 文档。 此文档中的 versionTag 元素应该与提交的版本相匹配，并且 configs 对象包含你请求的配置更改。

### <a name="example-restart-a-service-component"></a>示例：重新启动服务组件

此时，如果查看 Ambari Web UI，会发现 Spark 服务指出需要将它重新启动才能使新配置生效。 使用以下步骤重新启动该服务。

1. 使用以下命令启用 Spark 服务的维护模式：

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```
   
    这些命令将向服务器发送用于开启维护模式的 JSON 文档。 可使用以下请求来验证服务当前是否处于维护模式：
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```
   
    返回值为 `ON`。

2. 接下来，使用以下命令关闭该服务：

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```
    
    响应类似于以下示例：
   
    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```
    
    > [!IMPORTANT]
    > 此 URI 返回的 `href` 值正在使用群集节点的内部 IP 地址。 要从群集外部使用该地址，请将“10.0.0.18:8080”部分替换为群集的 FQDN。 
    
    以下命令检索请求状态：

    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    响应 `COMPLETED` 表示请求已完成。

3. 完成前一个请求后，请使用以下命令来启动服务。
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```
   
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```
    该服务现在将使用新配置。

4. 最后，使用以下命令关闭维护模式。
   
    ```bash
    curl -u admin -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/SPARK"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>后续步骤

有关 REST API 的完整参考，请参阅 [Ambari API 参考 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

