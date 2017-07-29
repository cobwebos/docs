---
title: "使用 mongoimport 和 mongorestore 将数据导入 Azure Cosmos DB 帐户以与 MongoDB API 配合使用 | Microsoft Docs"
description: "了解如何使用 mongoimport 和 mongorestore 将数据导入到适用于 MongoDB 的 API 帐户"
keywords: "mongoimport，mongorestore"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 3823cfc0696c917a66d41e9dbde4349f635a4011
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017

---

# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB：导入 MongoDB 数据 

若要将数据从 MongoDB 迁移到 Azure Cosmos DB 帐户，以与 MongoDB API 配合使用，必须执行以下操作：

* 从 [MongoDB 下载中心](https://www.mongodb.com/download-center)下载 *mongoimport.exe* 或 *mongorestore.exe*。
* 获取[适用于 MongoDB 的 API 连接字符串](connect-mongodb-account.md)。

若要从 MongoDB 导入数据，并计划将导入数据与 DocumentDB API 配合使用，应使用[数据迁移工具](import-data.md)导入数据。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 检索连接字符串
> * 使用 mongoimport 导入 MongoDB 数据
> * 使用 mongorestore 导入 MongoDB 数据

## <a name="prerequisites"></a>先决条件

* 增加吞吐量：数据迁移的持续时间取决于为集合设置的吞吐量。 请确保对于较大的数据迁移增加吞吐量。 完成迁移后，减少吞吐量以节约成本。 有关在 [Azure 门户](https://portal.azure.com)中增加吞吐量的详细信息，请参阅 [Azure Cosmos DB 中的性能级别和定价层](performance-levels.md)。

* 启用 SSL：Azure Cosmos DB 具有严格的安全要求和标准。 请确保在与帐户进行交互时启用 SSL。 本文的其余部分介绍了如何为 mongoimport 和 mongorestore 启用 SSL。

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>查找连接字符串信息（主机、端口、用户名和密码）

1. 在 [Azure 门户](https://portal.azure.com)的左侧窗格中，单击“Azure Cosmos DB”条目。
2. 在“订阅”窗格中，选择帐户名称。
3. 在“连接字符串”边栏选项卡中，单击“连接字符串”。  
右侧窗格中包含成功连接到帐户所需的全部信息。

    ![“连接字符串”边栏选项卡](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>使用 mongoimport 将数据导入 MongoDB API

若要将数据导入 Azure Cosmos DB 帐户，请使用以下模板。 使用特定于帐户的值填写“主机”、“用户名”和“密码”。  

模板：

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

示例：  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>使用 mongorestore 将数据导入 MongoDB API

若要将数据还原到适用于 MongoDB 的 API 帐户，请使用以下模板执行导入。 使用特定于帐户的值填写“主机”、“用户名”和“密码”。

模板：

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

示例：

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>成功迁移指南

1. 预创建和缩放集合：
        
    * 默认情况下，Azure Cosmos DB 预配有一个包含 1,000 个请求单位 (RU) 的新 MongoDB 集合。 使用 mongoimport、mongorestore 或 mongomirror 开始迁移之前，通过 [Azure 门户](https://portal.azure.com)或 MongoDB 驱动程序和工具预创建所有集合。 如果集合大小超过 10GB，请务必创建包含相应分片键的[分片/分区集合](partition-data.md)。

    * 在 [Azure 门户](https://portal.azure.com)中，仅出于迁移目的，提高集合吞吐量，单分区集合的起始吞吐量为 1,000 个 RU，分片集合的起始吞吐量为 2,500 个 RU。 提高吞吐量后，可避免达到上限，并缩短迁移时间。 由于 Azure Cosmos DB 采用按小时计费，因此可以在迁移后立即降低吞吐量，以节省成本。

2. 计算单文档写入的近似 RU 费用：

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 通过 MongoDB Shell 连接到 Azure Cosmos DB MongoDB 数据库。 有关说明，可以参阅[将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md)。
    
    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 通过 MongoDB Shell 使用示例文档之一运行示例插入命令：
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. 运行 ```db.runCommand({getLastRequestStatistics: 1})```，响应如下所示：
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. 记下请求费用。
    
3. 确定计算机连接 Azure Cosmos DB 云服务的延迟时间：
    
    a.在“横幅徽标”下面，选择“删除上传的徽标”。 运行以下命令，通过 MongoDB Shell 启用详细日志记录：```setVerboseShell(true)```
    
    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 对数据库运行简单查询：```db.coll.find().limit(1)```。 响应如下所示：

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. 迁移前，删除已插入的文档，以确保没有重复文档。 可以运行下列命令来删除文档：```db.coll.remove({})```

5. 计算 batchSize 和 numInsertionWorkers 的近似值：

    * 若要计算 batchSize，请用预配的总 RU 数除以第 3 步中单文档写入所使用的 RU 数。
    
    * 如果计算出的 batchSize <= 24，请将此数字用作 batchSize 值。
    
    * 如果计算出的 batchSize > 24，请将 batchSize 值设置为 24。
    
    * 若要计算 *numInsertionWorkers* ，请使用以下公式：  *numInsertionWorkers =  (预配的吞吐量 * 延迟秒数) / (批大小 * 单文档写入所使用的 RU)*。
        
    |属性|值|
    |--------|-----|
    |batchSize| 24 |
    |预配的 RU 数 | 10000 |
    |延迟 | 0.100 秒 |
    |单文档写入需要支付费用的 RU 数 | 10 RU |
    |numInsertionWorkers | ? |
    
    numInsertionWorkers = (10000 RU x 0.1 秒) / (24 x 10 RU) = 4.1666

6. 运行最终迁移命令：

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>后续步骤

可以继续学习下一教程，了解如何使用 Azure Cosmos DB 查询 MongoDB 数据。 

> [!div class="nextstepaction"]
>[如何查询 MongoDB 数据？](../cosmos-db/tutorial-query-mongodb.md)

