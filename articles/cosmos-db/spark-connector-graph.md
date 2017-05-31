---
title: "Azure Cosmos DB：使用 Spark 和 Apache TinkerPop Gremlin 执行图形分析 | Microsoft Docs"
description: "提供设置说明，说明如何通过 Azure Cosmos DB 和 Spark GraphX 来设置和运行图形分析与并行计算"
services: cosmos-db
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
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: khdang
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 7a95953fadb3089f60fd55973fdb3410012655af
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-perform-graph-analytics-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB：使用 Spark 和 Apache TinkerPop Gremlin 执行图形分析

[Azure Cosmos DB](introduction.md) 是 Microsoft 提供的全局分布式多模型数据库服务。 可创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 最核心的全局分布和水平缩放功能。 Azure Cosmos DB 支持使用 [Apache TinkerPop Gremlin](graph-introduction.md) 完成 OLTP 图形工作负荷

[Spark](http://spark.apache.org/) 是 Apache Software Foundation 项目，专注于通用 OLAP 数据处理。 Spark 提供混合的内存/磁盘型分布式计算模型，类似于 Hadoop 的 MapReduce 模型。 可以使用 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/) 在云中部署 Apache Spark。 Spark 包括 [GraphX 库](http://spark.apache.org/graphx/)，该库适用于图形以及图形并行计算，并且也使用 Apache TinkerPop Gremlin。

Azure Cosmos DB 和 Spark 都有了以后，即可使用 Gremlin 执行 OLTP 和 OLAP 工作负荷。 本快速入门教程介绍如何在 Azure HDInsight Spark 群集上对 Azure Cosmos DB 运行 Gremlin 查询。

## <a name="prerequisites"></a>先决条件

* 在运行此示例之前，必须具备以下先决条件：
   * Azure HDInsight Spark 群集 2.0
   * JDK 1.8+（如果没有 JDK，请运行 `apt-get install default-jdk`）
   * Maven（如果没有 Maven，请运行 `apt-get install maven`）

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

有关如何预配 Azure HDInsight Spark 群集的更多详细信息，请参阅[预配 HDInsight 群集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。

## <a name="create-an-azure-cosmos-db-database-account"></a>创建 Azure Cosmos DB 数据库帐户

首先，使用图形 API 创建数据库帐户。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>添加集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="get-apache-tinkerpop"></a>获取 Apache TinkerPop

* 远程连接到 HDInsight 群集 `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net` 的主节点

* 克隆 TinkerPop3 源代码，在本地生成后再安装到 Maven 缓存

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

* 安装 Spark-Gremlin 插件 

    1. 插件安装由 Grape 负责。 首先需为 Grape 填充存储库信息，使之能够下载插件及其依赖项。 

        * 创建 Grape 配置文件（如果 `~/.groovy/grapeConfig.xml` 中没有）。 使用以下设置：

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

    2. 启动 Gremlin 控制台 `bin/gremlin.sh`
        
    3. 安装在前述步骤中生成的版本为 3.3.0-SNAPSHOT 的 Spark-Gremlin 插件

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

    4. 通过 `:plugin list` 检查 `Hadoop-Gremlin` 是否已激活。由于此插件会妨碍 Spark-Gremlin 插件 `:plugin unuse tinkerpop.hadoop`，因此应将其禁用

## <a name="prepare-tinkerpop3-dependencies"></a>准备 TinkerPop3 依赖项

在前面的步骤中生成 TinkerPop3 时，它还在目标目录中为 Spark 和 Hadoop 拉取了所有 jar 依赖项。 我们将使用 HDI 中预安装的 jar，只根据需要拉取其他依赖项。

    1. 转到 Gremlin 控制台目标目录 `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`。 

    2. 在其中将 `ext/` 下的所有 jar 移至 `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`

    3. 删除 `lib/` 下未在以下列表中的所有 jar 库。

        ```bash
        # TinkerPop3
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

## <a name="get-the-cosmos-db-spark-connector"></a>获取 Cosmos DB Spark 连接器

1. 从 [Github 上的 Azure Cosmos DB Spark 连接器](https://github.com/Azure/azure-documentdb-spark/tree/master/releases/azure-documentdb-spark-0.0.3_2.0.2_2.11)获取 Cosmos DB Spark 连接器 `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` 和 Cosmos DB Java SDK `azure-documentdb-1.10.0.jar`

2. 也可在本地生成。 由于最新版本的 Spark-Gremlin 是使用 Spark 1.6.1 生成的，不兼容目前在 Cosmos DB Spark 连接器中使用的 Spark 2.0.2，因此可以手动生成最新的 TinkerPop3 代码并安装 jar。

    1. 克隆 Cosmos DB Spark 连接器

    2. 生成 TinkerPop3（已在前述步骤中完成）。 在本地安装所有 TinkerPop 3.3.0-SNAPSHOT jar
    
    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    2. 将 `tinkerpop.version` `azure-documentdb-spark/pom.xml` 更新到 `3.3.0-SNAPSHOT`
    
    3. 使用 Maven 生成。 所需 jar 置于 `target` 和 `target/alternateLocation` 中

    ```bash
    git clone https://github.com/Azure/azure-documentdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. 将上述 jar 复制到本地目录 ~/azure-documentdb-spark

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>将依赖项分发到 Spark 辅助角色节点 

1. 由于图形数据的转换依赖于 TinkerPop3，因此需将相关的依赖项分发到所有 Spark 辅助角色节点。

2. 将此前列出的 Gremlin 依赖项以及 CosmosDB Spark 连接器 jar 和 CosmosDB Java SDK 复制到辅助角色节点，如下所示

    1. 将所有 jar 复制到 `~/azure-documentdb-spark` 中

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    2. 获取所有 Spark 辅助角色节点的列表，该列表位于 Ambari 仪表板 `Spark2` 部分的`Spark2 Clients`列表中。

    3. 将该目录复制到每个节点

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>设置环境变量

1. 找到 Spark 群集的 HDP 版本，该版本是 `/usr/hdp/` 下的目录名，例如“2.5.4.2-7”。

2. 为所有节点设置 hdp.version：在 Ambari 仪表板中，转到`YARN section -> Configs -> Advanced`。 在 `Custom yarn-site` 中添加新属性 `hdp.version`，其值设置为主节点上的 HDP 版本。 保存配置 - 会出现警告，但可以将其忽略。 然后，按照通知图标的指示重新启动 YARN 和 Oozie 服务。

3. 在主节点上设置以下环境变量（根据需要替换相应的值）：

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>准备图形配置

1. 使用 Cosmos DB 连接参数和 Spark 设置创建配置文件，然后将其置于 `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties` 中

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
    # DocumentDB Spark Connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. 更新 `spark.driver.extraClassPath` 和 `spark.executor.extraClassPath`，使之包括在前述步骤中分发的 jar 的目录，在本示例中为 `/home/sshuser/azure-documentdb-spark/*`

3. 为 Cosmos DB 填写以下详细信息：

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-tinkerpops-graph-and-save-to-cosmos-db"></a>加载 TinkerPop 的图形并将其保存到 Cosmos DB
我们将以 TinkerPop 预定义的 TinkerPop 现代图形为例，说明如何将图形保存到 Cosmos DB 中。 该图形以 Kryo 格式存储，在 tinkerpop 存储库中提供。

1. 由于我们以 YARN 模式运行 Gremlin，因此需在 Hadoop 文件系统中提供图形数据。 以下命令生成一个目录并将本地图形文件复制到其中。 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. 对 `gremlin-spark.properties` 文件进行临时更新，以便使用 `GryoInputFormat` 来读取图形。 我们还将 `inputLocation` 指定为已创建的目录，如下所示：

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

2. 启动 Gremlin 控制台并创建以下计算步骤，将数据保存到配置的 Cosmos DB 集合：  

    1. 创建图形 `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`

    2. 使用 SparkGraphComputer 进行写入：`graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`

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

3. 在数据资源管理器中，可以验证数据是否已引入到 Cosmos DB 中。

## <a name="load-the-graph-from-cosmos-db-and-run-gremlin-queries"></a>从 Cosmos DB 加载图形并运行 Gremlin 查询

1. 为了加载图形，可以通过编辑 `gremlin-spark.properties` 将 `graphReader` 设置为 `DocumentDBInputRDD`：

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. 执行以下步骤，以便加载图形、遍历数据并对其运行 Gremlin 查询：

    1. 启动 Gremlin 控制台 `bin/gremlin.sh`

    2. 使用配置 `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')` 创建图形

    3. 使用 SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)` 创建图形遍历

    4. 运行 Gremlin 图形查询：

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
> 若要查看更详细的日志记录，请在 `conf/log4j-console.properties` 中将日志级别设置为更详细的级别。
>

## <a name="next-steps"></a>后续步骤

本快速入门教程介绍了如何使用 Azure Cosmos DB 和 Spark 来共同处理图形。

> [!div class="nextstepaction"]

