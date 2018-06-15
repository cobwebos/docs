---
title: 适用于 Azure Cosmos DB 的 C++ 教程 | Microsoft Docs
description: 本 C++ 教程使用 Azure Cosmos DB 认可的 C++ SDK 创建 C++ 数据库和控制台应用程序。 Azure Cosmos DB 是全球规模的数据库服务。
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: cpp
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: sngun
ms.openlocfilehash: 0e142eaf4182331e0a5803c54d2cc1284e21b221
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807169"
---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-sql-api"></a>Azure Cosmos DB：适用于 SQL API 的 C++ 控制台应用程序教程
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [用于 MongoDB 的 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

欢迎使用 Azure Cosmos DB SQL API 认可的 C++ SDK 的 C++ 教程！ 完成本教程后，会获得一个可以创建并查询 Azure Cosmos DB 资源（包括 C++ 数据库）的控制台应用程序。

本快速入门涵盖：

* 创建并连接到 Azure Cosmos DB 帐户
* 设置应用程序
* 创建 C++ Azure Cosmos DB 数据库
* 创建集合
* 创建 JSON 文档
* 查询集合
* 替换文档
* 删除文档
* 删除 C++ Azure Cosmos DB 数据库

没有时间？ 不必担心！ 可在 [GitHub](https://github.com/stalker314314/DocumentDBCpp)上获取完整的解决方案。 有关快速说明，请参阅 [Get the complete solution](#GetSolution) （获取完整解决方案）。

现在，让我们开始吧！

## <a name="prerequisites-for-the-c-tutorial"></a>C++ 教程先决条件
请确保具有以下资源：

* 有效的 Azure 帐户。 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)，其中已安装 C++ 语言组件。 如果尚未安装 Visual Studio 2017，可以下载并使用**免费的** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

## <a name="step-1-create-an-azure-cosmos-db-account"></a>步骤 1：创建 Azure Cosmos DB 帐户
让我们创建一个 Azure Cosmos DB 帐户。 如果已有一个可以使用的帐户，可以直接跳到[设置 C++ 应用程序](#SetupC++)。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupC++"></a>步骤 2：设置 C++ 应用程序
1. 打开 Visual Studio，在“文件”菜单中单击“新建”，并单击“项目”。 
2. 在“新建项目”窗口中的“已安装”窗格内，展开“Visual C++”，单击“Win32”，并单击“Win32 控制台应用程序”。 将项目命名为 hellodocumentdb，然后单击“确定”。 
   
    ![“新建项目”向导的屏幕截图](media/sql-api-cpp-get-started/hello.png)
3. Win32 应用程序向导启动后，请单击“完成”。
4. 创建项目后，在“解决方案资源管理器”中右键单击“hellodocumentdb”项目，并单击“管理 NuGet 包”，打开 NuGet 包管理器。 
   
    ![项目菜单中显示“管理 NuGet 包”的屏幕截图](media/sql-api-cpp-get-started/nuget.png)
5. 在“NuGet: hellodocumentdb”选项卡中，单击“浏览”，并搜索 *documentdbcpp*。 在结果中选择“DocumentDbCPP”，如以下屏幕截图中所示：   
   
    ![突出显示了 DocumentDbCpp 包的屏幕截图](media/sql-api-cpp-get-started/cpp.png)
   
    此包将安装对 C++ REST SDK（DocumentDbCPP 的依赖项）的引用。 将包添加到项目后，可以开始编写一些代码。   

## <a id="Config"></a>步骤 3：从 Azure 门户复制 Azure Cosmos DB 数据库的连接详细信息
打开 [Azure 门户](https://portal.azure.com)，导航到创建的 Azure Cosmos DB 帐户。 在下一步骤中，需要使用 Azure 门户中的 URI 和主密钥从 C++ 代码片段建立连接。 

![Azure 门户中的 Azure Cosmos DB URI 和密钥](media/sql-api-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>步骤 4：连接到 Azure Cosmos DB 帐户
1. 在源代码中的 `#include "stdafx.h"` 后面添加以下标头和命名空间。
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. 接下来，将以下代码添加到 main 函数，并根据步骤 3 中的 Azure Cosmos DB 设置替换帐户配置和主密钥。 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    现已获得用于初始化客户端的代码，接下来让我们看看如何使用 Azure Cosmos DB 资源。

## <a id="CreateDBColl"></a>步骤 5：创建 C++ 数据库和集合
在执行此步骤之前，让我们为 Azure Cosmos DB 的新手回顾一下数据库、集合与文档之间的交互方式。 [数据库](sql-api-resources.md#databases)是跨集合分区的文档存储的逻辑容器。 [集合](sql-api-resources.md#collections)是 JSON 文档和相关联的 JavaScript 应用程序逻辑的容器。 可以在 [Azure Cosmos DB 分层资源模型和概念](sql-api-resources.md)中详细了解 Azure Cosmos DB 分层资源模型和概念。

要创建数据库和相应的集合，请将以下代码添加到 main 函数的末尾。 这会使用上一步骤中声明的客户端配置创建名为“FamilyRegistry”的数据库和名为“FamilyCollection”的集合。

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>步骤 6：创建文档
[文档](sql-api-resources.md#documents)是用户定义的（任意）JSON 内容。 现在，可以将文档插入 Azure Cosmos DB 中。 将以下代码复制到 main 函数的末尾即可创建文档。 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

总而言之，此代码将创建可在 Azure 门户中使用数据资源管理器查询的 Azure Cosmos DB 数据库、集合与文档。 

![C++ 教程 - 演示帐户、数据库、集合和文档之间的层次关系的示意图](media/sql-api-cpp-get-started/docs.png)

## <a id="QueryDB"></a>步骤 7：查询 Azure Cosmos DB 资源
Azure Cosmos DB 支持对存储在每个集合中的 JSON 文档进行[各种查询](sql-api-sql-query.md)。 以下示例代码演示了一个使用 SQL 语法生成的查询，可以针对上一步骤中创建的文档运行该查询。

函数会提取数据库、集合以及文档客户端的唯一标识符或资源 ID 作为参数。 请在 main 函数的前面添加此代码。

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>步骤 8：替换文档
Azure Cosmos DB 支持替换 JSON 文档，如以下代码中所示。 请 executesimplequery 函数的后面添加此代码。

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>步骤 9：删除文档
Azure Cosmos DB 支持删除 JSON 文档，为此，可以复制以下代码并将其粘贴到 replacedocument 函数的后面。 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>步骤 10：删除数据库
删除已创建的数据库会删除该数据库及其所有子资源（集合、文档等）。

复制以下代码片段（cleanup 函数）并将其粘贴到 deletedocument 函数的后面即可删除数据库和所有子资源。

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>步骤 11：一起运行所有 C++ 应用程序！
现在，我们已添加了用于创建、查询、修改和删除不同 Azure Cosmos DB 资源的代码。  接下来，让我们从 hellodocumentdb.cpp 中的 main 函数添加对这些不同函数的调用，配合一些诊断消息来将这些代码关联到一起。

为此，可将应用程序的 main 函数替换为以下代码。 这会覆写在执行步骤 3 时复制到代码中的 account_configuration_uri 和 primary_key，因此，请保存该代码行，或者再次从门户中复制值。 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

现在，应该可以在 Visual Studio 中按 F5，或者在终端窗口中找到应用程序并运行相应的可执行文件，来生成并运行这些代码。 

应该看到已启动应用的输出。 输出应与以下屏幕截图相符：

![Azure Cosmos DB C++ 应用程序输出](media/sql-api-cpp-get-started/console.png)

祝贺你！ 已完成了 C++ 教程并创建了第一个 Azure Cosmos DB 控制台应用程序！

## <a id="GetSolution"></a>获取完整的 C++ 教程解决方案
若要生成包含本文所有示例的 GetStarted 解决方案，需要做好以下准备：

* [Azure Cosmos DB 帐户][create-account]。
* GitHub 上提供的 [GetStarted](https://github.com/stalker314314/DocumentDBCpp) 解决方案。

## <a name="next-steps"></a>后续步骤
* 了解如何[监视 Azure Cosmos DB 帐户](monitor-accounts.md)。
* 在[查询板块](https://www.documentdb.com/sql/demo)中针对示例数据集运行查询。
* 在 [Azure Cosmos DB 文档页](https://azure.microsoft.com/documentation/services/cosmos-db/)的“Develop”（开发）部分中了解有关编程模型的详细信息。

[create-account]: create-sql-api-dotnet.md#create-account


