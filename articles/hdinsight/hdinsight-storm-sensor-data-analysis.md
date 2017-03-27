---
title: "使用 Apache Storm 和 HBase 分析传感器数据 | Microsoft Docs"
description: "了解如何使用虚拟网络连接到 Apache Storm。 了解如何使用 Storm 和 HBase 处理来自 Azure 事件中心的传感器数据，然后使用 D3.js 来可视化这些数据。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a9a1ac8e-5708-4833-b965-e453815e671f
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 1289376a5a979b26537e5616c6e89a618f11040e
ms.lasthandoff: 03/03/2017


---
# <a name="analyze-sensor-data-with-apache-storm-event-hub-and-hbase-in-hdinsight-hadoop"></a>使用 Apache Storm、事件中心和 HDInsight 中的 HBase (Hadoop) 分析传感器数据
了解如何使用 Apache Storm on HDInsight 处理来自 Azure 事件中心的传感器数据。 数据随后存储到 Apache HBase on HDInsight，并使用 D3.js 进行可视化。

本文档中所使用的 Azure Resource Manager 模板演示如何在资源组中创建多个 Azure 资源。 模板创建一个 Azure 虚拟网络、两个 HDInsight 群集（Storm 和 HBase）和一个 Azure Web 应用。 将实时 Web 仪表板的 node.js 实现自动部署到 Web 应用。

> [!NOTE]
> 本文档中的信息和示例演示需要安装 HDInsight 3.5。
>
> Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

## <a name="prerequisites"></a>先决条件
* Azure 订阅。 请参阅 [获取 Azure 免费试用版](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
  
  > [!IMPORTANT]
  > 无需现有的 HDInsight 群集。 本文档中的步骤创建以下资源：
  > 
  > * Azure 虚拟网络
  > * 一个 Storm on HDInsight 群集（基于 Linux，2 个工作节点）
  > * 一个 HBase on HDInsight 群集（基于 Linux，2 个工作节点）
  > * 承载 Web 仪表板的 Azure Web 应用

* [Node.js](http://nodejs.org/)：用于从本地预览开发环境上的 Web 仪表板。
* [Java 和 JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)：用于开发 Storm 拓扑。
* [Maven](http://maven.apache.org/what-is-maven.html)：用于生成和编译项目。
* [Git](http://git-scm.com/)：用于从 GitHub 中下载项目。
* **SSH** 客户端：用于连接到基于 Linux 的 HDInsight 群集。 有关如何将 SSH 与 HDInsight 配合使用的详细信息，请参阅以下文档：
  
  * [在 Windows 客户端中将 SSH (PuTTY) 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)
  * [在 Linux、Unix、OS X 或 Windows 10 上的 Bash 中将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    > [!NOTE]
    > 另外，用户还必须有权限访问 `scp` 命令，该命令用于在本地开发环境与使用 SSH 的 HDInsight 群集之间复制文件。


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
  
  * 该网站在 Node.js 中实现，因此它可以在用于测试的任何客户端操作系统上运行，或者可以部署到 Azure 网站。
  * [Socket.io](http://socket.io/) 用于 Storm 拓扑和网站之间的实时通信。
    
    > [!NOTE]
    > 使用 Socket.io 进行通信是一个实现细节。 你可以使用任何通信框架，例如原始 WebSockets 或 SignalR。

  * [D3.js](http://d3js.org/) 用于绘制发送到网站的数据的图表。

> [!IMPORTANT]
> 需要两个群集，因为没有方法支持为 Storm 和 HBase 创建同一个 HDInsight 群集。
> 
> 

拓扑使用 [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) 类从事件中心读取数据，并使用 [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html) 类将数据写入到 HBase。 与网站的通信可通过使用 [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java) 来实现。

下面是拓扑图。

![拓扑图示意图](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [!NOTE]
> 这是一个简化的拓扑视图。 在运行时，每个组件的实例为每个分区创建事件中心所读取。 这些实例分布在群集中，节点和数据在它们之间路由，如下所示：
> 
> * 从 spout 到分析器的数据已经过负载平衡。
> * 从分析器到仪表板和 HBase 的数据按设备 ID 进行分组，因此，来自同一设备的消息始终流向同一组件。
> 
> 

### <a name="topology-components"></a>拓扑组件
* **EventHub Spout**：提供 spout 作为 Apache Storm 版本 0.10.0 和更高版本的一部分。
  
  > [!NOTE]
  > 此示例中使用的事件中心 spout 需要 Storm on HDInsight 群集版本 3.3 或 3.4。 有关如何通过旧版 Storm on HDInsight 使用事件中心的信息，请参阅[使用 Storm on HDInsight 处理 Azure 事件中心的事件](hdinsight-storm-develop-java-event-hub-topology.md)。
  > 
  > 
* **ParserBolt.java**：spout 发出的数据是原始的 JSON，有时每次会发出多个事件。 此 bolt 演示如何读取 spout 发出的数据，并将它作为包含多个字段的元组形式发送到新流。
* **DashboardBolt.java**：此组件演示如何使用 Java 的 Socket.io 客户端库将数据实时发送到 Web 仪表板。

本示例使用 [Flux](https://storm.apache.org/releases/0.10.0/flux.html) 框架，因此 YAML 文件中包含拓扑定义。 有两个文件：

* **no-hbase.yaml** - 在开发环境中测试拓扑时使用此文件。 因为不能从群集所在的虚拟网络外部访问 HBase Java API，因此该文件不使用 HBase 组件。
* **with-hbase.yaml** - 将拓扑部署到 Storm 群集时，使用此文件。 该文件在与 HBase 群集相同的虚拟网络中运行，所以使用 HBase 组件。

## <a name="prepare-your-environment"></a>准备环境
在使用本示例之前，必须创建 Storm 拓扑从中读取的 Azure 事件中心。

### <a name="configure-event-hub"></a>配置事件中心
事件中心是此示例的数据源。 按照下列步骤创建一个事件中心。

1. 从 [Azure 门户](https://portal.azure.com)选择“+ 新建” -> “物联网” -> “事件中心”。
2. 在“创建命名空间”边栏选项卡中，执行以下任务：
   
   1. 输入命名空间的**名称**。
   2. 选择定价层。 “基本”对于本示例来说已足够。
   3. 选择要使用的 Azure **订阅**。
   4. 选择现有的 Azure 资源组或创建新的资源组。
   5. 选择事件中心的“位置”。
   6. 选择“固定到仪表板”，然后单击“创建”。
3. 完成创建后，将显示命名空间的“事件中心”边栏选项卡。 从此处选择“+ 添加事件中心”。 在“创建事件中心”边栏选项卡上输入 **sensordata** 的名称，然后选择“创建”。 将其他字段保留默认值。
4. 从命名空间的“事件中心”边栏选项卡，选择“事件中心”。 选择“sensordata”条目。
5. 从 sensordata 事件中心的边栏选项卡，选择“共享访问策略”。 使用“+ 添加”链接添加以下策略：

    | 策略名称 | 声明 |
    | ----- | ----- |
    | devices | 发送 |
    | storm | 侦听 |

1. 选择这两种策略，然后记下“主密钥”值。 在以后的步骤中需要用到这两种策略的值。

## <a name="download-and-configure-the-project"></a>下载并配置项目
使用以下命令从 GitHub 中下载项目。

    git clone https://github.com/Blackmist/hdinsight-eventhub-example

命令执行完毕后，可得到以下目录结构：

    hdinsight-eventhub-example/
        TemperatureMonitor/ - this contains the topology
            resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
            src/ - the Java bolts
            dev.properties - contains configuration values for your environment
        dashboard/nodejs/ - this is the node.js web dashboard
        SendEvents/ - utilities to send fake sensor data

> [!NOTE]
> 本文档不会深入介绍本示例中包含的代码的完整详细信息。 但是，已对代码进行全面注释。

打开 **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** 文件并将事件中心信息添加到以下行：

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [!NOTE]
> 此示例假设使用 **storm** 作为具有 **Listen** 声明的策略的名称，并假设事件中心的名称为 **sensordata**。

在添加此信息后，请保存该文件。

## <a name="compile-and-test-locally"></a>编译并在本地测试
测试之前，必须启动仪表板以查看拓扑的输出，并生成要在事件中心中存储的数据。

> [!IMPORTANT]
> 执行本地测试时，此拓扑的 HBase 组件未激活，因为从 Azure 虚拟网络（包含群集）外部无法访问 HBase 群集的 Java API。


### <a name="start-the-web-application"></a>启动 Web 应用程序
1. 打开新的命令提示符或终端，并将目录更改为 **hdinsight-eventhub-example/dashboard**。 使用以下命令安装 Web 应用程序所需的依赖项：
   
        npm install
2. 使用以下命令启动 Web 应用程序：
   
        node server.js
   
    你应看到类似于下面的消息：
   
        Server listening at port 3000
3. 打开 Web 浏览器，并输入 **http://localhost:3000/**作为地址。 你应看到类似于下面的页面：
   
    ![Web 仪表板](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)
   
    将此命令提示符或终端保持打开状态。 测试完成后，使用 Ctrl-C 停止 Web 服务器。

### <a name="start-generating-data"></a>开始生成数据
> [!NOTE]
> 本部分中的步骤使用 Node.js，以便它们可以在任何平台上使用。 对于其他语言示例，请参阅 **SendEvents** 目录。

1. 打开新的命令提示符、外壳程序或终端，将目录切换为 **hdinsight-eventhub-example/SendEvents/nodejs**，然后使用以下命令安装应用程序所需的依赖项：
   
        npm install
2. 在文本编辑器中打开 **app.js** 文件，并添加之前获取的事件中心信息：
   
        // ServiceBus Namespace
        var namespace = 'YourNamespace';
        // Event Hub Name
        var hubname ='sensordata';
        // Shared access Policy name and key (from Event Hub configuration)
        var my_key_name = 'devices';
        var my_key = 'YourKey';
   
   > [!NOTE]
   > 此示例假设已使用 **sensordata** 作为事件中心的名称，并假设具有 **Send** 声明的策略的名称为 **devices**。

3. 使用以下命令在事件中心插入新条目：
   
        node app.js
   
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

### <a name="start-the-topology"></a>启动拓扑
1. 打开新的命令提示符、外壳程序或终端，将目录切换为 **hdinsight-eventhub-example/TemperatureMonitor**，然后使用以下命令启动拓扑：
   
        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
   
    如果使用的是 PowerShell，请使用以下命令：
   
        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"
   
   > [!NOTE]
   > 如果使用的是 Linux/Unix/OS X 系统，并且已[在开发环境中安装了 Storm](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html)，则可以改用以下命令：
   > 
   > `mvn compile package`
   > `storm jar target/TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml --filter dev.properties`
   
    此命令在本地模式下启动 **no-hbase.yaml** 文件中定义的拓扑。 **dev.properties** 文件包含的值为事件中心提供连接信息。 启动后，拓扑从事件中心读取条目，并将其发送到在本地计算机上运行的仪表板。 可看到 Web 仪表板中显示一些行，如下图所示：
   
    ![包含数据的仪表板](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

2. 仪表板正在运行时，使用前面步骤中的 `node app.js` 命令将新数据发送到事件中心。 由于温度值是随机生成的，因此图表会更新以显示温度的大幅变化。
   
   > [!NOTE]
   > 使用 `node app.js` 命令时必须位于 **hdinsight-eventhub-example/SendEvents/Nodejs** 目录中。

3. 验证仪表板是否更新后，使用 Ctrl+C 停止拓扑。 可使用 Ctrl+C 来停止本地 Web 服务器。

## <a name="create-a-storm-and-hbase-cluster"></a>创建 Storm 和 HBase 群集

本节中的步骤使用 [Azure Resource Manager 模板](../azure-resource-manager/resource-group-template-deploy.md)创建 Azure 虚拟网络以及虚拟网络上的 Storm 和 HBase 群集。 该模板还会创建 Azure Web 应用并将仪表板的副本部署到其中。

> [!NOTE]
> 使用虚拟网络，以便在 Storm 群集上运行的拓扑可以直接与使用 HBase Java API 的 HBase 群集进行通信。

本文档使用的 Resource Manager 模板位于 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json**.的公共 blob 容器上。

1. 单击以下按钮以登录到 Azure，然后在 Azure 门户中打开 Resource Manager 模板。
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet-3.5.json" target="_blank"><img src="./media/hdinsight-storm-sensor-data-analysis/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 在“自定义部署”边栏选项卡中输入以下值：
   
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
5. 阅读条款和条件，然后选择“我同意上述条款和条件”。
6. 最后，选中“固定到仪表板”，然后选择“购买”。 创建群集大约需要 20 分钟时间。

创建资源后，会重定向到包含群集和 Web 仪表板的资源组边栏选项卡。

![虚拟网络和群集的“资源组”边栏选项卡](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [!IMPORTANT]
> 请注意，HDInsight 群集的名称为 **storm BASENAME** 和 **hbase BASENAME**，其中 BASENAME 是为模板提供的名称。 在连接到群集的后续步骤中，会用到这些名称。 此外请注意，仪表板站点的名称是 **basename-dashboard**。 查看仪表板时会用到此名称。

## <a name="configure-the-dashboard-bolt"></a>配置仪表板 bolt

为了将数据发送到部署为 Web 应用的仪表板，必须在 **dev.properties** 文件中修改以下行：

    dashboard.uri: http://localhost:3000

将 `http://localhost:3000` 更改为 `http://BASENAME-dashboard.azurewebsites.net` 并保存该文件。 用上一步中提供的基名称替换 **BASENAME**。 此外可以使用之前创建的资源组，以便选择仪表板并查看 URL。

## <a name="create-the-hbase-table"></a>创建 HBase 表

若要在 HBase 中存储数据，首先必须创建一个表。 预先创建 Storm 需要写入的资源，因为尝试从 Storm 拓扑内部创建资源可能导致多个实例尝试创建相同的资源。 在拓扑外部创建资源，将 Storm 用于读/写和分析。

1. 利用在创建群集过程中向模板提供的 SSH 用户名和密码，使用 SSH 连接到 HBase 群集。 例如，如果使用 `ssh` 命令连接，请使用以下语法：
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
   
    在下列命令中，用创建群集时提供的 SSH 用户名替换 **USERNAME**，用提供的基名称替换 **BASENAME**。 出现提示时，请输入 SSH 用户名密码。
2. 从 SSH 会话中，启动 HBase 外壳程序。
   
        hbase shell
   
    加载外壳程序后，出现 `hbase(main):001:0>` 提示符。
3. 从 HBase Shell 中，输入以下命令以创建存储传感器数据的表。
   
        create 'SensorData', 'cf'
4. 使用以下命令验证是否已创建该表：
   
        scan 'SensorData'
   
    这会返回类似于以下示例的信息，指示表中有 0 行。
   
        ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds
5. 输入 `exit` 退出 HBase shell：

## <a name="configure-the-hbase-bolt"></a>配置 HBase bolt

若要从 Storm 群集写入 HBase，必须向 HBase bolt 提供 HBase 群集的配置详细信息。 此示例使用 HBase 群集中的 **hbase-site.xml** 文件。

### <a name="download-the-hbase-sitexml"></a>下载 hbase-site.xml

从命令提示符处，使用 SCP 从群集下载 **hbase-site.xml** 文件。 在下面的示例中，用创建群集时提供的 SSH 用户替换 **USERNAME**，用之前提供的基名称替换 **BASENAME**。 出现提示时，请输入 SSH 用户名密码。 在 TemperatureMonitor 项目中使用该文件的路径替换 `/path/to/TemperatureMonitor/resources/hbase-site.xml`。

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

此命令下载 **hbase-site.xml** 到指定的路径。

## <a name="build-package-and-deploy-the-solution-to-hdinsight"></a>生成解决方案，并将其打包和部署到 HDInsight

在开发环境中，按照以下步骤将 Storm 拓扑部署到 Storm 群集。

1. 从 **TemperatureMonitor** 目录中，使用以下命令来执行新的生成并从项目创建 JAR 包：
   
        mvn clean compile package
   
    此命令在项目的 **target** 目录中创建一个名为 **TemperatureMonitor-1.0-SNAPSHOT.jar** 的文件。

2. 使用 scp 将 **TemperatureMonitor-1.0-SNAPSHOT.jar** 文件上传到 Storm 群集。 在下面的示例中，用创建群集时提供的 SSH 用户替换 **USERNAME**，用之前提供的基名称替换 **BASENAME**。 出现提示时，请输入 SSH 用户名密码。
   
        scp target/TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar

   > [!NOTE]
   > 上传文件可能需要数分钟的时间。

    使用 scp 上传 **dev.properties** 文件，因为此文件包含用来连接事件中心和仪表板的信息。
   
        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties

3. 文件上传完后，使用 SSH 连接到群集。
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. 在 SSH 会话中，使用以下命令启动拓扑。
   
        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
   
    这将使用 **with-hbase.yaml** 文件中的拓扑定义和 **dev.properties** 文件中的配置值启动拓扑。

5. 启动拓扑后，打开浏览器到 Azure 发布的网站，然后使用 `node app.js` 命令将数据发送到事件中心。 你应该看到 Web 仪表板更新以显示信息。
   
    ![仪表板](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## <a name="view-hbase-data"></a>查看 HBase 数据
使用以下步骤连接到 HBase 并确认数据已写入表：

1. 使用 SSH 连接到 HBase 群集。
   
        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
2. 从 SSH 会话中，启动 HBase 外壳程序。
   
        hbase shell
   
    加载外壳程序后，出现 `hbase(main):001:0>` 提示符。
3. 从表中查看行：
   
        scan 'SensorData'
   
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

