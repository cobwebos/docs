---
title: "使用 Azure 流分析作业运行 Azure Functions | Microsoft Docs"
description: "了解如何将 Azure Functions 配置为流分析作业的输出接收器。"
keywords: "数据输出, 流式处理数据, Azure Function"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: f2f4a8d8cda752dc6ed197b8402119f7cbcaf58f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>运行 Azure Functions 与 Azure 流分析作业 

可将 Functions 配置为流分析作业的输出接收器之一，以便通过 Azure 流分析使用 Azure Functions。 Functions 是事件驱动的按需计算体验，它允许实现由 Azure 或第三方服务中出现的事件所触发的代码。 Functions 响应触发的这一功能使其成为流分析作业的自然输出。

流分析通过 HTTP 触发调用 Functions。 通过 Functions 输出适配器，用户可以将 Functions 连接到流分析，以便基于流分析查询触发事件。 

本教程演示如何使用 [Azure Functions](../azure-functions/functions-overview.md) 将流分析连接到 [Azure Redis 缓存](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md)。 

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>创建流分析作业以运行函数 

本部分演示如何配置流分析作业以运行将数据写入 Azure Redis 缓存的函数。 流分析作业从 Azure 事件中心读取事件，并运行调用函数的查询。 此函数从流分析作业读取数据，并将其写入 Azure Redis 缓存。

![显示各项 Azure 服务间关系的图表](./media/stream-analytics-with-azure-functions/image1.png)

需要执行以下步骤来完成此任务：
* [创建以事件中心为输入的流分析作业](#create-stream-analytics-job-with-event-hub-as-input)  
* [创建 Azure Redis 缓存实例](#create-an-azure-redis-cache)  
* [在 Azure Functions 中创建可将数据写入到 Azure Redis 缓存的函数](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [更新流分析作业，以函数作为输出](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [在 Azure Redis 缓存中检查结果](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>创建以事件中心为输入的流分析作业

按照[实时欺诈检测](stream-analytics-real-time-fraud-detection.md)教程以创建事件中心，启动事件生成器应用程序，并创建流分析作业。 （跳过创建查询和输出的步骤。 改为按以下各节所述设置 Functions 输出。）

## <a name="create-an-azure-redis-cache-instance"></a>创建 Azure Redis 缓存实例

1. 使用[创建缓存](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)中所述的步骤，在 Azure Redis 缓存中创建缓存。  

2. 创建缓存后，在“设置”下方选择“访问密钥”。 记下主要连接字符串。

   ![Azure Redis 缓存连接字符串的屏幕截图](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>在 Azure Functions 中创建可将数据写入到 Azure Redis 缓存的函数

1. 请参阅 Functions 文档的[创建函数应用](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)一节。 该小节演示了如何通过使用 CSharp 语言，[在 Azure Functions 中创建函数应用和 HTTP 触发的函数](../azure-functions/functions-create-first-azure-function.md#create-function)。  

2. 浏览到 run.csx 函数。 将其更新为以下代码。 （请务必将“\<在此处放置 redis 缓存的连接字符串\>”替换为上一节中检索到的 Azure Redis 缓存主连接字符串。）  

   ```csharp
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
    }    

   ```

   当流分析从函数收到“HTTP 请求实体过大”异常时，将减小发送到 Azure Functions 的批次的大小。 在函数中，使用下面的代码检查流分析，确保其不会发送过大的批次。 确保函数中使用的最大批次数和最大批次大小值与在流分析门户中输入的值一致。

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. 在所选的文本编辑器中，创建名为 project.json 的 JSON 文件。 使用下面的代码，将其保存在本地计算机上。 此文件包含 C# 函数所需的 NuGet 包依赖项。  
   
   ```json
       {
         "frameworks": {
             "net46": {
                 "dependencies": {
                     "StackExchange.Redis":"1.1.603",
                     "Newtonsoft.Json": "9.0.1"
                 }
             }
         }
     }

   ```
 
4. 返回到 Azure 门户。 从“平台功能”选项卡，浏览到你的函数。 在“开发工具”下方，选择“应用服务编辑器”。 
 
   ![应用服务编辑器的屏幕截图](./media/stream-analytics-with-azure-functions/image3.png)

5. 在应用服务编辑器中，右键单击根目录，并上传 project.json 文件。 上传成功后，刷新页面。 现在，应可看到名为 project.lock.json 的自动生成文件。 该自动生成文件包含对 project.json 文件中指定 .dll 文件的引用。  

   ![应用服务编辑器的屏幕截图](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>更新流分析作业，以函数作为输出

1. 在 Azure 门户中打开流分析作业。  

2. 浏览到你的函数，并选择“概述” > “输出” > “添加”。 若要添加新的输出，请选择“Azure 函数”接收器选项。 新的 Functions 输出适配器现可使用，它具有以下属性：  

   |**属性名称**|**说明**|
   |---|---|
   |输出别名| 在作业查询中输入指代输出的用户友好名称。 |
   |导入选项| 可使用当前订阅中的函数；如果函数位于其他订阅中，也可手动提供设置。 |
   |Function App| Functions 应用的名称 |
   |函数| Functions 应用中函数的名称（run.csx 函数的名称）。|
   |最大批大小|设置每个输出批的最大大小，此值将发送到你的函数。 默认情况下，此值设置为 256 KB。|
   |最大批数|指定发送给函数的每个批次中的最大事件数。 默认值为 100。 此属性是可选的。|
   |密钥|可以使用其他订阅中的函数。 提供用于访问你的函数的键值。 此属性是可选的。|

3. 命名输出别名。 在本教程中，我们将其命名为 saop1（可以使用你选择的任何名称）。 填写其他详细信息。  

4. 打开流分析作业，将查询更新为以下内容。 （如果已将输出接收器命名为其他名称，请务必替换“saop1”文本。）  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. 在命令行运行以下命令，启动 telcodatagen.exe 应用程序（使用格式 `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`）：  
   
   **telcodatagen.exe 1000 .2 2**
    
6.  启动流分析作业。

## <a name="check-azure-redis-cache-for-results"></a>在 Azure Redis 缓存中检查结果

1. 浏览到 Azure 门户中，并查找你的 Azure Redis 缓存。 选择“控制台”。  

2. 使用 [Redis 缓存命令](https://redis.io/commands)验证数据在 Redis 缓存中。 （该命令采用 Get {key} 格式。）例如：

   **Get "12/19/2017 21:32:24 - 123414732"**

   此命令应会打印指定键的值：

   ![Azure Redis 缓存输出的屏幕截图](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>已知问题

在 Azure 门户中，尝试将最大批次大小/最大批次数值重置为空（默认），值将在保存时改回上次输入的值。 这时，请手动输入这些字段的默认值。

