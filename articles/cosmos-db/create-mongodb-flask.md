---
title: 使用 Azure Cosmos DB 的 API for MongoDB 构建 Python Flask Web 应用
description: 演示了一个 Python Flask 代码示例，可以参考该示例使用 Azure Cosmos DB 的用于 MongoDB 的 API 进行连接和查询。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 53d1010f501e59b99c3ba6bac675124cdc5f77a0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659213"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>快速入门：使用 Azure Cosmos DB 的用于 MongoDB 的 API 构建 Python 应用

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

在本快速入门中，我们使用 Mongo DB API 帐户的 Azure Cosmos DB 或 Azure Cosmos DB 模拟器来运行从 GitHub 克隆的 Python Flask To-Do Web 应用。 Azure Cosmos DB 是一种多模型数据库服务，它通过全局分布和水平缩放功能让你快速创建和查询文档、表、键/值和图形数据库。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或者[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 而无需 Azure 订阅。 或者，可以使用 [Azure Cosmos DB 模拟器](local-emulator.md)。 
- [Python 3.6+](https://www.python.org/downloads/)
- 带 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python)的 [Visual Studio Code](https://code.visualstudio.com/Download)。

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，请从 GitHub 克隆 Flask-MongoDB 应用、设置连接字符串并运行。 会看到以编程方式处理数据是多么容易。

1. 打开命令提示符，新建一个名为“git-samples”的文件夹，然后关闭命令提示符。

    ```bash
    md "C:\git-samples"
    ```

2. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为要安装示例应用的新文件夹。

    ```bash
    cd "C:\git-samples"
    ```

3. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. 运行以下命令以安装 python 模块。

    ```bash 
    pip install -r .\requirements.txt
    ```
4. 在 Visual Studio Code 中打开该文件夹。

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何使用代码创建数据库资源，可以查看以下代码片段。 否则，可以跳到[运行 Web 应用](#run-the-web-app)。 

以下代码片段全部摘自 *app.py* 文件，并对本地 Azure Cosmos DB 模拟器使用连接字符串。 密码需要进行拆分（如下所示）以适应在其他方面无法解析的正斜杠。

* 初始化 MongoDB 客户端、检索数据库，并进行身份验证。

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* 检索集合或创建集合（如果不存在）。

    ```python
    todos = db.todo #Select the collection
    ```

* 创建应用

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>运行 Web 应用

1. 确保 Azure Cosmos DB 模拟器正在运行。

2. 打开终端窗口并使用 `cd` 切换到保存应用的目录。

3. 然后，使用 `set FLASK_APP=app.py`、`$env:FLASK_APP = app.py`（适用于 PowerShell 编辑器）或 `export FLASK_APP=app.py`（如果使用的是 Mac）设置 Flask 应用的环境变量。 

4. 使用 `flask run` 运行此应用并浏览到 *http:\//127.0.0.1:5000/* 。

5. 添加和删除任务，并查看集合中添加和更改的任务。

## <a name="create-a-database-account"></a>创建数据库帐户

若要针对实时 Azure Cosmos DB 帐户测试代码，请转到 Azure 门户创建一个帐户。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>更新连接字符串

若要针对实时 Azure Cosmos DB 帐户测试代码，请获取连接字符串信息。 然后将其复制到此应用中。

1. 在 Azure 门户的 Azure Cosmos DB 帐户的左侧导航栏中，选择“连接字符串”，然后选择“读写密钥”。   我们将使用屏幕右侧的复制按钮复制用户名、连接字符串和密码。 

2. 在根目录中打开 app.py 文件  。

3. 从门户中（使用复制按钮）复制“用户名”值，并在 app.py 文件中将其设为“名称”的值    。

4. 然后从门户中复制“连接字符串”值，并在 *app.py* 文件中将其设为 MongoClient 值   。

5. 最后从门户复制“密码”值，并在 app.py 文件中将其设为“密码”的值    。

现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 此时，即可像以前一样运行它。

## <a name="deploy-to-azure"></a>“部署到 Azure”

若要部署此应用，可在 Azure 中创建新的 Web 应用，并使用此 GitHub 存储库分支启用持续部署。 按照此[教程](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment)，在 Azure 中使用 GitHub 设置持续部署。

部署到 Azure 时，应删除应用程序密钥并确保以下部分未被注释掉：

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

然后需要将 MONGOURL、MONGO_PASSWORD 和 MONGO_USERNAME 添加到应用程序设置中。 可参阅此[教程](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings)了解有关 Azure Web 应用中应用程序设置的详细信息。

如果不想创建此存储库的分支，也可选择下方的“部署到 Azure”按钮。  然后应转到 Azure，就可使用 Azure Cosmos DB 的帐户信息设置应用程序设置。

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> 如果打算将代码存储在 GitHub 或其他源代码管理选项中，请务必从代码中删除连接字符串。 可改为使用 Web 应用的应用程序设置进行设置。

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Mongo DB API 帐户的 Azure Cosmos DB，以及如何使用 Azure Cosmos DB 模拟器运行从 GitHub 克隆的 Python Flask To-Do Web 应用。 现在可以将其他数据导入 Azure Cosmos DB 帐户了。 

> [!div class="nextstepaction"]
> [将 MongoDB 数据导入 Azure Cosmos DB](mongodb-migrate.md)
