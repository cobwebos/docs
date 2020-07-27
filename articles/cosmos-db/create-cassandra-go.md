---
title: 使用 gocql 客户端构建带有 Azure Cosmos DB Cassandra API 的 Go 应用
description: 本快速入门展示了如何使用 Go 客户端与 Azure Cosmos DB Cassandra API 进行交互
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.openlocfilehash: ba53fb786b1d1f61535168cda2152049a12dfb99
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535505"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>快速入门：使用 `gocql` 客户端构建 Go 应用来管理 Azure Cosmos DB Cassandra API 数据

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> * [Golang](create-cassandra-go.md)
>

Azure Cosmos DB 是一种多模型数据库服务，你可以借助其全球分布和水平缩放功能快速创建和查询文档、表、键/值和图数据库。 在本快速入门中，你将首先创建一个 Azure Cosmos DB Cassandra API 帐户。 然后，你将运行一个 Go 应用程序来创建 Cassandra 密钥空间、表，并执行一些操作。 此 Go 应用使用 [gocql](https://github.com/gocql/gocql)，这是 Go 语言的 Cassandra 客户端。 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu)。 或者[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu) 而无需 Azure 订阅。
- 在计算机上安装 [Go](https://golang.org/)，并了解 Go 的实践知识。
- [Git](https://git-scm.com/downloads)。

## <a name="create-a-database-account"></a>创建数据库帐户

在创建数据库之前，需通过 Azure Cosmos DB 创建 Cassandra 帐户。

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

首先从 GitHub 克隆应用程序开始。

1. 打开命令提示符，然后创建一个新的名为 `git-samples` 的文件夹。

    ```bash
    md "C:\git-samples"
    ```

2. 打开 git 终端窗口（例如 git bash）。 使用 `cd` 命令转到新文件夹并安装示例应用。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有心了解如何通过代码创建数据库资源，可以查看以下代码片段。 否则，可以跳到[运行应用程序](#run-the-application)

`GetSession` 函数（`utils\utils.go` 的一部分）返回一个 [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session)，该函数用于执行 insert、find 等群集操作。

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

会将 Azure Cosmos DB Cassandra 主机传递给 [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) 函数，以获取 [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) 结构，然后进行配置，使其使用用户名、密码、端口以及合适的 TLS 版本（[HTTPS/SSL/TLS 加密安全要求](https://docs.microsoft.com/azure/cosmos-db/database-security?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database)）

然后从 `main` 函数 (`main.go`) 调用 `GetSession` 函数。

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

以环境变量的形式接受连接信息和凭证（在 `init` 方法中解析）

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

然后使用它在 Azure Cosmos DB 上执行各种操作（`operations\setup.go` 的一部分），并从 `keyspace` 和 `table` 创建开始。

顾名思义，`DropKeySpaceIfExists` 函数只在 `keyspace` 存在时才会将其删除。

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

`CreateKeySpace` 函数用于创建 `keyspace` (`user_profile`)

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

接下来是表创建 (`user`)，它负责 `CreateUserTable` 函数

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

创建密钥空间和表后，调用 CRUD 操作（`operations\crud.go` 的一部分）。 

`InsertUser` 用于创建 `User`。 它使用 [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind) 设置用户信息（ID、名称和城市）作为查询参数

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser` 用于使用特定的用户 ID 搜索用户 (`model\user.go`)，而 [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan) 将用户属性（由 Cassandra 返回）绑定到单个变量（`userid`、`name`、`city`）- 这只是将获得的结果用作搜索查询结果的方法之一

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers` 用于提取所有用户。 [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap) 作为速记方法使用，以 `map` 的片段形式获取用户的所有信息。 将每个 `map` 看作键值对，其中列名称（例如 `user_id`）包含键及其相应的值。

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

使用 `mapToUser` 函数将用户信息的每个 `map` 转换为 `User`，该函数只需从相应的列中提取值并使用它来创建 `User` 结构的实例即可

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>运行应用程序

如前所述，应用程序接受环境变量形式的连接和凭据。 

1. 在 [Azure 门户](https://portal.azure.com/)的 Azure Cosmos DB 帐户中，选择“连接字符串”。 

    :::image type="content" source="./media/create-cassandra-go/copy-username-connection-string-azure-portal.png" alt-text="从 Azure 门户中的连接字符串页面查看和复制详细信息":::

复制以下属性（`CONTACT POINT`、`PORT`、`USERNAME` 和 `PRIMARY PASSWORD`）的值，并将它们设置为相应的环境变量

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

在“终端”窗口中，切换到正确的文件夹。 例如：

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. 在终端中，运行以下命令以启动应用程序。

```shell
go run main.go
```

3. 终端窗口显示各种操作的通知信息，包括密钥空间和表设置、用户创建等。

4. 在 Azure 门户中，打开数据资源管理器，以查询、修改和处理这些新数据。 

    :::image type="content" source="./media/create-cassandra-go/view-data-explorer-go-app.png" alt-text="在数据资源管理器中查看数据 - Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Cassandra API 创建 Azure Cosmos DB 帐户，以及如何运行用于创建 Cassandra 数据库和容器的 Go 应用。 现在可以将其他数据导入 Azure Cosmos DB 帐户了。 

> [!div class="nextstepaction"]
> [将 Cassandra 数据导入 Azure Cosmos DB](cassandra-import-data.md)
