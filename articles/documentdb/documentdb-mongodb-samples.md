---
title: "使用 MongoDB API 生成 Cosmos DB 应用 | Microsoft Docs"
description: "使用适用于 MongoDB 的 DocumentDB API 创建联机数据库的教程。"
keywords: "mongodb 示例"
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: d4243fc1400c4e9ad545060f253ae46a579e406f
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>使用 Node.js 生成 Azure Cosmos DB: API for MongoDB 应用
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [用于 MongoDB 的 Node.js](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
>

此示例说明如何使用 Node.js 生成 Azure Cosmos DB: API for MongoDB 控制台应用。

若要使用此示例，必须：

* [创建](documentdb-create-mongodb-account.md) Azure Cosmos DB: API for MongoDB 帐户。
* 检索 MongoDB [连接字符串](documentdb-connect-mongodb-account.md)信息。

## <a name="create-the-app"></a>创建应用程序

1. 创建 app.js 文件，并复制粘贴以下代码。

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';

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

2. 按照帐户设置修改 app.js 文件中的以下变量（了解如何查找[连接字符串](documentdb-connect-mongodb-account.md)）：
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
    ```
     
3. 打开偏爱的终端，运行 **npm install mongodb --save**，然后使用 **node app.js** 运行应用程序

## <a name="next-steps"></a>后续步骤
* 了解如何配合[使用 MongoChef](documentdb-mongodb-mongochef.md) 与 Azure Cosmos DB: API for MongoDB 帐户。

