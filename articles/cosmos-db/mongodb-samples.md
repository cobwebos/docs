---
title: 使用 MongoDB API 生成 Cosmos DB 应用 | Microsoft Docs
description: 使用适用于 MongoDB 的 Azure Cosmos DB API 创建联机数据库的教程。
keywords: mongodb 示例
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: bd31656404f11c9676b321e2e40454c33f61e3f5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795187"
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>使用 Node.js 生成 Azure Cosmos DB: API for MongoDB 应用
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [用于 MongoDB 的 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

此示例说明如何使用 Node.js 生成 Azure Cosmos DB: API for MongoDB 控制台应用。

若要使用此示例，必须：

* [创建](create-mongodb-dotnet.md#create-account) Azure Cosmos DB: API for MongoDB 帐户。
* 检索 MongoDB [连接字符串](connect-mongodb-account.md)信息。

## <a name="create-the-app"></a>创建应用程序

1. 创建 app.js 文件，并复制粘贴以下代码。

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **可选**：如果使用的是 MongoDB Node.js 2.2 驱动程序，请替换下面的代码片段：

    原始：

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    应替换为：

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. 按照帐户设置修改 app.js 文件中的以下变量（了解如何查找[连接字符串](connect-mongodb-account.md)）：

    > [!IMPORTANT]
    > MongoDB Node.js 3.0 驱动程序需要在 Cosmos DB 密码中对特殊字符进行编码。 请务必将“=”字符编码为 %3D
    >
    > 示例：密码 jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv== 编码为 jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D
    >
    > MongoDB Node.js 2.2 驱动程序无需在 Cosmos DB 密码中对特殊字符进行编码。
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. 打开偏爱的终端，运行 **npm install mongodb --save**，并使用 **node app.js** 运行应用程序

## <a name="next-steps"></a>后续步骤
* 了解如何配合[使用 MongoChef](mongodb-mongochef.md) 与 Azure Cosmos DB: API for MongoDB 帐户。
