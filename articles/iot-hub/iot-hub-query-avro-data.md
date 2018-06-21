---
title: 使用 Azure Data Lake Analytics 查询 Avro 数据 |Microsoft Docs
description: 使用消息正文属性将设备遥测数据路由到 blob 存储并查询写入到 blob 存储的 Avro 格式数据。
services: iot-hub
documentationcenter: ''
author: ksaye
manager: obloch
ms.service: iot-hub
ms.topic: article
ms.date: 05/29/2018
ms.author: Kevin.Saye
ms.openlocfilehash: 98a30155c73a937042b4bea6568543fb5152d748
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726613"
---
# <a name="query-avro-data-using-azure-data-lake-analytics"></a>使用 Azure Data Lake Analytics 查询 Avro 数据

本文介绍了如何查询 Avro 数据以便高效地将消息从 Azure IoT 中心路由到 Azure 服务。 从博客文章公告—[Azure IoT Hub message routing: now with routing on message body]（Azure IoT 中心消息路由：现在可基于消息正文进行路由了）来看，IoT 中心支持基于属性或消息正文进行路由。 另请参阅[基于消息正文进行路由][Routing on message bodies]。 

挑战在于，当 Azure IoT 中心将消息路由到 blob 存储时，IoT 中心以 Avro 格式写入内容，该格式同时包括消息正文和消息属性。 请注意，IoT 中心仅支持以 Avro 数据格式将数据写入到 blob 存储，此格式不用于任何其他终结点。 请参阅[如果使用 Azure 存储容器][When using Azure storage containers]。 虽然 Avro 格式非常适用于数据/消息保留，但是查询数据时很有挑战性。 比较而言，JSON 或 CSV 格式更容易用来查询数据。

若要解决此问题，可以使用许多大数据模式来对数据进行转换和缩放，以应对非关系大数据需求和格式。 其中一种模式是“按查询付费”，Azure Data Lake Analytics (ADLA) 采用的就是这种模式。 它是本文的重点。 虽然可以在 Hadoop 或其他解决方案中轻松执行查询，但 ADLA 通常更适合“按查询付费”方式。 U-SQL 中有一个适用于 Avro 的“提取程序”。 请参阅 [U-SQL Avro 示例]。

## <a name="query-and-export-avro-data-to-a-csv-file"></a>查询 Avro 数据并将其导出到 CSV 文件
本部分将指导你查询 Avro 数据并将其导出到 Azure Blob 存储中的一个 CSV 文件，但是也可以轻松将数据放置在其他存储库或数据存储中。

1. 将 Azure IoT 中心设置为通过使用消息正文中的属性来选择消息来将数据路由到 Azure Blob 存储终结点。

    ![步骤 1a 的屏幕捕获][img-query-avro-data-1a]

    ![步骤 1b 的屏幕捕获][img-query-avro-data-1b]

2. 确保你的设备有属性或消息正文中的编码、内容类型和所需的数据，如产品文档中所述。 当在 Device Explorer 中查看时（见下文），可以验证这些属性是否已正确设置。

    ![步骤 2 的屏幕捕获][img-query-avro-data-2]

3. 设置 Azure Data Lake Store (ADLS) 和 Azure Data Lake Analytics 实例。 虽然 Azure IoT 中心不路由到 Azure Data Lake Store，但 ADLA 需要一个该实例。

    ![步骤 3 的屏幕捕获][img-query-avro-data-3]

4. 在 ADLA 中，将 Azure Blob 存储配置为一个附加存储（Azure IoT 中心将数据路由到的同一 Blob 存储）。

    ![步骤 4 的屏幕捕获][img-query-avro-data-4]
 
5. 如 [U-SQL Avro 示例]中所述，需要 4 个 DLL。  将这些文件上传到 ADLS 中的某个位置。

    ![步骤 5 的屏幕捕获][img-query-avro-data-5] 

6. 在 Visual Studio 中，创建一个 U-SQL 项目
 
    ![步骤 6 的屏幕捕获][img-query-avro-data-6]

7. 复制以下脚本的内容并将其粘贴到新创建的文件中。 修改 3 个突出显示的部分：ADLA 帐户、关联的 DLL 的路径，以及存储帐户的正确路径。
    
    ![步骤 7a 的屏幕捕获][img-query-avro-data-7a]

    用于简单输出到 CSV 的实际 U-SQL 脚本：
    
    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
        ""type"":""record"",
        ""name"":""Message"",
        ""namespace"":""Microsoft.Azure.Devices"",
        ""fields"":[{
        ""name"":""EnqueuedTimeUtc"",
        ""type"":""string""
        },
        {
        ""name"":""Properties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""SystemProperties"",
        ""type"":{
        ""type"":""map"",
        ""values"":""string""
        }
        },
        {
        ""name"":""Body"",
        ""type"":[""null"",""bytes""]
        }
        ]
        }");

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```    

    运行下面所示的脚本，当限制为 10 个分析单元时，ADLA 花费了 5 分钟并处理了 177 个文件，将输出汇总到了一个 CSV 文件中。
    
    ![步骤 7b 的屏幕捕获][img-query-avro-data-7b]

    查看输出，可以看到 Avro 内容已转换为 CSV 文件。 如果希望解析 JSON，请继续执行步骤 8。
    
    ![步骤 7c 的屏幕捕获][img-query-avro-data-7c]

    
8. 大多数 IoT 消息采用 JSON 格式。  添加下列行，您可以将消息解析为 JSON，因此你可以添加 WHERE 子句并仅输出所需的数据。

    ```sql
       @jsonify = SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body)) AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                   message["event"] AS msgevent,
                   message["object"] AS msgobject,
                   message["status"] AS msgstatus,
                   message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

9. 查看输出，现在可以看到 select 命令中每个项的列。 
    
    ![步骤 8 的屏幕捕获][img-query-avro-data-8]

## <a name="next-steps"></a>后续步骤
在本教程中，你已学习了如何查询 Avro 数据以便高效地将消息从 Azure IoT 中心路由到 Azure 服务。

若要查看使用 IoT 中心完成端到端解决方案的示例，请参阅 [Azure IoT 远程监视解决方案加速器][lnk-iot-sa-land]。

若要了解有关使用 IoT 中心开发解决方案的详细信息，请参阅 [IoT 中心开发人员指南]。

若要详细了解 IoT 中心的消息路由，请参阅[使用 IoT 中心发送和接收消息][lnk-devguide-messaging]。

<!-- Images -->
[img-query-avro-data-1a]: ./media/iot-hub-query-avro-data/query-avro-data-1a.png
[img-query-avro-data-1b]: ./media/iot-hub-query-avro-data/query-avro-data-1b.png
[img-query-avro-data-2]: ./media/iot-hub-query-avro-data/query-avro-data-2.png
[img-query-avro-data-3]: ./media/iot-hub-query-avro-data/query-avro-data-3.png
[img-query-avro-data-4]: ./media/iot-hub-query-avro-data/query-avro-data-4.png
[img-query-avro-data-5]: ./media/iot-hub-query-avro-data/query-avro-data-5.png
[img-query-avro-data-6]: ./media/iot-hub-query-avro-data/query-avro-data-6.png
[img-query-avro-data-7a]: ./media/iot-hub-query-avro-data/query-avro-data-7a.png
[img-query-avro-data-7b]: ./media/iot-hub-query-avro-data/query-avro-data-7b.png
[img-query-avro-data-7c]: ./media/iot-hub-query-avro-data/query-avro-data-7c.png
[img-query-avro-data-8]: ./media/iot-hub-query-avro-data/query-avro-data-8.png

<!-- Links -->
[Azure IoT Hub message routing: now with routing on message body]: https://azure.microsoft.com/blog/iot-hub-message-routing-now-with-routing-on-message-body/（Azure IoT 中心消息路由：现在可基于消息正文进行路由了）

[Routing on message bodies]: iot-hub-devguide-query-language.md#routing-on-message-bodies
[When using Azure storage containers]:iot-hub-devguide-endpoints.md#when-using-azure-storage-containers

[U-SQL Avro 示例]:https://github.com/Azure/usql/tree/master/Examples/AvroExamples

[lnk-iot-sa-land]: ../iot-accelerators/index.md
[IoT 中心开发人员指南]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
