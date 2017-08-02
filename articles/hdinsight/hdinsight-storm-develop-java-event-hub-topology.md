---
title: "使用 Java 通过 Storm on HDInsight 处理事件中心的事件 | Microsoft Docs"
description: "了解如何使用通过 Maven 创建的 Java Storm 拓扑处理事件中心数据。"
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/13/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 2e8ebbdab2be7bed224a67facec798820615bb22
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>使用 Storm on HDInsight 从 Azure 事件中心处理事件 (Java)

了解如何将 Azure 事件中心与 Storm on HDInsight 配合使用。 此示例使用基于 Java 的组件在 Azure 事件中心中读取和写入数据。

Azure 事件中心可让你处理网站、应用程序和设备中的大量数据。 借助事件中心 Spout，你可以轻松使用 Apache Storm on HDInsight 实时分析这些数据。 你还可以使用事件中心 Bolt 从 Storm 向事件中心写入数据。

## <a name="prerequisites"></a>先决条件

* Apache Storm on HDInsight 3.6 版群集。 有关详细信息，请参阅 [Storm on HDInsight 群集入门](hdinsight-apache-storm-tutorial-get-started-linux.md)。

    > [!IMPORTANT]
    > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* [Azure 事件中心](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)。

* [Oracle Java Developer Kit (JDK) 版本 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 或等效工具，例如 [OpenJDK](http://openjdk.java.net/)。

* [Maven](https://maven.apache.org/download.cgi)：Maven 是 Java 项目的项目生成系统。

* 文本编辑器或集成开发环境 (IDE)。

    > [!NOTE]
    > 你的编辑器或 IDE 可能具有处理 Maven 的特定功能，但本文档中未提供说明。 有关环境编辑功能的详细信息，请参阅所使用产品的文档。

    * SSH 客户端。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

* `ssh` 和 `scp` 命令。 这些命令用于将文件复制到 HDInsight 群集。 在 Windows 上，可以通过 Bash on Windows 10 获取这些命令。

## <a name="understanding-the-example"></a>了解示例

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) 示例包含两个拓扑：

`resources/writer.yaml` 拓扑将随机数据写入 Azure 事件中心。 数据由 `DeviceSpout` 组件生成，是随机设备 ID 和设备值。 因此它可以模拟发出字符串 ID 和数字值的某些硬件。

`resources/reader.yaml` 拓扑从事件中心读取数据（EventHubWriter 写入的数据）、分析 JSON 数据，记录 `deviceId` 和 `deviceValue` 数据。

数据在写入事件中心之前已格式化为 JSON 文档，在被读取器读取时，将从 JSON 解析成元组。 JSON 格式如下：

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>项目配置

`POM.xml` 文件包含此 Maven 项目的配置信息。 需要关注的部分是：

#### <a name="event-hub-components"></a>事件中心组件

读取和写入 Azure 事件中心的组件位于 [HDInsight 存储库](https://github.com/hdinsight/mvn-rep)。 `POM.xml` 文件中的以下节从此存储库加载组件

```xml
<repositories>
    <repository>
        <id>hdinsight-examples</id>
        <url>http://raw.github.com/hdinsight/mvn-repo/master</url>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>EventHubs Storm Spout 依赖性

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>${storm.eventhub.version}</version>
</dependency>
```

此 xml 定义 eventhubs 包的依赖性，包含用于从事件中心读取的 Spout 和写入事件中心的 Bolt。

```xml
</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

此 xml 配置项目以生成 Java 8 的输出，由 HDInsight 3.5 或更高版本使用。

#### <a name="the-maven-shade-plugin"></a>maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
    </transformers>
    <!-- Keep us from getting a bad signature error -->
    <filters>
    <filter>
        <artifact>*:*</artifact>
        <excludes>
            <exclude>META-INF/*.SF</exclude>
            <exclude>META-INF/*.DSA</exclude>
            <exclude>META-INF/*.RSA</exclude>
        </excludes>
    </filter>
    </filters>
</configuration>
<executions>
    <execution>
    <phase>package</phase>
    <goals>
        <goal>shade</goal>
    </goals>
    </execution>
</executions>
</plugin>
```

此 xml 配置解决方案以将输出打包到 uber jar。 该 jar 包含项目代码和必需的依赖项。 它还用于：

* 重命名依赖项的许可证文件。
* 排除安全性/签名。
* 确保同一接口的多个实现已合并成一个条目。

这些配置设置阻止运行时错误。

#### <a name="topology-definitions"></a>拓扑定义

此示例使用 [Flux](https://storm.apache.org/releases/1.1.0/flux.html) 框架。 此框架使用 YAML 定义拓扑。 主要优点是不需要在 Java 代码中对拓扑进行硬编码。 由于定义是 YAML，因此可以在提交拓扑之前对其进行更改，而无需重新编译任何内容。

__writer.yaml__：

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubwriter"

components:
  # Configure the Event Hub spout
  - id: "eventhubbolt-config"
    className: "org.apache.storm.eventhubs.bolt.EventHubBoltConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.write.policy.name}"
      - "${eventhub.write.policy.key}"
      - "${eventhub.namespace}"
      - "servicebus.windows.net"
      - "${eventhub.name}"

spouts:
  - id: "device-emulator-spout"
    className: "com.microsoft.example.DeviceSpout"
    parallelism: ${eventhub.partitions}

bolts:
  - id: "eventhub-bolt"
    className: "org.apache.storm.eventhubs.bolt.EventHubBolt"
    constructorArgs:
      - ref: "eventhubbolt-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

# How data flows through the components
streams:
  - name: "spout -> eventhub" # just a string used for logging
    from: "device-emulator-spout"
    to: "eventhub-bolt"
    grouping:
        type: SHUFFLE

  - name: "spout -> logger"
    from: "device-emulator-spout"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

__reader.yaml__：

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubreader"

components:
  # Configure the Event Hub spout
  - id: "eventhubspout-config"
    className: "org.apache.storm.eventhubs.spout.EventHubSpoutConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.read.policy.name}"
      - "${eventhub.read.policy.key}"
      - "${eventhub.namespace}"
      - "${eventhub.name}"
      - ${eventhub.partitions}

spouts:
  - id: "eventhub-spout"
    className: "org.apache.storm.eventhubs.spout.EventHubSpout"
    constructorArgs:
      - ref: "eventhubspout-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

bolts:
  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

  # Parses from JSON into tuples
  - id: "parser-bolt"
    className: "com.microsoft.example.ParserBolt"
    parallelism: ${eventhub.partitions}

# How data flows through the components
streams:
  - name: "spout -> parser" # just a string used for logging
    from: "eventhub-spout"
    to: "parser-bolt"
    grouping:
        type: SHUFFLE

  - name: "parser -> log-bolt"
    from: "parser-bolt"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

#### <a name="tell-the-topology-about-event-hub"></a>向拓扑告知有关事件中心的信息

在运行时，`dev.properties` 文件用于将事件中心配置传递给拓扑。 以下示例是该文件的默认内容：

```yaml
eventhub.write.policy.name: writer
eventhub.write.policy.key: your_key_here
eventhub.read.policy.name: reader
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: storm
eventhub.partitions: 2
```

## <a name="configure-environment-variables"></a>配置环境变量

可以在开发工作站上安装 Java 和 JDK 时设置以下环境变量。 不过，你应该检查它们是否存在并且包含系统的正确值。

* **JAVA_HOME** - 应该指向已安装 Java 运行时环境 (JRE) 的目录。 例如，在 Unix 或 Linux 分发版中，它的值应该类似于 `/usr/lib/jvm/java-7-oracle`。 在 Windows 中，它的值类似于 `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - 应该包含以下路径：

  * **JAVA_HOME**（或等效的路径）
  * **JAVA_HOME\bin**（或等效的路径）
  * 安装 Maven 的目录

## <a name="configure-event-hub"></a>配置事件中心

事件中心是此示例的数据源。 使用以下步骤创建事件中心。

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“新建” > “服务总线” > “事件中心” > “自定义创建”。

2. 在“添加新事件中心”屏幕中，输入**事件中心名称**。 选择要在其中创建中心的**区域**，然后创建一个命名空间或选择现有命名空间。 最后，单击**箭头**以继续。

    ![向导页 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

   > [!NOTE]
   > 选择与 Storm on HDInsight 服务器相同的**位置**，以降低延迟和成本。

3. 在“配置事件中心”屏幕中，输入“分区计数”和“消息保留期”值。 对于本示例，请使用分区计数 10，消息保留期 1。 记下分区计数，因为稍后需要用到此值。

    ![向导页 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. 创建事件中心之后，请选择命名空间、“事件中心”，然后选择前面创建的事件中心。
5. 选择“配置”，然后使用以下信息创建两个新的访问策略：

    <table>
    <tr><th>Name</th><th>权限</th></tr>
    <tr><td>写入器</td><td>发送</td></tr>
    <tr><td>读取器</td><td>侦听</td></tr>
    </table>

    创建权限后，在页面底部选择“保存”图标。 使用这些共享访问策略读取和写入到事件中心。

    ![策略](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. 保存策略后，使用页面底部的“共享访问密钥生成器”检索 **writer** 和 **reader** 策略的密钥。 保存这些密钥。

## <a name="download-and-build-the-project"></a>下载并生成项目

1. 从 GitHub 中下载项目：[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)。 可以下载 zip 存档形式的包，或者使用 [git](https://git-scm.com/) 在本地克隆项目。

2. 使用事件中心的配置修改 `dev.properties` 文件。

3. 使用以下命令生成和打包项目：

        mvn package

    此命令下载所需的依赖项，进行生成，然后打包项目。 输出将作为 **EventHubExample-1.0-SNAPSHOT.jar** 存储在 **/target** 目录中。

## <a name="test-locally"></a>本地测试

由于这些拓扑只是读取和写入事件中心，因此，如果有 [Storm 开发环境](http://storm.apache.org/releases/current/Setting-up-development-environment.html)，则可以在本地对其进行测试。 使用以下步骤在开发环境中本地运行：

1. 运行写入器：

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /writer.yaml --filter dev.properties

2. 运行读取器：

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /reader.yaml --filter dev.properties

> [!TIP]
> * `--local`：以本地模式（非分布式）运行拓扑。
> * `-R /writer.yaml`：从 jar 中打包的 `resources` 加载拓扑定义。 如果拓扑是本地文件系统上的文件，请指定其路径作为最后一个参数。
> * `--filter dev.properties`：使用 `dev.properties` 的内容填充拓扑定义中的值。 例如，`${eventhub.read.policy.name}`。

在本地运行时，会将输出记录到控制台。 使用 __Ctrl+C__ 停止拓扑。

## <a name="deploy-the-topologies"></a>部署拓扑

1. 使用 SCP 将 jar 包复制到 HDInsight 群集。 将 USERNAME 替换为群集的 SSH 用户。 将 CLUSTERNAME 替换为 HDInsight 群集名：

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    如果使用了 SSH 帐户密码，则系统将提示输入该密码。 如果将 SSH 密钥与帐户配合使用，则可能需要使用 `-i` 参数来指定密钥文件的路径。 例如： `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    此命令会将文件复制到群集上 SSH 用户的主目录。

2. 上传文件完成后，可使用 SSH 连接到 HDInsight 群集。 将 **USERNAME** 替换为 SSH 登录名。 将 **CLUSTERNAME** 替换为 HDInsight 群集名：

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > 如果使用了 SSH 帐户的密码，则系统将提示输入该密码。 如果将 SSH 密钥与帐户配合使用，则可能需要使用 `-i` 参数来指定密钥文件的路径。 以下示例从 `~/.ssh/id_rsa` 加载私钥：
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. 使用以下命令启动拓扑：

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties

    > [!TIP]
    > * `--remote`：将拓扑提交到 Nimbus 服务，后者在群集中的工作节点上启动拓扑。

4. 若要查看记录的数据，请转到 https://CLUSTERNAME.azurehdinsight.net/stormui，其中 __CLUSTERNAME__ 是 HDInsight 群集的名称。 选择拓扑，深入到组件。 选择组件实例的 __port__ 项即可查看记录的信息。

5. 使用以下命令停止拓扑：

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>后续步骤

* [Storm on HDInsight 的示例拓扑](hdinsight-storm-example-topology.md)

