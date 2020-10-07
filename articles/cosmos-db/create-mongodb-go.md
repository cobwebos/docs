---
title: 将 Go 应用程序连接到 Azure Cosmos DB 的 API for MongoDB
description: 本快速入门演示如何将现有 Go 应用程序连接到 Azure Cosmos DB 的 API for MongoDB。
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 0c03c4f163ef36335dacdc3c28340164dcd23fba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "85299188"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>快速入门：将 Go 应用程序连接到 Azure Cosmos DB 的 API for MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB 是一种多模型数据库服务，你可以借助其全球分布和水平缩放功能快速创建和查询文档、表、键/值和图数据库。 本快速入门将使用 Azure Cloud Shell 创建和管理 Azure Cosmos DB 帐户，从 GitHub 克隆现有示例应用程序并将其配置为使用 Azure Cosmos DB。 

示例应用程序是使用 Go 编写的基于命令行的 `todo` 管理工具。 Azure Cosmos DB 的 API for MongoDB [与 MongoDB Wire Protocol 兼容](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction#wire-protocol-compatibility)，因而任何 MongoDB 客户端驱动程序都可以与其连接。 此应用程序使用[适用于 MongoDB 的 Go 驱动程序](https://github.com/mongodb/mongo-go-driver)，其使用方式使该应用程序完全知道数据存储在 Azure Cosmos DB 数据库中。

## <a name="prerequisites"></a>先决条件
- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free)。 或者[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 而无需 Azure 订阅。 你还可以将 [Azure Cosmos DB 模拟器](https://aka.ms/cosmosdb-emulator)与连接字符串 `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` 配合使用。
- 在计算机上安装 [Go](https://golang.org/)，并了解 Go 的实践知识。
- [Git](https://git-scm.com/downloads)。
- [Azure CLI 2.0+](/cli/azure/install-azure-cli)（如果不想使用 Azure Cloud Shell）。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

运行下列命令以克隆示例存储库。

1. 打开命令提示符，新建一个名为 `git-samples` 的文件夹，然后关闭命令提示符。

    ```bash
    mkdir "C:\git-samples"
    ```

2. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为要安装示例应用的新文件夹。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解应用程序的工作原理，可以查看以下代码片段。 否则，可以跳到[运行应用程序](#run-the-application)。 应用程序布局如下所示：

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

以下代码片段全部摘自 `todo.go` 文件。

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>将 Go 应用连接到 Azure Cosmos DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) 封装 Azure Cosmos DB 的连接字符串，该字符串使用环境变量传入（后面一部分会详细介绍）。 连接使用 [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient)（`clientOptions` 实例传递的目标位置）进行初始化。 调用 [`Ping` 函数](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping)以确认连接是否成功（这是一种快速失败策略）

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> 务必使用 [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) 配置，否则会出现以下连接错误：`unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>创建 `todo` 项

若要创建 `todo`，我们将获取 [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) 的句柄，并调用 [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) 函数。 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

传入包含说明和状态（最初设置为 `pending`）的 `Todo` 结构

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>列出 `todo` 项

我们可以根据条件列出 TODO。 随之创建一个 [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) 来封装筛选条件

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) 用于根据筛选器搜索文档，并将结果转换为 `Todo` 的切片

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

最后，信息以表格格式呈现

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>更新 `todo` 项

`todo` 可以基于其 `_id` 进行更新。 [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) 筛选器根据 `_id` 创建，并且会为更新的信息创建另一个筛选器，该信息是这种情况下的新状态（`completed` 或 `pending`）。 最后，通过筛选器和更新后的文档调用 [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) 函数

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>删除 `todo`

`todo` 根据其 `_id` 进行删除，并以 [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) 实例的形式封装。 调用 [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) 以删除文档。

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>构建应用程序

转到在其中克隆应用程序的目录并生成应用程序（使用 `go build`）。

```bash
cd monogdb-go-quickstart
go build -o todo
```

确认应用程序已正确生成。

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>安装 Azure Cosmos DB

### <a name="sign-in-to-azure"></a>登录 Azure

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅 [安装 Azure CLI]。 

如果使用已安装的 Azure CLI，请使用 [az login](/cli/azure/reference-index#az-login) 命令登录到 Azure 订阅，按屏幕说明操作。 如果使用 Azure Cloud Shell，可以跳过此步骤。

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>添加 Azure Cosmos DB 模块

如果使用已安装的 Azure CLI，请运行 `az` 命令，检查是否已安装 `cosmosdb` 组件。 如果 `cosmosdb` 处于基本命令列表中，继续执行下一个命令。 如果使用 Azure Cloud Shell，可以跳过此步骤。

如果 `cosmosdb` 不在基本命令列表中，请重装 [Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az-group-create) 创建[资源组](../azure-resource-manager/management/overview.md)。 Azure 资源组是在其中部署和管理 Azure 资源（例如 Web 应用、数据库和存储帐户）的逻辑容器。 

以下示例在西欧区域中创建资源组。 选择资源组的唯一名称。

如果使用 Azure Cloud Shell，请选择“试用”，按照屏幕提示登录，然后将命令复制到命令提示符中。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

使用 [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) 命令创建 Cosmos 帐户。

在以下命令中，请将 `<cosmosdb-name>` 占位符替换成自己的唯一 Cosmos 帐户名。 此唯一名称将用作 Cosmos DB 终结点 (`https://<cosmosdb-name>.documents.azure.com/`) 的一部分，因此这个名称需要在 Azure 中的所有 Cosmos 帐户中具有唯一性。 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` 参数启用 MongoDB 客户端连接。

创建 Azure Cosmos DB 帐户后，Azure CLI 会显示类似于以下示例的信息： 

> [!NOTE]
> 此示例使用 JSON 作为 Azure CLI 输出格式，此为默认设置。 若要使用其他输出格式，请参阅 [Azure CLI 命令的输出格式](https://docs.microsoft.com/cli/azure/format-output-azure-cli)。

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>检索数据库键

若要连接到 Cosmos 数据库，需要使用数据库密钥。 使用 [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) 命令检索主键。

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI 输出类似于以下示例的信息。 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>配置应用程序 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>将连接字符串、MongoDB 数据库和集合名称导出为环境变量。 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> 由于 Cosmos DB 的要求，`ssl=true` 选项很重要。 有关详细信息，请参阅[连接字符串要求](connect-mongodb-account.md#connection-string-requirements)。
>

对于 `MONGODB_CONNECTION_STRING` 环境变量，请替换 `<COSMOSDB_ACCOUNT_NAME>` 和 `<COSMOSDB_PASSWORD>` 的占位符

1. `<COSMOSDB_ACCOUNT_NAME>`：创建的 Azure Cosmos DB 帐户的名称
2. `<COSMOSDB_PASSWORD>`：上一步中提取的数据库密钥

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

可以为 `MONGODB_DATABASE` 和 `MONGODB_COLLECTION` 选择首选值，也可以将其保留原样。

## <a name="run-the-application"></a>运行应用程序

创建一个 `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

如果成功，则会看到一个输出，其中包含新建文档的 MongoDB `_id`：

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

再创建一个 `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

列出所有 `todo`

```bash
./todo --list all
```

应该会看到刚才以表格格式添加的那些文件

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

若要更新 `todo` 的状态（例如将其更改为 `completed` 状态），请使用 `todo` ID

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

仅列出已完成的 `todo`

```bash
./todo --list completed
```

应该会看到刚刚更新的那一项

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>在数据资源管理器中查看数据

Azure Cosmos DB 中存储的数据可用于在 Azure 门户中查看和查询。

若要查看、查询和处理在上一步骤中创建的用户数据，请在 Web 浏览器中登录到 [Azure 门户](https://portal.azure.com)。

在顶部搜索框中，输入 **Azure Cosmos DB**。 打开 Cosmos 帐户边栏选项卡后，请选择 Cosmos 帐户。 在左侧导航栏中，选择“数据资源管理器”。 在“集合”窗格中展开你的集合，即可查看该集合中的文档，查询数据，甚至可以创建和运行存储过程、触发器与 UDF。 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="数据资源管理器，显示新创建的文档&quot;:::


使用 ID 删除 `todo`

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

列出要确认的 `todo`

```bash
./todo --list all
```

刚刚删除的 `todo` 不应出现

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| &quot;5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已了解了如何使用 Azure Cloud Shell 创建 Azure Cosmos DB MongoDB API 帐户，以及如何创建和运行 Go 命令行应用以管理 `todo`。 现在可以将其他数据导入 Azure Cosmos DB 帐户了。

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](mongodb-migrate.md)
