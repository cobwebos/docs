---
title: 带有使用 Java 4.0 SDK 的 Azure Cosmos DB Cassandra API 的 Java 应用
description: 本快速入门介绍如何配合 Azure 门户和 Java 4.0 SDK 使用 Azure Cosmos DB Cassandra API 创建配置文件应用程序
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 076cb892740b99971400fbc34f60dc1083554555
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532199"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v4-driver"></a>生成 Java 应用以管理 Azure Cosmos DB Cassandra API 数据（v4 驱动程序）

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> 

在本快速入门中，你将创建一个 Azure Cosmos DB Cassandra API 帐户，并使用从 GitHub 克隆的 Cassandra Java 应用通过 Java 的 [v4.x Apache Cassandra 驱动程序](https://github.com/datastax/java-driver/tree/4.x)创建一个 Cassandra 数据库和一个容器。 Azure Cosmos DB 是一种多模型数据库服务，你可以借助其全球分布和水平缩放功能快速创建和查询文档、表、键/值和图数据库。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或者[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 而无需 Azure 订阅。
- [Java 开发工具包 (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)。 将 `JAVA_HOME` 环境变量指向其中安装了 JDK 的文件夹。
- [Maven 二进制存档](https://maven.apache.org/download.cgi)。 在 Ubuntu 上运行 `apt-get install maven`，以安装 Maven。
- [Git](https://www.git-scm.com/downloads)。 在 Ubuntu 上运行 `sudo apt-get install git`，以安装 Git。

## <a name="create-a-database-account"></a>创建数据库帐户

在创建文档数据库之前，需通过 Azure Cosmos DB 创建 Cassandra 帐户。

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，让我们转到如何使用代码上来。 从 GitHub 克隆 Cassandra 应用，设置连接字符串，并运行应用。 会看到以编程方式处理数据是多么容易。 

1. 打开命令提示符。 创建名为 `git-samples` 的新文件夹。 然后，关闭命令提示符。

    ```bash
    md "C:\git-samples"
    ```

2. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为要安装示例应用的新文件夹。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started-v4.git
    ```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何通过代码创建数据库资源，可以查看以下代码片段。 否则，可以直接跳转到[更新连接字符串](#update-your-connection-string)。 这些代码片段全部摘自 src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java** 文件。  

* `CqlSession` 连接到 Azure Cosmos DB Cassandra API 并返回可供访问的会话（v3 驱动程序中的 `Cluster` 对象现已过时）。 Cassandra 主机、端口、用户名和密码是使用 Azure 门户中的连接字符串页设置的。

    ```java
        this.session = CqlSession.builder().withSslContext(sc)
                .addContactPoint(new InetSocketAddress(cassandraHost, cassandraPort)).withLocalDatacenter(region)
                .withAuthCredentials(cassandraUsername, cassandraPassword).build();
    ```


以下代码片段来自 src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java** 文件。

* 如果密钥空间自上次运行的时候已存在，则删除该密钥空间。

    ```java
    public void dropKeyspace() {
        String query = "DROP KEYSPACE IF EXISTS "+keyspace+"";
        session.execute(query);
        LOGGER.info("dropped keyspace '"+keyspace+"'");
    } 
    ```
* 创建新的键空间。

    ```java
    public void createKeyspace() {
        String query = "CREATE KEYSPACE "+keyspace+" WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }";
        session.execute(query);
        LOGGER.info("Created keyspace '"+keyspace+"'");
    }
    ```

* 创建新表。

   ```java
    public void createTable() {
        String query = "CREATE TABLE "+keyspace+"."+table+" (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table '"+table+"'");
    }
   ```

* 使用预定义的语句对象插入用户实体。

    ```java
    public String prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  "+keyspace+"."+table+" (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return insertStatement;
    }

    public void insertUser(String preparedStatement, int id, String name, String city) {
        PreparedStatement prepared = session.prepare(preparedStatement);
        BoundStatement bound = prepared.bind(id, city, name).setIdempotent(true);
        session.execute(bound);
    }
    ```

* 通过查询获取所有用户信息。

    ```java
    public void selectAllUsers() {
        final String query = "SELECT * FROM "+keyspace+"."+table+"";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

 * 通过查询获取单个用户的信息。

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM "+keyspace+"."+table+" where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。 连接字符串详细信息使应用能够与托管数据库进行通信。

1. 在 [Azure 门户](https://portal.azure.com/)的 Azure Cosmos DB 帐户中，选择“连接字符串”****。 

    :::image type="content" source="./media/create-cassandra-java/copy-username-connection-string-azure-portal.png" alt-text="在 Azure 门户的“连接字符串”页面中查看并复制用户名":::

2. 使用屏幕右侧的 :::image type="icon" source="./media/create-cassandra-java/copy-button-azure-portal.png"::: 按钮复制“CONTACT POINT”值。

3. 打开 C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources** 文件夹中的 config.properties** 文件。 

3. 粘贴门户中的“联系点”值，并覆盖第 2 行中的 `<Cassandra endpoint host>`。

    config.properties** 的第 2 行现在应类似于 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. 返回到门户，并复制“用户名”值。 粘贴门户中的“用户名”值，并覆盖第 4 行中的 `<cassandra endpoint username>`。

    config.properties** 的第 4 行现在应类似于 

    `cassandra_username=cosmos-db-quickstart`

4. 返回到门户，并复制“密码”值。 粘贴门户中的“密码”值，并覆盖第 5 行中的 `<cassandra endpoint password>`。

    config.properties** 的第 5 行现在应类似于 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. 在第 6 行中，如果需要使用特定的 TLS/SSL 证书，请将 `<SSL key store file location>` 替换为该 TLS/SSL 证书的位置。 如果未提供值，则会使用安装在 <JAVA_HOME>/jre/lib/security/cacerts 中的 JDK 证书。 

6. 如果将第 6 行更改为使用特定的 TLS/SSL 证书，请将第 7 行更新为对该证书使用密码。 

7. 请注意，需要为联系点添加默认区域（例如`West US`），例如

    `region=West US`

    这是因为 v.4x 驱动程序只允许将一个本地 DC 与联系点配对。 如果要添加默认区域以外的区域（这是首次创建 Cosmos DB 帐户时给出的区域），则需要在添加联系点时使用区域后缀，例如 `host-westus.cassandra.cosmos.azure.com`。

8. 保存 config.properties** 文件。

## <a name="run-the-java-app"></a>运行 Java 应用

1. 在 git 终端窗口中，通过 `cd` 转至 `azure-cosmosdb-cassandra-java-getting-started-v4` 文件夹。

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started-v4"
    ```

2. 在 git 终端窗口中，使用以下命令生成 `cosmosdb-cassandra-examples.jar` 文件。

    ```git
    mvn clean install
    ```

3. 在 git 终端窗口中，运行以下命令以启动 Java 应用程序。

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    终端窗口会显示通知，指出密钥空间和表已创建。 然后，它会选择并返回表中的所有用户，然后显示输出，接着按 ID 选择行并显示值。  

    按 Ctrl+C 停止执行程序并关闭控制台窗口。

4. 在 Azure 门户中，打开数据资源管理器****，以查询、修改和处理这些新数据。 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="在 Azure 门户的“连接字符串”页面中查看并复制用户名":::

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Cassandra API 创建 Azure Cosmos DB 帐户，以及如何运行用于创建 Cassandra 数据库和容器的 Cassandra Java 应用。 现在可以将其他数据导入 Azure Cosmos DB 帐户了。 

> [!div class="nextstepaction"]
> [将 Cassandra 数据导入 Azure Cosmos DB](cassandra-import-data.md)
