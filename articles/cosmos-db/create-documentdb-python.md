---
title: "Azure Cosmos DB：使用 Python 和 DocumentDB API 生成应用 | Microsoft Docs"
description: "演示一个可以用来连接到 Azure Cosmos DB DocumentDB API 并进行查询的 Python 代码示例"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 51c11be2-af6d-425f-a86a-39cbfe61da29
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 05/13/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: a2059aeb0ac147c896a7306f9530a410974a8e9a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---
# <a name="azure-cosmos-db-build-a-documentdb-api-app-with-python-and-the-azure-portal"></a>Azure Cosmos DB：使用 Python 和 Azure 门户生成 DocumentDB API 应用

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，它们都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。 

本快速入门教程演示如何使用 Azure 门户创建 Azure Cosmos DB 帐户、文档数据库和集合。 然后将生成并运行基于 [DocumentDB Python API](documentdb-sdk-python.md) 构建的控制台应用。

## <a name="prerequisites"></a>先决条件

* 在运行此示例之前，必须具备以下先决条件：
    * 如果尚未安装 Visual Studio 2017，可以下载并使用**免费的** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。
    * 来自 [GitHub](http://microsoft.github.io/PTVS/)的 Python Tools for Visual Studio。 本教程使用的是 Python Tools for VS 2015。
    * 来自 [python.org](https://www.python.org/downloads/release/python-2712/) 的 Python 2.7

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>添加集合

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在，让我们从 github 克隆 DocumentDB API 应用、设置连接字符串，并运行。 会看到以编程方式处理数据是多么容易。 

1. 打开 git 终端窗口（例如 git bash）并使用 `cd` 切换到工作目录。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-python-getting-started.git
    ```  
## <a name="review-the-code"></a>查看代码

让我们快速查看一下应用中发生的情况。 打开 DocumentDBGetStarted.py 文件，会发现以下代码行创建 Azure Cosmos DB 资源。 


* 将对 DocumentClient 进行初始化。

    ```python
    # Initialize the Python DocumentDB client
    client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})
    ```

* 将创建一个新数据库。

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['DOCUMENTDB_DATABASE'] })
    ```

* 将创建一个新集合。

    ```python
    # Create collection options
    options = {
        'offerEnableRUPerMinuteThroughput': True,
        'offerVersion': "V2",
        'offerThroughput': 400
    }

    # Create a collection
    collection = client.CreateCollection(db['_self'], { 'id': config['DOCUMENTDB_COLLECTION'] }, options)
    ```

* 将创建一些文档。

    ```python
    # Create some documents
    document1 = client.CreateDocument(collection['_self'],
        { 
            'id': 'server1',
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': 'some' 
        })
    ```

* 将使用 SQL 执行查询

    ```python
    # Query them in SQL
    query = { 'query': 'SELECT * FROM server s' }    
            
    options = {} 
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryDocuments(collection['_self'], query, options)
    results = list(result_iterable);

    print(results)
    ```

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“密钥”，并单击“读写密钥”。 使用屏幕右侧的复制按钮将 URI 和主密钥复制到下一步的 `DocumentDBGetStarted.py` 文件中。

    ![在 Azure 门户的“密钥”边栏选项卡中查看并复制访问密钥](./media/create-documentdb-dotnet/keys.png)

2. 打开 `DocumentDBGetStarted.py` 文件。 

3. 从门户中复制 URI 值（使用复制按钮），并在 `DocumentDBGetStarted.py` 中将其设为终结点密钥的值。 

    `config.ENDPOINT : "https://FILLME.documents.azure.com"`

4. 然后从门户复制“主密钥”的值，并在 `DocumentDBGetStarted.py` 中将其设为 `config.MASTERKEY` 的值。 现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

    `config.MASTERKEY : "FILLME"`
    
## <a name="run-the-app"></a>运行应用程序
1. 在 Visual Studio 中，右键单击**解决方案资源管理器**中的项目，选择当前 Python 环境，并右键单击。

2. 选择“安装 Python 包”，并键入 **pydocumentdb**

3. 按 F5 运行应用程序。 应用会显示在浏览器中。 

现可返回到数据资源管理器，查看查询、修改和处理此新数据。 

## <a name="review-slas-in-the-azure-portal"></a>在 Azure 门户中查看 SLA

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本快速入门教程在 Azure 门户中创建的所有资源，步骤如下：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，并单击已创建资源的名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门教程中，已了解如何创建 Azure Cosmos DB 帐户、使用数据资源管理器创建集合和运行应用。 现在可以将其他数据导入 Cosmos DB 帐户。 

> [!div class="nextstepaction"]
> [将 DocumentDB API 的数据导入 Azure Cosmos DB](import-data.md)



