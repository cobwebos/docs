---
title: "Azure Cosmos DB：如何使用 DocumentDB API 进行查询？ | Microsoft 文档"
description: "了解如何使用 Azure Cosmos DB 的 DocumentDB API 进行查询"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: feffc553a9aa931d96cec71c101674fce08a466b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017

---

# <a name="azure-cosmos-db-how-to-query-with-api-for-mongodb"></a>Azure Cosmos DB：如何使用 MongoDB API 进行查询？

Azure Cosmos DB [MongoDB API](mongodb-introduction.md) 支持 [MongoDB shell 查询](https://docs.mongodb.com/manual/tutorial/query-documents/)。 

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 MongoDB 查询数据

## <a name="sample-document"></a>示例文档

本文中的查询使用下面的示例文档。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a id="examplequery1"></a>示例查询 1 

在上述家庭示例文档中，以下查询返回其 ID 字段匹配 `WakefieldFamily` 的文档。

**查询**
    
    db.families.find({ id: “WakefieldFamily”})

**结果**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a>示例查询 2 

下一个查询返回该家庭中的所有子女。 

**查询**
    
    db.familes.find( { id: “WakefieldFamily” }, { children: true } )

**结果**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a>示例查询 3 

下一个查询返回所有已注册的家庭。 

**查询**
    
    db.families.find( { "isRegistered" : true })
**结果**不返回任何文档。 

## <a id="examplequery4"></a>示例查询 4

下一个查询返回所有未注册的家庭。 

**查询**
    
    db.families.find( { "isRegistered" : false })
**结果**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a>示例查询 5

下一个查询返回所有未注册且所在州为纽约 (NY) 的家庭。 

**查询**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**结果**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a>示例查询 6

下一个查询返回子女读 8 年级的所有家庭。

**查询**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**结果**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a>示例查询 7

下一个查询返回有 3 个子女的家庭。

**查询**
  
      db.Family.find( {children: { $size:3} } )

**结果**

将不返回任何结果，因为子女数不超过 2。 仅当参数为 2 时此查询才能成功，并返回完整的文档。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 已了解如何使用 MongoDB 进行查询 

现在可继续学习下一教程，了解如何全局发布数据。

> [!div class="nextstepaction"]
> [全局分发数据](tutorial-global-distribution-documentdb.md)


