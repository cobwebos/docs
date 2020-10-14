---
title: 快速入门：将 Cassandra API 与 Python 配合使用 - Azure Cosmos DB
description: 本快速入门介绍如何配合 Python 使用 Azure Cosmos DB 的 Apache Cassandra API 创建配置文件应用程序。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 06e0d3618e96b44eca60f32a0977e7f03d6f5603
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824619"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>快速入门：使用 Python SDK 和 Azure Cosmos DB 构建 Cassandra 应用

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

在本快速入门中，你将创建一个 Azure Cosmos DB Cassandra API 帐户，并使用从 GitHub 克隆的 Cassandra Python 应用创建一个 Cassandra 数据库和一个容器。 Azure Cosmos DB 是一种多模型数据库服务，你可以借助其全球分布和水平缩放功能快速创建和查询文档、表、键/值和图数据库。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 或者[免费试用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 而无需 Azure 订阅。
- [Python 2.7.14+ 或 3.4+](https://www.python.org/downloads/)。
- [Git](https://git-scm.com/downloads)。
- [适用于 Apache Cassandra 的 Python 驱动程序](https://github.com/datastax/python-driver)。

## <a name="create-a-database-account"></a>创建数据库帐户

在创建文档数据库之前，需通过 Azure Cosmos DB 创建 Cassandra 帐户。

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在从 GitHub 克隆 Cassandra API 应用，设置连接字符串，并运行应用。 你会看到以编程方式处理数据是多么容易。 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>查看代码

此步骤是可选的。 如果有意了解如何通过代码创建数据库资源，可以查看以下代码片段。 这些代码片段全部摘自 pyquickstart.py 文件。 否则，可以直接跳转到[更新连接字符串](#update-your-connection-string)。 

* 使用从 Azure 门户检索的 `contactPoint` 和 `port` 信息对 `cluster` 进行初始化。 然后，`cluster` 使用 `connect()` 方法连接到 Azure Cosmos DB Cassandra API。 授权连接是通过使用用户名、密码和默认证书或显式证书（如果你在配置文件中提供了一个）来建立的。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="authenticateAndConnect":::

* 创建新的键空间。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createKeyspace":::

* 创建新表。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createTable":::

* 插入键/值实体。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="insertData":::

* 用于获取所有键值的查询。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryAllItems":::
    
* 用于获取键-值的查询。

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryByID":::

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。 连接字符串使应用能与托管数据库进行通信。

1. 在 [Azure 门户](https://portal.azure.com/)的 Azure Cosmos DB 帐户中，选择“连接字符串”。 

1. 使用屏幕右侧的 :::image type="icon" source="./media/create-cassandra-python/copy.png"::: 按钮复制最上面的值“联系点”。 

   :::image type="content" source="./media/create-cassandra-python/keys.png" alt-text="在 Azure 门户的连接字符串边栏选项卡中查看并复制用户名、密码和联系点":::

1. 打开 config.py 文件。 

1. 粘贴门户中的“联系点”值，并覆盖第 10 行中的 `<FILLME>`。

    第 10 行现在应如下所示 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

1. 复制并粘贴门户中的“用户名”值，并覆盖第 6 行中的 `<FILLME>`。

    第 6 行现在应如下所示 

    `'username': 'cosmos-db-quickstart',`
    
1. 复制并粘贴门户中的“密码”值，并覆盖第 8 行中的 `<FILLME>`。

    第 8 行现在应如下所示

    `'password' = '2Ggkr662ifxz2Mg==`';`

1. 保存 config.py 文件。
    
## <a name="use-the-x509-certificate"></a>使用 X509 证书

1. 从 [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt) 在本地下载 Baltimore CyberTrust 根证书。 使用文件扩展名“.cer”重命名该文件。

   证书的序列号为 `02:00:00:b9`，SHA1 指纹为 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`。

2. 打开 pyquickstart.py，并将 `path\to\cert` 更改为指向新证书。

3. 保存 pyquickstart.py。

## <a name="run-the-python-app"></a>运行 Python 应用

1. 使用 git 终端中的 cd 命令更改到 `azure-cosmos-db-cassandra-python-getting-started` 文件夹。 

2. 运行以下命令安装所需的模块：

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. 运行以下命令启动 Python 应用程序：

    ```
    python pyquickstart.py
    ```

3. 通过命令行验证结果是否符合预期。

    按 CTRL+C 停止执行程序并关闭控制台窗口。 

    :::image type="content" source="./media/create-cassandra-python/output.png" alt-text="在 Azure 门户的连接字符串边栏选项卡中查看并复制用户名、密码和联系点":::
    
4. 在 Azure 门户中，打开数据资源管理器，以查询、修改和处理这些新数据。 

    :::image type="content" source="./media/create-cassandra-python/data-explorer.png" alt-text="在 Azure 门户的连接字符串边栏选项卡中查看并复制用户名、密码和联系点":::

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用 Cassandra API 创建 Azure Cosmos DB 帐户，以及如何运行用于创建 Cassandra 数据库和容器的 Cassandra Python 应用。 现在可以将其他数据导入 Azure Cosmos DB 帐户了。 

> [!div class="nextstepaction"]
> [将 Cassandra 数据导入 Azure Cosmos DB](cassandra-import-data.md)

