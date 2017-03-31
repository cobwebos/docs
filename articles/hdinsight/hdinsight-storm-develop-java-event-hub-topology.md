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
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: fcca957dc365d8c38b5a08991939860c5af96813
ms.lasthandoff: 03/25/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>使用 Storm on HDInsight 从 Azure 事件中心处理事件 (Java)

了解如何将 Azure 事件中心与 Storm on HDInsight 配合使用。 此示例使用基于 Java 的组件在 Azure 事件中心中读取和写入数据。

Azure 事件中心可让你处理网站、应用程序和设备中的大量数据。 借助事件中心 Spout，你可以轻松使用 Apache Storm on HDInsight 实时分析这些数据。 你还可以使用事件中心 Bolt 从 Storm 向事件中心写入数据。

## <a name="prerequisites"></a>先决条件

* Apache Storm on HDInsight 3.5 版群集。 有关详细信息，请参阅 [Storm on HDInsight 群集入门](hdinsight-apache-storm-tutorial-get-started-linux.md)。
    
    > [!IMPORTANT]
    > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight Deprecation on Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)（HDInsight 在 Windows 上即将弃用）。

* [Azure 事件中心](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)。

* [Oracle Java 开发人员工具包 (JDK) 版本 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或等效工具，例如 [OpenJDK](http://openjdk.java.net/)。

* [Maven](https://maven.apache.org/download.cgi)：Maven 是 Java 项目的项目生成系统。

* 文本编辑器或集成开发环境 (IDE)。
  
  > [!NOTE]
  > 你的编辑器或 IDE 可能具有处理 Maven 的特定功能，但本文档中未提供说明。 有关环境编辑功能的详细信息，请参阅所使用产品的文档。
  
  * SSH 客户端。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

* 一个 SCP 客户端。 所有 Linux、Unix 和 OS X 系统（包括 Windows 10 Bash）都提供 `scp` 命令。对于未提供 `scp` 命令的 Windows 版本，建议使用 PSCP。 PSCP 可通过 [PuTTY 下载页](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)获得。

## <a name="understanding-the-example"></a>了解示例

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) 示例包含两个拓扑：

**com.microsoft.example.EventHubWriter** 将随机数据写入 Azure 事件中心。 数据由 Spout 生成，是随机设备 ID 和设备值。 因此它可以模拟发出字符串 ID 和数字值的某些硬件。

**com.microsoft.example.EventHubReader** 从事件中心读取数据，并将其存储到 /devicedata 目录的群集默认存储中。

数据在写入事件中心之前已格式化为 JSON 文档，在被读取器读取时，将从 JSON 解析成元组。 JSON 格式如下：

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>项目配置

**POM.xml** 文件包含此 Maven 项目的配置信息。 需要关注的部分是：

#### <a name="the-eventhubs-storm-spout-dependency"></a>EventHubs Storm Spout 依赖性

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

此 xml 定义 eventhubs 包的依赖性，包含用于从事件中心读取的 Spout 和写入事件中心的 Bolt。

> [!NOTE]
> 在本文档后面将安装此包。

#### <a name="the-hdfsbolt-and-wasb-components"></a>HdfsBolt 和 WASB 组件

HdfsBolt 一般用于将数据存储到 Hadoop 分布式文件系统 (HDFS)。 但是，HDInsight 群集使用 Azure 存储空间 (WASB) 作为默认的数据存储区，因此我们必须加载多个组件，使 HdfsBolt 识别 WASB 文件系统。

```xml
<!--HdfsBolt stuff -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-hdfs</artifactId>
    <exclusions>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
    </exclusion>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
    </exclusion>
    </exclusions>
    <version>${storm.version}</version>
</dependency>
<!--So HdfsBolt knows how to talk to WASB -->
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-hdfs</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-azure</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>${hadoop.version}</version>
    <exclusions>
    <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```

> [!NOTE]
> 如果使用早期版本的 HDInsight（例如 3.2 版），则必须手动注册这些组件。 有关示例，请参阅示例存储库的 [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) 分支。

#### <a name="the-maven-compiler-plugin"></a>maven-compiler-plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

这将配置项目以生成 Java 8 的输出，由 HDInsight 3.5 使用。

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
    <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
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

这将配置解决方案以将输出打包到 uber jar。 该 jar 包含项目代码和必需的依赖项。 它还用于：

* 重命名依赖项的许可证文件。
* 排除安全性/签名。
* 确保同一接口的多个实现已合并成一个条目。

这些配置设置阻止运行时错误。

#### <a name="the-exec-maven-plugin"></a>exec-maven-plugin

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.2.1</version>
    <executions>
    <execution>
    <goals>
        <goal>exec</goal>
    </goals>
    </execution>
    </executions>
    <configuration>
    <executable>java</executable>
    <includeProjectDependencies>true</includeProjectDependencies>
    <includePluginDependencies>false</includePluginDependencies>
    <classpathScope>compile</classpathScope>
    <mainClass>${storm.topology}</mainClass>
    </configuration>
</plugin>
```

使用此配置可以更轻松地在本地开发环境中运行拓扑。 可以使用以下语法在本地运行拓扑：

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

例如，`mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`。

#### <a name="the-resources-section"></a>resources 节

```xml
<resources>
    <resource>
    <directory>${basedir}/conf</directory>
    <filtering>false</filtering>
    <includes>
        <include>EventHubs.properties</include>
    </includes>
    </resource>
</resources>
```

此配置定义项目中包含的非 Java 资源。 例如，**EventHubs.properties** 文件包含用于连接到 Azure 事件中心的信息。

## <a name="configure-environment-variables"></a>配置环境变量

可以在开发工作站上安装 Java 和 JDK 时设置以下环境变量。 不过，你应该检查它们是否存在并且包含系统的正确值。

* **JAVA_HOME** - 应该指向已安装 Java 运行时环境 (JRE) 的目录。 例如，在 Unix 或 Linux 分发版中，它的值应该类似于 `/usr/lib/jvm/java-7-oracle`。 在 Windows 中，它的值类似于 `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - 应该包含以下路径：
  
  * **JAVA_HOME**（或等效的路径）
  * **JAVA_HOME\bin**（或等效的路径）
  * 安装 Maven 的目录

## <a name="download-and-register-the-eventhub-components"></a>下载并注册 EventHub 组件

1. 从 [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar) 下载 `storm-eventhubs-1.0.2-jar-with-dependencies.jar`。 此文件包含用于从 EventHubs 读取和写入的 spout 和 bolt 组件。

2. 使用以下命令在本地 maven 存储库中注册组件：
    
        mvn install:install-file -Dfile=storm-eventhubs-1.0.2-jar-with-dependencies.jar -DgroupId=com.microsoft -DartifactId=eventhubs -Dversion=1.0.2 -Dpackaging=jar
    
    修改 `-Dfile=` 参数以指向下载的文件位置。

    此命令将该文件安装到本地 Maven 存储库中，以便 Maven 在编译时可以找到它。

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

2. 使用以下命令生成和打包项目：
   
        mvn package
   
    此命令下载所需的依赖项，进行生成，然后打包项目。 输出将作为 **EventHubExample-1.0-SNAPSHOT.jar** 存储在 **/target** 目录中。

## <a name="deploy-the-topologies"></a>部署拓扑

此项目创建的 jar 包含两个拓扑：**com.microsoft.example.EventHubWriter** 和 **com.microsoft.example.EventHubReader**。 应该先启动 EventHubWriter 拓扑，因为它会将事件写入事件中心，然后由 EventHubReader 读取事件。

1. 使用 SCP 将 jar 包复制到 HDInsight 群集。 将 USERNAME 替换为群集的 SSH 用户。 将 CLUSTERNAME 替换为 HDInsight 群集名：
   
        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    如果使用了 SSH 帐户密码，则系统将提示输入该密码。 如果将 SSH 密钥与帐户配合使用，则可能需要使用 `-i` 参数来指定密钥文件的路径。 例如，`scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`。
   
   > [!NOTE]
   > 如果客户端是 Windows 工作站，则可能尚未安装 SCP 命令。 建议使用可以从 [PuTTY download page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)（PuTTY 下载页面）中下载的 PSCP。
   
    此命令会将文件复制到群集上 SSH 用户的主目录。

2. 上传文件完成后，可使用 SSH 连接到 HDInsight 群集。 将 **USERNAME** 替换为 SSH 登录名。 将 **CLUSTERNAME** 替换为 HDInsight 群集名：
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > 如果使用了 SSH 帐户的密码，则系统将提示输入该密码。 如果将 SSH 密钥与帐户配合使用，则可能需要使用 `-i` 参数来指定密钥文件的路径。 以下示例将从 `~/.ssh/id_rsa` 加载私钥：
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    如果使用 PuTTY，请在“主机名(或 IP 地址)”字段中输入 `CLUSTERNAME-ssh.azurehdinsight.net`，然后单击“打开”进行连接。 系统将提示输入 SSH 帐户名。
   
   > [!NOTE]
   > 如果使用了 SSH 帐户密码，则系统将提示输入该密码。 如果将 SSH 密钥与帐户配合使用，则可能需要使用以下步骤来选择密钥：
   > 
   > 1. 在“类别”中，依次展开“连接”和“SSH”，然后选择“身份验证”。
   > 2. 单击“浏览”，然后选择包含私钥的 .ppk 文件。
   > 3. 单击“打开”进行连接。

3. 使用以下命令启动拓扑：
   
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader
   
    这些命令使用“读取器”和“写入器”的友好名称启动拓扑。

4. 等待一分钟让拓扑生成数据。 使用以下命令验证已写入到 HDInsight 存储的数据：
   
        hadoop fs -ls /devicedata
   
    此命令返回类似于以下文本的文件列表：
   
        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt
   
   > [!NOTE]
   > 某些文件的大小可能会显示为 0，因为虽然它们是由 EventHubReader 所创建的，但数据还尚未存储到其中。
   
    可以使用以下命令来查看这些文件的内容：
   
        hadoop fs -text /devicedata/*.txt
   
    这会返回类似于以下文本的数据：
   
        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574
   
    第一列包含设备 ID 值，而第二列则是设备值。

5. 使用以下命令停止拓扑：
   
        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>删除群集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>故障排除

如果在 /devicedata 目录中看不到文件，请使用 Storm UI 来查找拓扑返回的任何错误。

有关使用 Storm UI 的详细信息，请参阅以下主题：

* 如果使用**基于 Linux** 的 Storm on HDInsight 群集，请参阅[在基于 Linux 的 HDInsight 上部署和管理 Apache Storm 拓扑](hdinsight-storm-deploy-monitor-topology-linux.md)

* 如果使用**基于 Windows** 的 Storm on HDInsight 群集，请参阅[在基于 Windows 的 HDInsight 上部署和管理 Apache Storm 拓扑](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>后续步骤

* [Storm on HDInsight 的示例拓扑](hdinsight-storm-example-topology.md)


