---
title: "Azure Cosmos DB：使用 Spark 和 Apache TinkerPop Gremlin 执行图形分析 | Microsoft Docs"
description: "本文提供通过 Spark 和 TinkerPop SparkGraphComputer 在 Azure Cosmos DB 中设置和运行图形分析和并行计算的说明。"
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 09/08/2017
ms.author: khdang
ms.openlocfilehash: 3ba34ac177cb9b88030be85288e3b3a429d4fd8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB：使用 Spark 和 Apache TinkerPop Gremlin 执行图形分析

[Azure Cosmos DB](introduction.md) 是 Microsoft 提供的全局分布式多模型数据库服务。 可创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 最核心的全局分布和水平缩放功能。 Azure Cosmos DB 支持使用 [Apache TinkerPop Gremlin](graph-introduction.md) 的联机事务处理 (OLTP) 图形工作负荷。

[Spark](http://spark.apache.org/) 是 Apache Software Foundation 项目，专注于通用联机分析处理 (OLAP) 数据处理。 Spark 提供混合的内存/磁盘型分布式计算模型，类似于 Hadoop MapReduce 模型。 可以使用 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) 在云中部署 Apache Spark。

通过将 Azure Cosmos DB 和 Spark 相结合，即可在使用 Gremlin 时执行 OLTP 和 OLAP 工作负荷。 本快速入门文章介绍如何在 Azure HDInsight Spark 群集上对 Azure Cosmos DB 运行 Gremlin 查询。

## <a name="prerequisites"></a>先决条件

在运行此示例之前，必须具备以下先决条件：
* Azure HDInsight Spark 群集 2.0
* JDK 1.8+（如果没有 JDK，请运行 `apt-get install default-jdk`）。
* Maven（如果没有 Maven，请运行 `apt-get install maven`）。
* Azure 订阅 ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

有关如何设置 Azure HDInsight Spark 群集的信息，请参阅[预配 HDInsight 群集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="create-an-azure-cosmos-db-database-account"></a>创建 Azure Cosmos DB 数据库帐户

首先，使用图形 API 创建数据库帐户，方法如下：

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-collection"></a>添加集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-graph.md)]

## <a name="get-apache-tinkerpop"></a>获取 Apache TinkerPop

获取 Apache TinkerPop，方法如下：

1. 远程连接到 HDInsight 群集 `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net` 的主节点。

2. 克隆 TinkerPop3 源代码，在本地生成后再安装到 Maven 缓存。

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. 安装 Spark-Gremlin 插件 

    a. 插件安装由 Grape 负责。 为 Grape 填充存储库信息，使之能够下载插件及其依赖项。 

      创建 Grape 配置文件（如果 `~/.groovy/grapeConfig.xml` 中没有）。 使用以下设置：

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. 启动 Gremlin 控制台 `bin/gremlin.sh`。
        
    c. 安装在前述步骤中生成的版本为 3.3.0-SNAPSHOT 的 Spark-Gremlin 插件：

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. 通过 `:plugin list` 检查 `Hadoop-Gremlin` 是否已激活。 禁用此插件，因为它可能妨碍 Spark Gremlin 插件`:plugin unuse tinkerpop.hadoop`。

## <a name="prepare-tinkerpop3-dependencies"></a>准备 TinkerPop3 依赖项

在前述步骤中生成 TinkerPop3 时，此进程还在目标目录中为 Spark 和 Hadoop 拉取了所有 jar 依赖项。 使用通过 HDI 预安装的 jar，仅在必要时拉取其他依赖项。

1. 转到位于 `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone` 的 Gremlin 控制台目标目录。 

2. 将 `ext/` 下的所有 jar 移至 `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`。

3. 删除 `lib/` 下未在以下列表中的所有 jar 库：

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>获取 Azure Cosmos DB Spark 连接器

1. 从 [GitHub 上的 Azure Cosmos DB Spark 连接器](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11)获取 Azrue Cosmos DB Spark 连接器 `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` 和 Cosmos DB Java SDK `azure-documentdb-1.10.0.jar`。

2. 也可在本地生成。 由于最新版本的 Spark-Gremlin 是使用 Spark 1.6.1 生成的，不兼容目前在 Azure Cosmos DB Spark 连接器中使用的 Spark 2.0.2，因此可以手动生成最新的 TinkerPop3 代码并安装 jar。 请执行以下操作：

    a. 克隆 Azure Cosmos DB Spark 连接器。

    b. 生成 TinkerPop3（已在前述步骤中完成）。 在本地安装所有 TinkerPop 3.3.0-SNAPSHOT jar。

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. 将 `tinkerpop.version` `azure-documentdb-spark/pom.xml` 更新到 `3.3.0-SNAPSHOT`。
    
    d. 使用 Maven 生成。 所需 jar 置于 `target` 和 `target/alternateLocation` 中。

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. 将上述 jar 复制到本地目录 ~/azure-documentdb-spark：

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>将依赖项分发到 Spark 辅助角色节点 

1. 由于图形数据的转换依赖于 TinkerPop3，因此需将相关的依赖项分发到所有 Spark 辅助角色节点。

2. 将前述 Gremlin 依赖项、CosmosDB Spark 连接器 jar 和 CosmosDB Java SDK 复制到辅助角色节点，方法如下：

    a. 将所有 jar 复制到 `~/azure-documentdb-spark` 中。

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. 获取所有 Spark 辅助角色节点的列表，该列表位于 Ambari 仪表板 `Spark2` 部分的 `Spark2 Clients` 列表中。

    c. 将该目录复制到每个节点。

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>设置环境变量

1. 查找 Spark 群集的 HDP 版本。 它是 `/usr/hdp/` 下的目录名（如，2.5.4.2-7）。

2. 为所有节点设置 hdp.version。 在 Ambari 仪表板中，转到“YARN 部分” > “配置” > “高级”，然后执行以下操作： 
 
    a. 在 `Custom yarn-site` 中添加新属性 `hdp.version`，其值设置为主节点上的 HDP 版本。 
     
    b. 保存配置。 出现警告，但可将其忽略。 
     
    c. 按照通知图标的指示重启 YARN 和 Oozie 服务。

3. 在主节点上设置以下环境变量（根据需要替换相应的值）：

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>准备图形配置

1. 使用 Azure Cosmos DB 连接参数和 Spark 设置创建配置文件，然后将其置于 `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties` 中。

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. 更新 `spark.driver.extraClassPath` 和 `spark.executor.extraClassPath`，使之包括前述步骤中分发的 jar 的目录，在本示例中为 `/home/sshuser/azure-documentdb-spark/*`。

3. 为 Azure Cosmos DB 提供以下详细信息：

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>加载 TinkerPop 图形，并将其保存到 Azure Cosmos DB 中
为了演示如何将图形保存到 Azure Cosmos DB 中，此示例使用 TinkerPop 预定义的 TinkerPop 新式图形。 该图形以 Kryo 格式存储，在 TinkerPop 存储库中提供。

1. 由于以 YARN 模式运行 Gremlin，因此必须在 Hadoop 文件系统中提供图形数据。 使用以下命令生成一个目录并将本地图形文件复制到其中。 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. 临时更新 `gremlin-spark.properties` 文件，以便使用 `GryoInputFormat` 来读取图形。 还将 `inputLocation` 指定为创建的目录，如下所示：

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. 启动 Gremlin 控制台并创建以下计算步骤，将数据保存到配置的 Azure Cosmos DB 集合：  

    a. 创建图形 `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`。

    b. 使用 SparkGraphComputer 写入 `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`。

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. 从数据资源管理器中，可以验证数据是否已保存到 Azure Cosmos DB 中。

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>从 Azure Cosmos DB 加载图形，并运行 Gremlin 查询

1. 若要加载图形，请编辑 `gremlin-spark.properties` 将 `graphReader` 设置为 `DocumentDBInputRDD`：

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. 通过执行以下步骤加载图形、遍历数据并对其运行 Gremlin 查询：

    a. 启动 Gremlin 控制台 `bin/gremlin.sh`。

    b. 使用配置 `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')` 创建图形。

    c. 使用 SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)` 创建图形遍历。

    d. 运行以下 Gremlin 图形查询：

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> 要查看更详细的日志记录，请在 `conf/log4j-console.properties` 中将日志级别设置为更详细的级别。
>

## <a name="next-steps"></a>后续步骤

本快速入门文章介绍了如何结合使用 Azure Cosmos DB 和 Spark 来处理图形。

> [!div class="nextstepaction"]
