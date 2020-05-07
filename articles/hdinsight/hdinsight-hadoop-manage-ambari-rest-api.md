---
title: 使用 Ambari REST API 监视和管理 Hadoop - Azure HDInsight
description: 了解如何使用 Ambari 监视和管理 Azure HDInsight 中的 Hadoop 群集。 在本文档中，你将了解如何使用 HDInsight 群集随附的 Ambari REST API。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 48602cb65430bcf6720b4d6f4ba05c771a7bd55b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559956"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>使用 Apache Ambari REST API 管理 HDInsight 群集

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

了解如何使用 Apache Ambari REST API 管理和监视 Azure HDInsight 中的 Apache Hadoop 群集。

## <a name="what-is-apache-ambari"></a>什么是 Apache Ambari

Apache Ambari 提供基于 [REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) 的简单易用型 Web UI 来简化 Hadoop 群集的管理和监视。  基于 Linux 的 HDInsight 群集已按默认提供 Ambari。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Hadoop 群集。 请参阅 [Linux 上的 HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。

* Windows 10 上的 Bash on Ubuntu。  本文中的示例使用 Windows 10 上的 Bash shell。 有关安装步骤，请参阅[适用于 Linux 的 Windows 子系统安装指南 - Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)。  也可以使用其他 [Unix shell](https://www.gnu.org/software/bash/)。  这些示例在经过轻微的修改后，可在 Windows 命令提示符下运行。  或者，你可以使用 Windows PowerShell。

* jq，一个命令行 JSON 处理程序。  请参阅 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。

* Windows PowerShell。  或者，可以使用 Bash。

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Ambari Rest API 的基本统一资源标识符

 HDInsight 上 Ambari REST API 的基本统一资源标识符 (URI) 为 `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`，其中 `CLUSTERNAME` 是群集的名称。  URI 中的群集名称**区分大小写**。  虽然 URI (`CLUSTERNAME.azurehdinsight.net`) 的完全限定域名 (FQDN) 部分中的群集名称不区分大小写，但 URI 中的其他部分是区分大小写的。

## <a name="authentication"></a>身份验证

连接到 HDInsight 上的 Ambari 需要 HTTPS。 使用在群集创建过程中提供的管理员帐户名称（默认值是 **admin**）和密码。

对于企业安全性套餐群集（而不`admin`是），请使用完全限定`username@domain.onmicrosoft.com`的用户名（例如）。

## <a name="examples"></a>示例

### <a name="setup-preserve-credentials"></a>设置（保留凭据）

请保留凭据，以免在每个示例中重复输入。  群集名称将在单独的步骤中保留。

**答： Bash**  
编辑以下脚本，将 `PASSWORD` 替换为实际密码。  然后输入该命令。

```bash
export password='PASSWORD'
```  

**B. PowerShell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>识别大小写正确的群集名称

群集名称的实际大小写可能与预期的不同。  此处的步骤将显示实际的大小写，并将其存储在一个变量中以供以后使用。

编辑以下脚本，将 `CLUSTERNAME` 替换为群集名称。 然后输入该命令。 （FQDN 的群集名称不区分大小写。）

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

### <a name="parsing-json-data"></a>解析 JSON 数据

以下示例使用 [jq](https://stedolan.github.io/jq/) 或 [ConvertFrom-Json](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/convertfrom-json) 来分析 JSON 响应文档并仅显示结果中的 `health_report` 信息。

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

### <a name="get-the-fqdn-of-cluster-nodes"></a>获取群集节点的 FQDN

可能需要知道群集节点的完全限定的域名（FQDN）。 可以使用以下示例轻松检索群集中各个节点的 FQDN：

**所有节点**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
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
| jq -r '.host_components[].HostRoles.host_name'
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
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>获取群集节点的内部 IP 地址

本部分中的示例返回的 IP 地址不能通过 internet 直接访问。 只能在包含 HDInsight 群集的 Azure 虚拟网络中访问它们。

有关使用 HDInsight 和虚拟网络的详细信息，请参阅[为 HDInsight 规划虚拟网络](hdinsight-plan-virtual-network-deployment.md)。

要查找 IP 地址，必须知道群集节点的内部完全限定的域名 (FQDN)。 在知悉 FQDN 后，可以获取主机的 IP 地址。 下面的示例首先查询 Ambari 中所有主机节点的 FQDN。 然后查询每个主机的 IP 地址 Ambari。

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

HDInsight 群集必须使用 Azure 存储帐户或 Data Lake Storage 作为默认存储。 创建群集后，可以使用 Ambari 来检索此信息。 例如，当希望从 HDInsight 外部的容器中读取数据或者将数据写入到其中时。

以下示例检索群集的默认存储配置：

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> 这些示例将返回应用于服务器的第一个配置 (`service_config_version=1`)，其中包含此信息。 如果要检索创建群集后修改的值，可能需要列出配置版本并检索最新版本。

返回值类似于下列示例之一：

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` - 此值表示群集使用 Azure 存储帐户作为默认存储。 `ACCOUNTNAME` 值是存储帐户的名称。 `CONTAINER` 部分是存储帐户中的 blob 容器的名称。 该容器是群集的 HDFS 兼容存储的根目录。

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` - 此值表示群集使用 Azure Data Lake Storage Gen2 作为默认存储。 `ACCOUNTNAME` 和 `CONTAINER` 值对于前面提到的 Azure 存储而言意义相同。

* `adl://home` - 此值表示群集使用 Azure Data Lake Storage Gen1 作为默认存储。

    若要查找 Data Lake Storage 帐户名称，请使用以下示例：

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
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
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    返回值类似于 `/clusters/CLUSTERNAME/`。 此值是 Data Lake Storage 帐户中的一个路径。 此路径是群集的 HDFS 兼容文件系统的根目录。  

> [!NOTE]  
> [Azure PowerShell](/powershell/azure/overview) 提供的 [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) cmdlet 也返回群集的存储信息。

### <a name="get-all-configurations"></a>获取所有配置

获取适用于群集的配置。

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

此示例将返回一个 JSON 文档，其中包含已安装组件的当前配置。 请参阅*标记*值。 以下示例是从 Spark 群集类型返回的数据摘录。

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

### <a name="get-configuration-for-specific-component"></a>获取特定组件的配置

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
   进行修改，然后输入以下命令：

   * 替换`livy2-conf`为新的组件。
   * 请将 `INITIAL` 替换为在[获取所有配置](#get-all-configurations)中检索到的 `tag` 实际值。

     **答： Bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. PowerShell**  
     PowerShell 脚本使用 [jq](https://stedolan.github.io/jq/)。  编辑以下 `C:\HD\jq\jq-win64`，以反映 [jq](https://stedolan.github.io/jq/) 的实际路径和版本。

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     Jq 用来将从 HDInsight 检索到的数据转变为新的配置模板。 具体而言，这些示例将执行以下操作：

   * 创建包含字符串“version”和日期并存储在 `newtag` 中的唯一值。

   * 为新配置创建根文档。

   * 获取 `.items[]` 数组的内容，并将其添加在 **desired_config** 元素下。

   * 删除 `href`、`version` 和 `Config` 元素，因为提交新配置时不需要这些元素。

   * 添加一个值为 `version#################` 的 `tag` 元素。 数字部分基于当前日期。 每个配置必须有唯一的标记。

     最后，将数据保存到 `newconfig.json` 文档。 文档结构应该类似于以下示例：

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

2. 编辑 `newconfig.json`。  
   打开 `newconfig.json` 文档并在 `properties` 对象中修改/添加值。 以下示例将 `"livy.server.csrf_protection.enabled"` 的值从 `"true"` 更改为 `"false"`。

        "livy.server.csrf_protection.enabled": "false",

    完成修改后，保存该文件。

3. 提交 `newconfig.json`。  
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

    这些命令将**newconfig.json**文件的内容作为新配置提交到群集。 请求将返回一个 JSON 文档。 此文档中的 versionTag 元素应该与提交的版本相匹配，并且 configs 对象包含你请求的配置更改********。

### <a name="restart-a-service-component"></a>重启服务组件

此时，Ambari web UI 指出需要重新启动 Spark 服务，新配置才能生效。 使用以下步骤重新启动该服务。

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
    ```

2. 验证维护模式  

    这些命令将向服务器发送用于开启维护模式的 JSON 文档。 可使用以下请求来验证服务当前是否处于维护模式：

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
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
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
    > 此 URI 返回的 `href` 值正在使用群集节点的内部 IP 地址。 若要从群集外部使用它，请将`10.0.0.18:8080`部分替换为群集的 FQDN。  

4. 验证请求。  
    编辑以下命令，将 `29` 替换为上一步骤返回的 `id` 实际值。  以下命令检索请求状态：

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

    响应 `COMPLETED` 表示请求已完成。

5. 完成前一个请求后，使用以下命令启动 Spark2 服务。

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    该服务现在将使用新配置。

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

有关 REST API 的完整参考，请参阅 [Apache Ambari API 参考 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。  另请参阅[为 Apache Ambari 视图授权用户](./hdinsight-authorize-users-to-ambari.md)
