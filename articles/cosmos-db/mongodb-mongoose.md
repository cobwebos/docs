---
title: "将 Mongoose 框架与 Azure Cosmos DB 配合使用 | Microsoft Docs"
description: "了解如何将 Node.js Mongoose 应用连接到 Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: romitgirdhar
manager: jhubbard
editor: 
ms.assetid: de5eea58-ee7c-4609-b1c9-4af3e61a5883
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: rogirdh
ms.openlocfilehash: fb6db6555171b65767a715c6b4c8ff37f42c94ef
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Azure Cosmos DB：将 Mongoose 框架与 Azure Cosmos DB 配合使用

本教程演示在 Azure Cosmos DB 中存储数据时如何使用 [Mongoose 框架](http://mongoosejs.com/)。 本演练针对 Azure Cosmos DB 使用 MongoDB API。 不熟悉该接口的读者应知道，Mongoose 是 Node.js 中适用于 MongoDB 的对象建模框架，提供简单直接的、基于架构的解决方案来为应用程序数据建模。

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) 版本 v0.10.29 或更高版本。

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

让我们创建一个 Azure Cosmos DB 帐户。 如果已经有想要使用的帐户，可以跳到 [安装 Node.js 应用程序](#SetupNode)。 如果使用 Azure Cosmos DB 模拟器，请遵循 [Azure Cosmos DB 模拟器](local-emulator.md)中的步骤设置该模拟器，并直接跳到[设置 Node.js 应用程序](#SetupNode)。

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>设置 Node.js 应用程序

>[!Note]
> 如果只想演练示例代码而不是设置应用程序本身，请克隆本教程使用的[示例](https://github.com/Azure-Samples/Mongoose_CosmosDB)，并在 Azure Cosmos DB 上生成 Node.js Mongoose 应用程序。

1. 若要在所选的文件夹中创建 Node.js 应用程序，请在 node 命令提示符下运行以下命令。

    ```npm init```

    回答以下问题，然后即可开始创建项目。

1. 将一个新文件添加到该文件夹，并将此文件命名为 ```index.js```。
1. 使用一个 ```npm install``` 选项安装所需的包：
    * Mongoose：```npm install mongoose --save```
    * Dotenv（若要从 .env 文件加载机密）：```npm install dotenv --save```

    >[!Note]
    > ```--save``` 标志将依赖项添加到 package.json 文件。

1. 导入 index.js 文件中的依赖项。
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. 将 Cosmos DB 连接字符串和 Cosmos DB 名称添加到 ```.env``` 文件。

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. 将以下代码添加到 index.js 末尾，以使用 Mongoose 框架连接到 Azure Cosmos DB。
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > 此处的环境变量是使用“dotenv”npm 包以 process.env.{variableName} 加载的。

    连接到 Azure Cosmos DB 后，可以在 Mongoose 中立即开始设置对象模型。

## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>有关将 Mongoose 与 Azure Cosmos DB 配合使用的注意事项

对于创建的每个模型，Mongoose 会在幕后创建新的 MongoDB 集合。 但是，考虑到 Azure Cosmos DB 的按集合计费模型，如果有多个稀疏填充的对象模型，则它可能不是最具成本效益的方式。

本演练介绍上述两种模型。 首先演练如何为每个集合存储一种类型的数据。 这是 Mongoose 的实际行为。

Mongoose 还存在称作[鉴别器](http://mongoosejs.com/docs/discriminators.html)的概念。 鉴别器是架构继承机制。 使用鉴别器可在同一底层 MongoDB 集合的底层创建多个具有重叠架构的模型。

可将各种数据模型存储在同一集合中，然后在查询时使用筛选子句，只提取所需的数据。

### <a name="one-collection-per-object-model"></a>每个对象模型一个集合

默认的 Mongoose 行为是在每次创建对象模型时创建一个 MongoDB 集合。 本部分探讨如何使用适用于 Azure Cosmos DB 的 MongoDB 实现此目的。 当对象模型包含大量数据时，我们建议对 Azure Cosmos DB 使用此方法。 这是 Mongoose 的默认操作模型，因此，如果你熟悉 Mongoose 的话，则可能也熟悉此模型。

1. 再次打开 ```index.js```。

1. 为“Family”创建架构定义。

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. 为“Family”创建对象。

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. 最后，将对象保存到 Azure Cosmos DB。 这会在幕后创建一个集合。

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. 现在，我们创建另一个架构和对象。 这一次，我们要针对家庭成员可能感兴趣的“Vacation Destinations”创建一个架构。
    1. 我们按前面所述创建方案
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. 创建示例对象（可将多个对象添加到此架构）并将其保存。
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. 现在，请转到 Azure 门户，可以看到 Azure Cosmos DB 中创建了两个集合。

    ![Node.js 教程 - Azure 门户的屏幕截图，其中显示 Azure Cosmos DB 帐户，并突出显示了多个集合名称 - Node 数据库][mutiple-coll]

1. 最后，我们从 Azure Cosmos DB 读取数据。 由于我们使用的是默认 Mongoose 操作模型，读取操作与 Mongoose 的其他读取操作相同。

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>使用 Mongoose 鉴别器在单个集合中存储数据

在此方法中，我们使用 [Mongoose 鉴别器](http://mongoosejs.com/docs/discriminators.html)来帮助优化每个 Azure Cosmos DB 集合的成本。 使用鉴别器可以定义区分的“键”，使用该键可以存储、区分和筛选不同的对象模型。

我们将在此处创建一个基对象模型、定义区分键，并将“Family”和“VacationDestinations”作为扩展添加到基模型。

1. 让我们设置基本配置并定义鉴别器键。

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. 接下来，定义通用对象模型

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. 现在定义“Family”模型。 请注意，我们使用的是 ```commonModel.discriminator``` 而不是 ```mongoose.model```。 此外，我们还要将基本配置添加到 mongoose 架构。 因此，此处的 discriminatorKey 是 ```FamilyType```。

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. 同样，让我们添加另一个架构，这一次是为“VacationDestinations”添加的。 此处的 DiscriminatorKey 是 ```VacationDestinationsType```。

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. 最后，创建模型的对象并将其保存。
    1. 将对象添加到“Family”模型。
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. 接下来，将对象添加到“VacationDestinations”模型，并将其保存。
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. 现在，如果返回到 Azure 门户，可以看到只有一个名为 ```alldata``` 的集合，其中包含“Family”和“VacationDestinations”数据。

    ![Node.js 教程 - Azure 门户的屏幕截图，其中显示 Azure Cosmos DB 帐户，并突出显示了集合名称 - Node 数据库][alldata]

1. 另请注意，每个对象有另一个名为 ```__type``` 的属性，可帮助区分两个不同的对象模型。

1. 最后，读取存储在 Azure Cosmos DB 中的数据。 Mongoose 会负责根据模型筛选数据。 因此，在读取数据时，无需执行其他任何操作。 只需指定模型（在本例中为 ```Family_common```），Mongoose 就会根据“DiscriminatorKey”处理筛选。

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

可以看到，Mongoose 鉴别器的用法非常简单。 如果你的某个应用使用 Mongoose 框架，可以根据本教程中所述的方法，让该应用程序在 Azure Cosmos DB 上的 MongoDB API 中正常运行，且无需做出过多的更改。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

在[针对 MongoDB 功能和语法的 MongoDB API 支持](mongodb-feature-support.md)中详细了解 Azure Cosmos DB MongoDB API 支持的 MongoDB 操作、运算符、阶段、命令和选项。

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png