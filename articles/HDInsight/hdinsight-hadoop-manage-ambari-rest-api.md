<!-- not suitable for Mooncake -->

<properties
   pageTitle="使用 Apache Ambari REST API 监视和管理 HDInsight 群集 | Azure"
   description="了解如何使用 Ambari 监视和管理基于 Linux 的 HDInsight 群集。在本文档中，你将学习如何使用 HDInsight 群集随附的 Ambari REST API。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.date="07/05/2016"
	wacn.date=""/>

# 使用 Ambari REST API 管理 HDInsight 群集

[AZURE.INCLUDE [ambari-selector](../includes/hdinsight-ambari-selector.md)]

Apache Ambari 提供简单易用的 Web UI 和 REST API 来简化 Hadoop 群集的管理和监视。基于 Linux 的 HDInsight 群集包含 Ambari，用于监视群集和进行配置更改。在本文档中，你将通过执行一些常见任务（例如，查找群集节点的完全限定域名或查找群集使用的默认存储帐户），来了解有关使用 Ambari REST API 的基本知识。

> [AZURE.NOTE] 本文中的信息仅适用于基于 Linux 的 HDInsight 群集。对于基于 Windows 的 HDInsight 群集，使用 Ambari REST API 只能获得一部分监视功能。请参阅[使用 Ambari API 监视 HDInsight 上的基于 Windows 的 Hadoop](/documentation/articles/hdinsight-monitor-use-ambari-api/)。

## 先决条件

* [cURL](http://curl.haxx.se/)：cURL 是一个跨平台实用工具，可用于从命令行操作 REST API。在本文档中，将使用它来与 Ambari REST API 通信。
* [jq](https://stedolan.github.io/jq/)：jq 是用于处理 JSON 文档的跨平台命令行实用工具。在本文档中，将使用它来分析从 Ambari REST API 返回的 JSON 文档。
* [Azure CLI](/documentation/articles/xplat-cli-install/)：用于操作 Azure 服务的跨平台命令行实用工具。

    [AZURE.INCLUDE [use-latest-version](../includes/hdinsight-use-latest-cli.md)]

## <a id="whatis"></a>什么是 Ambari？

[Apache Ambari](http://ambari.apache.org) 提供简单易用的 Web UI，让你可以预配、管理和监视 Hadoop 群集，以此简化 Hadoop 管理。开发人员可以使用 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) 在其应用程序中集成这些功能。

基于 Linux 的 HDInsight 群集已按默认提供 Ambari。

## REST API

HDInsight 上的 Ambari REST API 的基本 URI 为 https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME， 其中 __CLUSTERNAME__ 是群集的名称。

> [AZURE.IMPORTANT] URI 的完全限定域名 (FQDN) 部分 (CLUSTERNAME.azurehdinsight.cn) 中的群集名称不区分大小写，但 URI 中的其他部分则区分大小写。例如，如果群集名称为 MyCluster，则有效的 URI 如下：
>
> `https://mycluster.azurehdinsight.cn/api/v1/clusters/MyCluster`
> `https://MyCluster.azurehdinsight.cn/api/v1/clusters/MyCluster`
>
> 以下 URI 将返回错误，因为名称的第二个部分未正确使用大小写。
>
> `https://mycluster.azurehdinsight.cn/api/v1/clusters/mycluster`
> `https://MyCluster.azurehdinsight.cn/api/v1/clusters/mycluster`

连接到 HDInsight 上的 Ambari 需要 HTTPS。此外，必须使用管理员帐户名（默认为 __admin__）和创建群集时提供的密码在 Ambari 上进行身份验证。

下面是使用 cURL 对 REST API 发出 GET 请求的示例：

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME"
    
运行此命令时，请将 __PASSWORD__ 替换为群集的管理员密码，将 __CLUSTERNAME__ 替换为群集名称。然后，你将收到一个 JSON 文档，其开头如下所示：

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

由于这是一个 JSON 文档，因此使用 JSON 分析器来检索数据通常会更方便。例如，如果你要检索群集的运行状况信息，可以使用以下命令。

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report'
    
这将会检索 JSON 文档，然后将输出输送到 jq。`.Clusters.health_report` 指出要检索的 JSON 文档内的元素。

## 示例：获取群集节点的 FQDN

使用 HDInsight 时，可能需要知道群集节点的完全限定域名 (FQDN)。可以使用以下命令轻松检索群集中各个节点的 FQDN：

* __头节点__：`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __辅助角色节点__：`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Zookeeper 节点__：`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

请注意，其中每个部分均遵循相同的模式来查询已知在这些节点上运行的组件，然后检索 `host_name` 元素（包含这些节点的 FQDN）。

返回文档的 `host_components` 元素包含多个项。使用 `.host_components[]` 然后指定元素内的路径将循环访问每个项，并从特定的路径提取值。如果你只想指定一个值（例如第一个 FQDN 条目），可以集合形式返回项，然后选择特定的条目：

    jq '[.host_components[].HostRoles.host_name][0]'

这将返回集合中的第一个 FQDN。

## 示例：获取默认存储帐户和容器

创建 HDInsight 群集时，必须使用 Azure 存储帐户和 Blob 容器作为群集的默认存储。创建群集后，可以使用 Ambari 来检索此信息。例如，如果你要以编程方式将数据直接写入容器。

以下命令将检索群集默认存储的 WASB URI：

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE] 这将返回应用到服务器的第一个配置 (`service_config_version=1`)，其中包含此信息。如果要检索创建群集后修改的值，可能需要列出配置版本并检索最新版本。

这将返回值类似于下面的值，其中，__CONTAINER__ 是默认容器，__ACCOUNTNAME__ 是 Azure 存储帐户名：

    wasb://CONTAINER@ACCOUNTNAME.blob.core.chinacloudapi.cn

然后，可以在 [Azure CLI](/documentation/articles/xplat-cli-install/) 中上载数据或者从容器中下载数据。

1. 获取存储帐户的资源组。将 __ACCOUNTNAME__ 替换为从 Ambari 检索的存储帐户名：

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    这将返回帐户的资源组名称。
    
    > [AZURE.NOTE] 如果此命令未返回任何内容，则你需要将 Azure CLI 更改为 Azure Resource Manager 模式，然后重新运行命令。若要切换到 Azure Resource Manager 模式，请使用以下命令。
    >
    > `azure config mode arm`
    
2. 获取存储帐户的密钥。将 __GROUPNAME__ 替换为上一步骤中使用的资源组。将 __ACCOUNTNAME__ 替换为存储帐户名：

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    这将返回帐户的主密钥。
    
3. 使用 upload 命令将文件存储在容器中：

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    将 __ACCOUNTNAME__ 替换为存储帐户名。将 __ACCOUNTKEY__ 替换为前面检索到的密钥。__FILEPATH__ 是要上载的文件的路径，__BLOBPATH__ 是容器中的路径。

    例如，如果希望文件出现在 HDInsight 上的 wasb://example/data/filename.txt 中，则 __BLOBPATH__ 是 `example/data/filename.txt`。

## 示例：更新 Ambari 配置

1. 获取当前配置，即 Ambari 存储为“所需配置”的配置：

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME?fields=Clusters/desired_configs"
        
    这将返回一个 JSON 文档，其中包含群集上安装的组件的当前配置（由 _tag_ 值标识）。例如，下面是从 Spark 群集类型返回的数据摘录。
    
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

    需要从此列表中复制组件的名称（例如，__spark\_thrift\_sparkconf__ 和 __tag__ 值）。
    
2. 使用以下命令检索组件和标记的配置。将 __spark-thrift-sparkconf__ 和 __INITIAL__ 替换为你要检索其配置的组件和标记。

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/configurations?type=spark-thrift-sparkconf&tag=INITIAL" | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
    
    Curl 将检索 JSON 文档，然后系统将使用 jq 来做出一些修改，以创建可用于添加/修改配置值的模板。具体操作如下：
    
    * 创建包含字符串“version”和日期并存储在 __newtag__ 中的唯一值
    * 为新的所需配置创建根文档
    * 获取 .items 数组的内容，并将其添加在 __desired\_config__ 元素下。
    * 删除 __href__、__version__ 和 __Config__ 元素，因为提交新配置时不需要这些元素
    * 添加新的 __tag__ 元素，并将其值设置为 __version#################__，其中的数字部分基于当前日期。每个配置必须有唯一的标记。
    
    最后，将数据保存到 __newconfig.json__ 文档。文档结构类似于下面：
    
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

3. 打开 __newconfig.json__ 文档并在 __properties__ 对象中修改/添加值。例如，将 __"spark.yarn.am.memory"__ 的值从 __"1g"__ 更改为 __"3g"__，并针对值为 __"256m"__ 的 __"spark.kryoserializer.buffer.max"__ 添加新元素。

        "spark.yarn.am.memory": "3g",
        "spark.kyroserializer.buffer.max": "256m",

    完成修改后，保存该文件。

4. 使用以下命令将更新的配置提交到 Ambari。

        cat newconfig.json | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME"
        
    此命令将 __newconfig.json__ 文件的内容输送到 curl 请求，后者会将它作为新的所需配置提交到群集。随后将返回一个 JSON 文档。此文档中的 __versionTag__ 元素应该与提交的版本相匹配，__configs__ 对象将包含你请求的配置更改。

### 示例：重新启动服务组件

此时，如果你查看 Ambari Web UI，会发现 Spark 服务指出需要将它重新启动才能使新配置生效。使用以下步骤重新启动该服务。

1. 使用以下命令启用 Spark 服务的维护模式。

        echo '{"RequestInfo": {"context": "turning on maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/services/SPARK"

    这会将 JSON 文档发送到启用维护模式的服务器（包含在 `echo` 语句中）。
    可以使用以下请求来验证服务当前是否处于维护模式。
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/services/SPARK" | jq .ServiceInfo.maintenance_state
        
    此时将返回 `"ON"` 值。

3. 接下来，使用以下命令关闭服务。

        echo '{"RequestInfo": {"context" :"Stopping the Spark service"}, "Body": {"ServiceInfo": {"state": "INSTALLED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/services/SPARK"
        
    你将收到如下所示的响应。
    
        {
            "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
            "Requests" : {
                "id" : 29,
                "status" : "Accepted"
            }
        }
    
    此 URI 返回的 `href` 值正在使用群集节点的内部 IP 地址。若要从群集外部使用该地址，请将“10.0.0.18:8080”部分替换为群集的 FQDN。例如，以下命令将检索请求的状态。
    
        curl -u admin:PASSWORD -H "X-Requested-By: ambari" "https://CLUSTERNAME/api/v1/clusters/CLUSTERNAME/requests/29" | jq .Requests.request_status
    
    如果此值返回 `"COMPLETED"`，则表示请求已完成。

4. 完成前一个请求后，请使用以下命令来启动服务。

        echo '{"RequestInfo": {"context" :"Restarting the Spark service"}, "Body": {"ServiceInfo": {"state": "STARTED"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/services/SPARK"

    服务重新启动后，将使用新的配置设置。

5. 最后，使用以下命令关闭维护模式。

        echo '{"RequestInfo": {"context": "turning off maintenance mode for SPARK"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' | curl -u admin:PASSWORD -H "X-Requested-By: ambari" -X PUT -d "@-" "https://CLUSTERNAME.azurehdinsight.cn/api/v1/clusters/CLUSTERNAME/services/SPARK"

##后续步骤

有关 REST API 的完整参考，请参阅 [Ambari API 参考 V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)。

> [AZURE.NOTE] 已禁用某些 Ambari 功能，因为这些功能由 HDInsight 云服务管理；例如，从群集中添加或删除主机，或添加新服务。

<!---HONumber=Mooncake_0725_2016-->