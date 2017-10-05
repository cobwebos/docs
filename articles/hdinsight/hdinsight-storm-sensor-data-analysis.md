---
title: "使用 Apache Storm 和 HBase 分析传感器数据 | Microsoft Docs"
description: "了解如何使用虚拟网络连接到 Apache Storm。 了解如何使用 Storm 和 HBase 处理来自 Azure 事件中心的传感器数据，并使用 D3.js 来可视化这些数据。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/09/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 760543dc3880cb0dbe14070055b528b94cffd36b
ms.openlocfilehash: 0d1cc959c87bd64ed728f8b56c9b9156fa492a8b
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>使用 Apache Storm、事件中心和 HDInsight 中的 HBase (Hadoop) 分析传感器数据

了解如何使用 Apache Storm on HDInsight 处理来自 Azure 事件中心的传感器数据。 数据随后存储到 Apache HBase on HDInsight，并使用 D3.js 进行可视化。

本文档中所使用的 Azure Resource Manager 模板演示如何在资源组中创建多个 Azure 资源。 模板创建一个 Azure 虚拟网络、两个 HDInsight 群集（Storm 和 HBase）和一个 Azure Web 应用。 将实时 Web 仪表板的 node.js 实现自动部署到 Web 应用。

> [!NOTE]
> 本文档中的信息和示例演示需要安装 HDInsight 3.6。
>
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。
* [Node.js](http://nodejs.org/)：用于从本地预览开发环境上的 Web 仪表板。
* [Java 和 JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)：用于开发 Storm 拓扑。
* [Maven](http://maven.apache.org/what-is-maven.html)：用于生成和编译项目。
* [Git](http://git-scm.com/)：用于从 GitHub 中下载项目。
* **SSH** 客户端：用于连接到基于 Linux 的 HDInsight 群集。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。


> [!IMPORTANT]
> 无需现有的 HDInsight 群集。 本文档中的步骤创建以下资源：
> 
> * Azure 虚拟网络
> * 一个 Storm on HDInsight 群集（基于 Linux，2 个工作节点）
> * 一个 HBase on HDInsight 群集（基于 Linux，2 个工作节点）
> * 承载 Web 仪表板的 Azure Web 应用

## <a name="architecture"></a>体系结构

![体系结构示意图](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

本示例包括以下组成部分：

* **Azure 事件中心**：包含从传感器收集的数据。
* **Storm on HDInsight**：用于实时处理来自事件中心的数据。
* **HBase on HDInsight**：Storm 处理后，为数据提供永久性的 NoSQL 数据存储区。
* **Azure 虚拟网络服务**：在 Storm on HDInsight 群集和 HBase on HDInsight 群集之间启用安全通信。
  
  > [!NOTE]
  > 使用 Java HBase 客户端 API 时，虚拟网络是必需的。 此虚拟网络不是通过 HBase 群集的公共网关进行公开。 通过将 HBase 和 Storm 群集安装到同一个虚拟网络，Storm 群集（或虚拟网络上的任何其他系统）便能够直接访问使用客户端 API 的 HBase。

* **仪表板网站**：实时绘制数据图表的示例仪表板。
  
  * 网站是以 Node.js 实现的。
  * [Socket.io](http://socket.io/) 用于 Storm 拓扑和网站之间的实时通信。
    
    > [!NOTE]
    > 使用 Socket.io 进行通信是一个实现细节。 可以使用任何通信框架，例如原始 WebSockets 或 SignalR。

  * [D3.js](http://d3js.org/) 用于绘制发送到网站的数据的图表。

> [!IMPORTANT]
> 需要两个群集，因为没有方法支持为 Storm 和 HBase 创建同一个 HDInsight 群集。

拓扑使用 [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) 类从事件中心读取数据，并使用 [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/releases/1.0.1/javadocs/org/apache/storm/hbase/bolt/HBaseBolt.html) 类将数据写入到 HBase。 与网站的通信可通过使用 [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java) 来实现。

下图说明拓扑的布局：

![拓扑图示意图](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> 该图是一个简化的拓扑视图。 为事件中心中的每个分区创建每个组件的实例。 这些实例分布在群集中，节点和数据在它们之间路由，如下所示：
> 
> * 从 spout 到分析器的数据已经过负载均衡。
> * 从分析器到仪表板和 HBase 的数据按设备 ID 进行分组，因此，来自同一设备的消息始终流向同一组件。

### <a name="topology-components"></a>拓扑组件

* **事件中心 Spout**：提供 Spout 作为 Apache Storm 版本 0.10.0 和更高版本的一部分。
  
  > [!NOTE]
  > 此示例中使用的事件中心 Spout 需要 Storm on HDInsight 群集版本 3.5 或 3.6。

* **ParserBolt.java**：spout 发出的数据是原始的 JSON，有时每次会发出多个事件。 此 Bolt 读取 Spout 发出的数据并解析 JSON 消息。 Bolt 随后以包含多个字段的元组发出数据。
* **DashboardBolt.java**：此组件演示如何使用 Java 的 Socket.io 客户端库将数据实时发送到 Web 仪表板。
* **no-hbase.yaml**：在本地模式下运行时使用的拓扑定义。 它不使用 HBase 组件。
* **with-hbase.yaml**：在群集上运行拓扑时使用的拓扑定义。 它使用 HBase 组件。
* **dev.properties**：事件中心 Spout、HBase Bolt 和仪表板组件的配置信息。

## <a name="prepare-your-environment"></a>准备环境

在使用本示例之前，必须创建 Storm 拓扑从中读取的 Azure 事件中心。

### <a name="configure-event-hub"></a>配置事件中心

事件中心是此示例的数据源。 按照下列步骤创建一个事件中心。

1. 从 [Azure 门户](https://portal.azure.com)选择“+ 新建” -> “物联网” -> “事件中心”。
2. 在“创建命名空间”部分中，执行以下任务：
   
   1. 输入命名空间的**名称**。
   2. 选择定价层。 “基本”对于本示例来说已足够。
   3. 选择要使用的 Azure **订阅**。
   4. 选择现有的 Azure 资源组或创建新的资源组。
   5. 选择事件中心的“位置”。
   6. 选择“固定到仪表板”，并单击“创建”。

3. 完成创建后，会显示命名空间的事件中心信息。 从此处选择“+ 添加事件中心”。 在“创建事件中心”部分中输入 sensordata 的名称，并选择“创建”。 将其他字段保留默认值。
4. 从命名空间的“事件中心”视图，选择“事件中心”。 选择“sensordata”条目。
5. 从 sensordata 事件中心，选择“共享访问策略”。 使用“+ 添加”链接添加以下策略：

    | 策略名称 | 声明 |
    | ----- | ----- |
    | devices | 发送 |
    | storm | 侦听 |

1. 选择这两种策略，并记下“主密钥”值。 在以后的步骤中需要用到这两种策略的值。

## <a name="download-and-configure-the-project"></a>下载并配置项目

使用以下命令从 GitHub 中下载项目。

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

命令执行完毕后，可得到以下目录结构：

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal.
                no-hbase.yaml - topology definition without hbase components.
                with-hbase.yaml - topology definition with hbase components.
            src/main/java/com/microsoft/examples/bolts/
                ParserBolt.java - parses JSON data into tuples
                DashboardBolt.java - sends data over Socket.IO to the web dashboard.
        dashboard/nodejs/ - this is the node.js web dashboard.
        SendEvents/ - utilities to send fake sensor data.

> [!NOTE]
> 本文档不会深入介绍本示例中所包含代码的完整详细信息。 但是，已对代码进行全面注释。

要将项目配置为从事件中心读取，请打开 `hdinsight-eventhub-example/TemperatureMonitor/dev.properties` 文件，并将事件中心信息添加到以下行：

```bash
eventhub.read.policy.name: your_read_policy_name
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: your_event_hub_name
eventhub.partitions: 2
```

## <a name="compile-and-test-locally"></a>编译并在本地测试

> [!IMPORTANT]
> 在本地使用拓扑需要工作的 Storm 开发环境。 有关详细信息，请参阅 Apache.org 上的 [Setting up a Storm development environment](http://storm.apache.org/releases/1.1.0/Setting-up-development-environment.html)（设置 Storm 开发环境）。

> [!WARNING]
> 如果使用 Windows 开发环境，则在本地运行拓扑时可能会收到 `java.io.IOException`。 如果是这样，请改为在 HDInsight 上运行拓扑。

测试之前，必须启动仪表板以查看拓扑的输出，并生成要在事件中心中存储的数据。

> [!IMPORTANT]
> 执行本地测试时，此拓扑的 HBase 组件未处于活动状态。 从包含群集的 Azure 虚拟网络外部无法访问 HBase 群集的 Java API。

### <a name="start-the-web-application"></a>启动 Web 应用程序

1. 打开命令提示符并将目录更改为 `hdinsight-eventhub-example/dashboard`。 使用以下命令安装 Web 应用程序所需的依赖项：
   
    ```bash
    npm install
    ```

2. 使用以下命令启动 Web 应用程序：
   
    ```bash
    node server.js
    ```
   
    将看到类似于以下文本的消息：
   
        Server listening at port 3000

3. 打开 Web 浏览器并输入 `http://localhost:3000/` 作为地址。 会显示类似于下图的页面：
   
    ![Web 仪表板](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    将此命令提示符保持打开状态。 测试完成后，使用 Ctrl-C 停止 Web 服务器。

### <a name="generate-data"></a>生成数据

> [!NOTE]
> 本部分中的步骤使用 Node.js，以便它们可以在任何平台上使用。 有关其他语言示例，请参阅 `SendEvents` 目录。

1. 打开新提示符、shell 或终端，并将目录更改为 `hdinsight-eventhub-example/SendEvents/nodejs`。 若要安装应用程序所需的依赖项，请使用以下命令：

    ```bash
    npm install
    ```

2. 在文本编辑器中打开 `app.js` 文件，并添加之前获取的事件中心信息：
   
    ```javascript
    // ServiceBus Namespace
    var namespace = 'YourNamespace';
    // Event Hub Name
    var hubname ='sensordata';
    // Shared access Policy name and key (from Event Hub configuration)
    var my_key_name = 'devices';
    var my_key = 'YourKey';
    ```
   
   > [!NOTE]
   > 此示例假定已使用 `sensordata` 作为事件中心的名称。 且使用 `devices` 作为具有 `Send` 声明的策略的名称。

3. 使用以下命令在事件中心插入新条目：
   
    ```bash
    node app.js
    ```
   
    可看到包含发送到事件中心的数据的多个输出行：
   
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
        {"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### <a name="build-and-start-the-topology"></a>构建并启动拓扑

1. 打开新命令提示符并将目录更改为 `hdinsight-eventhub-example/TemperatureMonitor`。 若要生成和打包拓扑，请使用以下命令： 

    ```bash
    mvn clean package
    ```

2. 若要在本地模式下启动拓扑，请使用以下命令：

    ```bash
    storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local --filter dev.properties resources/no-hbase.yaml
    ```

    * `--local`，在本地模式下启动拓扑。
    * `--filter`，使用 `dev.properties` 文件填充拓扑定义中的参数。
    * `resources/no-hbase.yaml`，使用 `no-hbase.yaml` 拓扑定义。
 
   启动后，拓扑从事件中心读取条目，并将其发送到在本地计算机上运行的仪表板。 可看到 Web 仪表板中显示一些行，如下图所示：
   
    ![包含数据的仪表板](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. 仪表板正在运行时，使用前面步骤中的 `node app.js` 命令将新数据发送到事件中心。 由于温度值是随机生成的，因此图表会更新以显示温度的大幅变化。
   
   > [!NOTE]
   > 使用 `node app.js` 命令时必须位于 **hdinsight-eventhub-example/SendEvents/Nodejs** 目录中。

3. 验证仪表板是否更新后，使用 Ctrl+C 停止拓扑。 可使用 Ctrl+C 来停止本地 Web 服务器。

## <a name="create-a-storm-and-hbase-cluster"></a>创建 Storm 和 HBase 群集

本节中的步骤使用 [Azure 资源管理器模板](../azure-resource-manager/resource-group-template-deploy.md)创建 Azure 虚拟网络以及虚拟网络上的 Storm 和 HBase 群集。 该模板还会创建 Azure Web 应用并将仪表板的副本部署到其中。

> [!NOTE]
> 使用虚拟网络，以便在 Storm 群集上运行的拓扑可以直接与使用 HBase Java API 的 HBase 群集进行通信。

本文档使用的资源管理器模板位于公共 blob 容器中（网址：https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet-3.6.json）。

1. 单击以下按钮以登录到 Azure，并在 Azure 门户中打开 Resource Manager 模板。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.6.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 在“自定义部署”部分中，输入以下值：
   
    ![HDInsight 参数](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
   
   * **基群集名称**：此值用作 Storm 和 HBase 群集的基名称。 例如，输入 **abc** 创建名为 **storm-abc** 的 Storm 群集和名为 **hbase-abc** 的 HBase 群集。
   * **群集登录用户名**：Storm 和 HBase 群集的管理员用户名。
   * **群集登录密码**：Storm 和 HBase 群集的管理员用户密码。
   * **SSH 用户名**：创建 Storm 和 HBase 群集的 SSH 用户。
   * **SSH 密码**：Storm 和 HBase 群集的 SSH 用户密码。
   * **位置**：在其中创建群集的区域。
     
     单击“确定”保存参数。

3. 使用“基本”部分创建资源组或选择现有资源组。
4. 在“资源组位置”下拉菜单中，选择与在“设置”部分为 **LOCATION** 参数所选的位置相同的位置。
5. 阅读条款和条件，并选择“我同意上述条款和条件”。
6. 最后，选中“固定到仪表板”，并选择“购买”。 创建群集大约需要 20 分钟时间。

创建资源后，会显示资源组的信息。

![VNet 和群集的资源组](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> 请注意，HDInsight 群集的名称为 **storm BASENAME** 和 **hbase BASENAME**，其中 BASENAME 是为模板提供的名称。 在连接到群集的后续步骤中，会用到这些名称。 此外请注意，仪表板站点的名称是 **basename-dashboard**。 稍后会在本文档中使用此值。

## <a name="configure-the-dashboard-bolt"></a>配置仪表板 bolt

为了将数据发送到部署为 Web 应用的仪表板，必须在 `dev.properties` 文件中修改以下行：

```yaml
dashboard.uri: http://localhost:3000
```

将 `http://localhost:3000` 更改为 `http://BASENAME-dashboard.azurewebsites.net` 并保存该文件。 用上一步中提供的基名称替换 **BASENAME**。 此外可以使用之前创建的资源组，以便选择仪表板并查看 URL。

## <a name="create-the-hbase-table"></a>创建 HBase 表

若要在 HBase 中存储数据，首先必须创建一个表。 预先创建 Storm 需要写入的资源，因为尝试从 Storm 拓扑内部创建资源可能导致多个实例尝试创建相同的资源。 在拓扑外部创建资源，将 Storm 用于读/写和分析。

1. 利用在创建群集过程中向模板提供的 SSH 用户名和密码，使用 SSH 连接到 HBase 群集。 例如，如果使用 `ssh` 命令连接，请使用以下语法：
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```
   
    将 `sshuser` 替换为创建群集时提供的 SSH 用户名。 将 `clustername` 替换为 HBase 群集名称。

2. 从 SSH 会话中，启动 HBase 外壳程序。
   
    ```bash
    hbase shell
    ```
   
    加载外壳程序后，出现 `hbase(main):001:0>` 提示符。

3. 从 HBase Shell 中，输入以下命令以创建存储传感器数据的表。
   
    ```hbase
    create 'SensorData', 'cf'
    ```

4. 使用以下命令验证是否已创建该表：
   
    ```hbase
    scan 'SensorData'
    ```
   
    这会返回类似于以下示例的信息，指示表中有 0 行。
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. 输入 `exit` 退出 HBase shell：

## <a name="configure-the-hbase-bolt"></a>配置 HBase bolt

若要从 Storm 群集写入 HBase，必须向 HBase bolt 提供 HBase 群集的配置详细信息。

1. 使用以下示例之一检索 HBase 群集的 Zookeeper 仲裁：

    ```bash
    CLUSTERNAME='your_HDInsight_cluster_name'
    curl -u admin -sS -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/HBASE/components/HBASE_MASTER" | jq '.metrics.hbase.master.ZookeeperQuorum'
    ```

    > [!NOTE]
    > 将 `your_HDInsight_cluster_name` 替换为 HDInsight 群集的名称。 有关安装 `jq` 实用工具的详细信息，请参阅 [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/)。
    >
    > 出现提示时，输入 HDInsight 管理员登录名的密码。

    ```powershell
    $clusterName = 'your_HDInsight_cluster_name'
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HBASE/components/HBASE_MASTER" -Credential $creds
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.metrics.hbase.master.ZookeeperQuorum
    ```

    > [!NOTE]
    > 将 `your_HDInsight_cluster_name 替换为 HDInsight 群集的名称。 出现提示时，输入 HDInsight 管理员登录名的密码。
    >
    > 本示例需要 Azure PowerShell。 有关使用 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/en-us/powershell/scripting/Getting-Started-with-Windows-PowerShell?view=powershell-6)

    这些示例返回的信息类似于以下文本：

    `zk2-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk0-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181,zk3-hbase.mf0yeg255m4ubit1auvj1tutvh.ex.internal.cloudapp.net:2181`

    Storm 使用此信息与 HBase 群集进行通信。

2. 修改 `dev.properties` 文件，并将 Zookeeper 仲裁信息添加到以下行：

    ```yaml
    hbase.zookeeper.quorum: your_hbase_quorum
    ```

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>生成解决方案，并将其打包和部署到 HDInsight

在开发环境中，按照以下步骤将 Storm 拓扑部署到 Storm 群集。

1. 从 `TemperatureMonitor` 目录中，使用以下命令来执行新的构建并从项目创建 JAR 包：
   
        mvn clean package
   
    此命令在项目的 `target` 目录中创建一个名为 `TemperatureMonitor-1.0-SNAPSHOT.jar in the ` 的文件。

2. 使用 scp 将 `TemperatureMonitor-1.0-SNAPSHOT.jar` 和 `dev.properties` 文件上传到 Storm 群集。 在下面的示例中，用创建群集时提供的 SSH 用户替换 `sshuser`，用 Storm 群集的名称替换 `clustername`。 出现提示时，请输入 SSH 用户名密码。
   
    ```bash
    scp target/TemperatureMonitor-1.0-SNAPSHOT.jar dev.properties sshuser@clustername-ssh.azurehdinsight.net:
    ```

   > [!NOTE]
   > 上传文件可能需要数分钟的时间。

    有关将 `scp` 和 `ssh` 命令与 HDInsight 配合使用的详细信息，请参阅[将 SSH 与 HDInsight 配合使用](./hdinsight-hadoop-linux-use-ssh-unix.md)

3. 文件上传后，使用 SSH 连接到 Storm 群集。
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    将 `sshuser` 替换为 SSH 用户名。 将 `clustername` 替换为 Storm 群集名称。

4. 若要启动拓扑，请在 SSH 会话中使用以下命令：
   
    ```bash
    storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote --filter dev.properties -R /with-hbase.yaml
    ```

    * `--remote`，将拓扑提交到 Nimbus 服务，后者将拓扑分发到群集中的主管节点。
    * `--filter`，使用 `dev.properties` 文件填充拓扑定义中的参数。
    * `-R /with-hbase.yaml`，使用包中包含的 `with-hbase.yaml` 拓扑。

5. 启动拓扑后，打开浏览器到 Azure 发布的网站，然后使用 `node app.js` 命令将数据发送到事件中心。 应该看到 Web 仪表板更新以显示信息。
   
    ![仪表板](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>查看 HBase 数据

使用以下步骤连接到 HBase 并确认数据已写入表：

1. 使用 SSH 连接到 HBase 群集。
   
    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    将 `sshuser` 替换为 SSH 用户名。 将 `clustername` 替换为 HBase 群集名称。

2. 从 SSH 会话中，启动 HBase 外壳程序。
   
    ```bash
    hbase shell
    ```
   
    加载外壳程序后，出现 `hbase(main):001:0>` 提示符。

3. 从表中查看行：
   
    ```hbase
    scan 'SensorData'
    ```
   
    此命令返回类似于以下文本的信息，该信息表明表中存在数据。
   
        hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds
   
   > [!NOTE]
   > 此扫描操作最多从表中返回 10 行。

## <a name="delete-your-clusters"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

若要一次性删除群集、存储和 Web 应用，请删除包含它们的资源组。

## <a name="next-steps"></a>后续步骤

如需更多 HDInsight 的 Storm 拓扑示例，请参阅 [Storm on HDInsight 的示例拓扑](hdinsight-storm-example-topology.md)。

有关 Apache Storm 的详细信息，请参阅 [Apache Storm](https://storm.incubator.apache.org/) 站点。

有关 HBase on HDInsight 的详细信息，请参阅 [HDInsight 上的 HBase 概述](hdinsight-hbase-overview.md)。

有关 Socket.io 的详细信息，请参阅 [socket.io](http://socket.io/) 站点。

有关 D3.js 的详细信息，请参阅 [D3.js - 数据驱动文档](http://d3js.org/)。

有关以 Java 创建拓扑的信息，请参阅[为 Apache Storm on HDInsight 开发 Java 拓扑](hdinsight-storm-develop-java-topology.md)。

有关以 .NET 创建拓扑的信息，请参阅[使用 Visual Studio 为 Apache Storm on HDInsight 开发 C# 拓扑](hdinsight-storm-develop-csharp-visual-studio-topology.md)。

[azure-portal]: https://portal.azure.com

