---
title: 教程 - 在 Azure 流分析作业中运行 Azure Functions
description: 本教程介绍如何将 Azure Functions 配置为流分析作业的输出接收器。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/27/2020
ms.openlocfilehash: 837174b3ccc08a74583587cb9efd34f8f720aec5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77589447"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>教程：从 Azure 流分析作业运行 Azure Functions 

可将 Functions 配置为流分析作业的输出接收器之一，以便通过 Azure 流分析运行 Azure Functions。 Functions 是事件驱动的按需计算体验，它允许实现由 Azure 或第三方服务中出现的事件所触发的代码。 Functions 响应触发的这一功能使其成为流分析作业的自然输出。

流分析通过 HTTP 触发调用 Functions。 通过 Functions 输出适配器，用户可以将 Functions 连接到流分析，以便基于流分析查询触发事件。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建和运行流分析作业
> * 创建用于 Redis 的 Azure 缓存实例
> * 创建 Azure 函数
> * 在用于 Redis 的 Azure 缓存中检查结果

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>创建流分析作业以运行函数 

本部分演示了如何配置流分析作业来运行一个函数以将数据写入到用于 Redis 的 Azure 缓存。 流分析作业从 Azure 事件中心读取事件，并运行调用函数的查询。 此函数从流分析作业读取数据，并将其写入到用于 Redis 的 Azure 缓存中。

![显示各项 Azure 服务间关系的图表](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>创建以事件中心为输入的流分析作业

按照[实时欺诈检测](stream-analytics-real-time-fraud-detection.md)教程以创建事件中心，启动事件生成器应用程序，并创建流分析作业。 跳过创建查询和输出的步骤。 改为按以下各节所述设置 Azure Functions 输出。

## <a name="create-an-azure-cache-for-redis-instance"></a>创建用于 Redis 的 Azure 缓存实例

1. 使用[创建缓存](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)中所述的步骤，在用于 Redis 的 Azure 缓存中创建缓存。  

2. 创建缓存后，在“设置”  下方选择“访问密钥”  。 记下主要连接字符串  。

   ![用于 Redis 的 Azure 缓存连接字符串的屏幕截图](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>在 Azure Functions 中创建可将数据写入到用于 Redis 的 Azure 缓存的函数

1. 请参阅 Functions 文档的[创建函数应用](../azure-functions/functions-create-first-azure-function.md#create-a-function-app)一节。 本部分演示如何使用 CSharp 语言[在 Azure Functions 中创建函数应用和 HTTP 触发的函数](../azure-functions/functions-create-first-azure-function.md#create-function)。  

2. 浏览到 run.csx  函数。 将其更新为以下代码。 将“\<在此处放置用于 Redis 的 Azure 缓存连接字符串\>”  替换为上一节中检索到的用于 Redis 的 Azure 缓存主连接字符串。 

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
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
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

   当流分析从函数收到“HTTP 请求实体过大”异常时，将减小发送到 Azure Functions 的批次的大小。 下面的代码确保流分析不会发送过大的批。 确保函数中使用的最大批次数和最大批次大小值与在流分析门户中输入的值一致。

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. 在所选的文本编辑器中，创建名为 project.json  的 JSON 文件。 粘贴下面的代码，并将其保存在本地计算机上。 此文件包含 C# 函数所需的 NuGet 包依赖项。  
   
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
 
4. 返回到 Azure 门户。 从“平台功能”  选项卡，浏览到你的函数。 在“开发工具”  下方，选择“应用服务编辑器”  。 
 
   ![应用服务编辑器的屏幕截图](./media/stream-analytics-with-azure-functions/image3.png)

5. 在应用服务编辑器中，右键单击根目录，并上传 project.json  文件。 上传成功后，刷新页面。 现在，应可看到名为 project.lock.json  的自动生成文件。 该自动生成文件包含对 project.json 文件中指定 .dll 文件的引用。  

   ![应用服务编辑器的屏幕截图](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>更新流分析作业，以函数作为输出

1. 在 Azure 门户中打开流分析作业。  

2. 浏览到你的函数，并选择“概述”   > “输出”   > “添加”  。 若要添加新的输出，请选择“Azure 函数”  接收器选项。 Functions 输出适配器具有以下属性：  

   |**属性名称**|**说明**|
   |---|---|
   |输出别名| 在作业查询中输入指代输出的用户友好名称。 |
   |导入选项| 可使用当前订阅中的函数；如果函数位于其他订阅中，也可手动提供设置。 |
   |Function App| Functions 应用的名称 |
   |函数| Functions 应用中函数的名称（run.csx 函数的名称）。|
   |最大批大小|设置发送到函数的每个输出批的最大大小（以字节为单位）。 默认情况下，此值设置为 262,144 字节 (256 KB)。|
   |最大批数|指定发送给函数的每个批次中的最大事件数。 默认值为 100。 此属性是可选的。|
   |密钥|可以使用其他订阅中的函数。 提供用于访问你的函数的键值。 此属性是可选的。|

3. 命名输出别名。 在本教程中，我们将其命名为 saop1  ，但你可以使用所选的任何名称。 填写其他详细信息。

4. 打开流分析作业，将查询更新为以下内容。 如果没有将输出接收器命名为 **saop1**，请记住在查询中更改它。  

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

5. 在命令行中运行以下命令，启动 telcodatagen.exe 应用程序。 该命令使用格式 `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`。  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  启动流分析作业。

## <a name="check-azure-cache-for-redis-for-results"></a>在用于 Redis 的 Azure 缓存中检查结果

1. 浏览到 Azure 门户，并查找你的用于 Redis 的 Azure 缓存。 选择“控制台”  。  

2. 使用[用于 Redis 的 Azure 缓存命令](https://redis.io/commands)验证你的数据是否在用于 Redis 的 Azure 缓存中。 （该命令采用 Get {key} 格式。）例如：

   **Get "12/19/2017 21:32:24 - 123414732"**

   此命令应会打印指定键的值：

   ![用于 Redis 的 Azure 缓存输出的屏幕截图](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>错误处理和重试

如果在将事件发送到 Azure Functions 时失败，流分析会重试大多数操作。 将会重试所有 http 异常，直至成功，但 http 错误 413（实体太大）除外。 实体太大错误被视为数据错误，遵循[重试或删除策略](stream-analytics-output-error-policy.md)。

> [!NOTE]
> 从流分析到 Azure Functions 的 HTTP 请求的超时设置为 100 秒。 如果 Azure Functions 应用处理批处理所用的时间超过100 秒，则流分析会出错。

## <a name="known-issues"></a>已知问题

在 Azure 门户中，尝试将最大批次大小/最大批次数值重置为空（默认），值将在保存时改回上次输入的值。 这时，请手动输入这些字段的默认值。

流分析当前不支持在 Azure Functions 上使用 [HTTP 路由](https://docs.microsoft.com/sandbox/functions-recipes/routes?tabs=csharp)。

不支持连接到虚拟网络中托管的 Azure Functions。

## <a name="clean-up-resources"></a>清理资源

若不再需要资源组、流式处理作业以及所有相关资源，请将其删除。 删除作业可避免对作业使用的流单元进行计费。 如果计划在将来使用该作业，可以先停止它，等到以后需要时再重启它。 如果不打算继续使用该作业，请按照以下步骤删除本快速入门创建的所有资源：

1. 在 Azure 门户的左侧菜单中，单击“资源组”  ，并单击已创建资源的名称。  
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。  

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个简单的运行 Azure 函数的流分析作业。 若要详细了解流分析作业，请继续阅读下一教程：

> [!div class="nextstepaction"]
> [在流分析作业中运行 JavaScript 用户定义的函数](stream-analytics-javascript-user-defined-functions.md)
