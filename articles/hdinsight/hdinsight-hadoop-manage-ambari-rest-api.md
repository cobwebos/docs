---
title: 使用 Ambari REST API 监视和管理 Hadoop - Azure HDInsight
description: 了解如何使用 Ambari 监视和管理 Azure HDInsight 中的 Hadoop 群集。 本文档介绍如何使用 HDInsight 群集随附的 Ambari REST API。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: hrasheed
ms.openlocfilehash: 37d4c4dbbe623d68edf1de5454d7752a81fc2bd2
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801872"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>使用 Apache Ambari REST API 管理 HDInsight 群集

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

了解如何使用 Apache Ambari REST API 管理和监视 Azure HDInsight 中的 Apache Hadoop 群集。

## <a id="whatis"></a>什么是 Apache Ambari
[Apache Ambari](https://ambari.apache.org)简化了管理和监视 Hadoop 群集的通过提供简单易用的 web UI 由其[REST Api](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。  基于 Linux 的 HDInsight 群集已按默认提供 Ambari。

## <a name="prerequisites"></a>系统必备
* **HDInsight 上的 Hadoop 群集**。 请参阅 [Linux 上的 HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

* **Bash on Ubuntu on Windows 10**。  在本文中的示例在 Windows 10 上使用 Bash shell。 请参阅[Linux 安装指南适用于 Windows 10 的 Windows 子系统](https://docs.microsoft.com/windows/wsl/install-win10)有关安装步骤。  其他[Unix shell](https://www.gnu.org/software/bash/)也将工作。  这些示例中，需进行一些细微的修改，可在 Windows 命令提示符处。  或者，可以使用 Windows PowerShell。

* **jq**，命令行的 JSON 处理器。  请参阅 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。

* **Windows PowerShell**。  或者，可以使用[Bash](https://www.gnu.org/software/bash/)。

## <a name="base-uri-for-ambari-rest-api"></a>用于 Ambari Rest API 的基 URI

 在 HDInsight 上 Ambari REST API 的基本 URI 是`https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`，其中`CLUSTERNAME`是群集的名称。  在 Uri 中的群集名称**区分大小写**。  URI 的完全限定域名 (FQDN) 部分 (CLUSTERNAME.azurehdinsight.net) 中的群集名称不区分大小写，但 URI 中的其他部分则区分大小写。

## <a name="authentication"></a>认证

连接到 HDInsight 上的 Ambari 需要 HTTPS。 使用在群集创建过程中提供的管理员帐户名称（默认值是 **admin**）和密码。

## <a name="examples"></a>示例

### <a name="setup-preserve-credentials"></a>安装程序 （保留凭据）
保留你的凭据以避免重新输入它们对每个示例。  群集名称将保留在单独的步骤。

**A.Bash**  
通过替换来编辑以下脚本`PASSWORD`与实际密码。  然后输入命令。

```bash
export password='PASSWORD'
```  

**B.PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>标识正确大小写格式的群集名称
群集名称的实际大小写可能不同于预期具体取决于群集的创建方式。  此处的步骤将显示实际大小写，并将其存储在变量中以所有后续示例。

编辑以下替换脚本`CLUSTERNAME`群集名称。 然后输入命令。 （FQDN 的群集名称不区分大小写。）

**A.Bash**  

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>分析 JSON 数据

下面的示例使用[jq](https://stedolan.github.io/jq/)或[Convertfrom-json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json)若要分析 JSON 响应文档并仅显示`health_report`结果中的信息。

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```   

### <a name="example-get-the-fqdn-of-cluster-nodes"></a> 获取群集节点的 FQDN

使用 HDInsight 时，可能需要知道群集节点的完全限定域名 (FQDN)。 可使用以下示例轻松检索群集中各个节点的 FQDN：

**所有节点**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**头节点**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**辅助角色节点**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper 节点**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="example-get-the-internal-ip-address-of-cluster-nodes"></a> 获取群集节点的内部 IP 地址

本部分中的示例所返回的 IP 地址不可直接通过 Internet 进行访问。 仅可在包含 HDInsight 群集的 Azure 虚拟网络内部对其进行访问。

有关将 HDInsight 与虚拟网络配合使用的详细信息，请参阅[使用 Azure 虚拟网络扩展 HDInsight 功能](hdinsight-extend-hadoop-virtual-network.md)。

要查找 IP 地址，必须知道群集节点的内部完全限定的域名 (FQDN)。 拥有 FQDN 后即可获取主机的 IP 地址。 下面的示例首先会向 Ambari 查询所有主机节点的 FQDN，再向 Ambari 查询每个主机的 IP 地址。

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp 
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>获取默认存储

创建 HDInsight 群集时，必须使用 Azure 存储帐户或 Data Lake Storage 作为群集的默认存储。 创建群集后，可以使用 Ambari 来检索此信息。 例如，如果要从 HDInsight 外部的容器读取数据或向其写入数据。

以下示例检索群集的默认存储配置：

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> 这些示例将返回应用于服务器的第一个配置 (`service_config_version=1`)，其中包含此信息。 如果要检索创建群集后修改的值，可能需要列出配置版本并检索最新版本。

返回值类似于以下其中一个示例：

* `wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - 此值表示群集使用 Azure 存储帐户作为默认存储。 值 `ACCOUNTNAME` 是存储帐户的名称。 `CONTAINER` 部分是存储帐户中 Blob 容器的名称。 容器是群集的 HDFS 兼容存储的根。

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` - 此值表示群集使用 Azure Data Lake Storage Gen2 作为默认存储。 `ACCOUNTNAME` 和 `CONTAINER` 值对于前面提到的 Azure 存储而言意义相同。

* `adl://home` - 此值表示群集使用 Azure Data Lake Storage Gen1 作为默认存储。

    若要查找 Data Lake Storage 帐户名称，请使用以下示例：

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    返回值类似于 `ACCOUNTNAME.azuredatalakestore.net`，其中，`ACCOUNTNAME` 是 Data Lake Storage 帐户的名称。

    若要查找 Data Lake Storage 中包含群集存储的目录，请使用以下示例：

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    返回值类似于 `/clusters/CLUSTERNAME/`。 此值是 Data Lake Storage 帐户中的一个路径。 此路径是群集的 HDFS 兼容文件系统的根目录。  

> [!NOTE]  
> [Get AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster)提供的 cmdlet [Azure PowerShell](/powershell/azure/overview)也会返回群集的存储信息。


### <a name="get-all-configurations"></a> 获取所有配置

获取可用于群集的配置。

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

此示例将返回一个 JSON 文档，其中包含群集上安装的组件的当前配置（由 *tag* 值标识）。 下面的示例是从 Spark 群集类型返回的数据摘录。
   
```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>获取有关特定组件的配置

获取你感兴趣的组件的配置。 在以下示例中，将 `INITIAL` 替换为从上一请求返回的标记值。

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

此示例返回的 JSON 文档包含 `livy2-conf` 组件的当前配置。

### <a name="update-configuration"></a>更新配置

1. 创建 `newconfig.json`。  
   修改，并输入以下命令：

   * 替换为`livy2-conf`与所需的组件。
   * 替换`INITIAL`与实际值为检索`tag`从[获取所有配置](#get-all-configurations)。

     **A.Bash**  
     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json

     ```

     **B. powershell**  
     PowerShell 脚本使用[jq](https://stedolan.github.io/jq/)。  编辑`C:\HD\jq\jq-win64`下面，以反映你的实际路径和版本[jq](https://stedolan.github.io/jq/)。

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq 用于将从 HDInsight 中检索的数据转换成新的配置模板。 具体而言，这些示例会执行以下操作：

   * 创建一个包含字符串“version”和日期并存储在 `newtag`中的唯一值。

   * 为新的所需配置创建根文档。

   * 获取 `.items[]` 数组的内容，并将其添加在 **desired_config** 元素下。

   * 删除 `href`、`version` 和 `Config` 元素，因为提交新配置时不需要这些元素。

   * 添加一个值为 `version#################` 的 `tag` 元素。 数字部分基于当前日期。 每个配置必须有唯一的标记。

     最后，数据将保存到 `newconfig.json` 文档。 该文档结构类似于下面的示例：

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. 编辑`newconfig.json`。  
   打开 `newconfig.json` 文档并在 `properties` 对象中修改/添加值。 以下示例将 `"livy.server.csrf_protection.enabled"` 的值从 `"true"` 更改为 `"false"`。

        "livy.server.csrf_protection.enabled": "false",

    完成修改后，保存该文件。

3. 提交`newconfig.json`。  
   使用以下命令将更新的配置提交到 Ambari。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    这些命令会将 **newconfig.json** 文件的内容提交到群集作为所需的新配置。 该请求会返回一个 JSON 文档。 此文档中的 **versionTag** 元素应该与提交的版本相匹配，并且 **configs** 对象包含你请求的配置更改。

### <a name="restart-a-service-component"></a>重启服务组件

此时，如果查看 Ambari Web UI，会发现 Spark 服务指出需要将它重新启动才能使新配置生效。 使用以下步骤重新启动该服务。

1. 使用以下命令启用 Spark2 服务的维护模式：

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    $resp.Content
    ```

2. 验证维护模式  

    这些命令将 JSON 文档发送到启用了维护模式的服务器。 可以使用以下请求来验证服务当前是否处于维护模式：

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    返回值为 `ON`。

3. 接下来，使用以下命令关闭 Spark2 服务：

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://girouxSpark.azurehdinsight.net/api/v1/clusters/girouxspark/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    其响应类似于如下示例：

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
    > 值 `href` 值正在使用群集节点的内部 IP 地址。 若要从群集外部使用该地址，请将“10.0.0.18:8080”部分替换为群集的 FQDN。  

4. 验证请求。  
    通过替换来编辑下面的命令`29`使用的实际值`id`返回从上一步。  以下命令检索请求的状态：

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    响应 `COMPLETED` 指示请求已完成。

5. 上一个请求完成后，使用以下命令启动 Spark2 服务。
   
    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    ```

    服务现在正在使用新配置。

6. 最后，使用以下命令关闭维护模式。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'

    ```

## <a name="next-steps"></a>后续步骤

有关 REST API 的完整参考，请参阅 [Apache Ambari API 参考 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

