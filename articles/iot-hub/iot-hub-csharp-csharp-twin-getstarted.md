---
title: "要开始使用 Azure IoT 中心设备 twins (.NET/.NET) |Microsoft 文档"
description: "如何使用 Azure IoT 中心设备 twins 添加标记，然后使用 IoT 中心查询。 使用 Azure IoT 设备 SDK for.NET 来实现的模拟的设备应用和 Azure IoT 服务 SDK for.NET 才能实现的服务应用程序添加标记，并运行 IoT 中心查询。"
services: iot-hub
documentationcenter: node
author: dsk-2015
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: 6073d594117e69676b753a1e3af25fffa3583a2b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-device-twins-netnet"></a>要开始使用设备 twins (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

在本教程结束时，你将具有这些.NET 控制台应用：

* **CreateDeviceIdentity**，.NET 应用程序将创建一个设备标识和关联的安全密钥将模拟的设备应用程序连接。
* **AddTagsAndQuery**，后者添加标记并查询设备 twins 的.NET 后端应用。
* **ReportConnectivity**，.NET 设备应用也不能模拟将与更早版本，创建的设备标识连接到 IoT 中心的设备中报告其连接状况。

> [!NOTE]
> 文章[Azure IoT Sdk] [ lnk-hub-sdks]提供有关 Azure IoT Sdk 的信息，你可以使用生成设备和后端应用程序。
> 
> 

若要完成本教程需要满足以下要求：

* Visual Studio 2015 或 Visual Studio 2017。
* 有效的 Azure 帐户。 (如果你没有帐户，则可以创建[免费帐户][ lnk-free-trial]几分钟即可完成。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

如果你想要改为以编程方式创建的设备标识，读取中的相应部分[将模拟的设备连接到你使用.NET 的 IoT 中心][ lnk-device-identity-csharp]文章。

## <a name="create-the-service-app"></a>创建服务应用程序
在此部分中，你创建.NET 控制台应用 （使用 C#），将位置元数据添加到与关联设备双向**myDeviceId**。 然后，它查询存储在 IoT 中心选择的设备位于美国、 然后报告移动电话的连接的那些设备 twins。

1. 在 Visual Studio 中，Visual C# Windows 经典桌面将项目添加到当前解决方案通过使用**控制台应用程序**项目模板。 将项目**AddTagsAndQuery**。
   
    ![新的 Visual C# Windows 经典桌面项目][img-createapp]
1. 在解决方案资源管理器，右键单击**AddTagsAndQuery**项目，，然后单击**管理 NuGet 包...**.
1. 在**NuGet 包管理器**窗口中，选择**浏览**并搜索**microsoft.azure.devices**。 选择**安装**安装**Microsoft.Azure.Devices**包，并接受使用条款。 此过程下载、 安装，并添加对引用[Azure IoT 服务 SDK] [ lnk-nuget-service-sdk] NuGet 包和其依赖项。
   
    ![NuGet 包管理器窗口][img-servicenuget]
1. 添加以下`using`语句的顶部**Program.cs**文件：
   
        using Microsoft.Azure.Devices;
1. 添加到以下字段**程序**类。 将占位符值替换为上一节中创建的中心的 IoT 中心连接字符串。
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. 添加以下方法**程序**类：
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    **RegistryManager**类公开与设备 twins 从服务进行交互所需的所有方法。 前面的代码首先初始化**registryManager**对象，然后检索有关设备双向**myDeviceId**，最后会用所需的位置信息的更新其标记。
   
    在更新后，它会执行两个查询： 第一个选择仅设备位于设备 twins **Redmond43**工厂和第二个改进查询以选择设备，还通过移动电话网络连接。
   
    请注意，前面的代码中，在创建时**查询**对象，指定返回的文档的最大数目。 **查询**对象包含**HasMoreResults**布尔值属性，你可以使用它来调用**GetNextAsTwinAsync**多次来检索所有结果的方法。 调用的方法， **GetNextAsJson**可用于的结果不设备 twins，例如，聚合查询的结果。
1. 最后，添加以下行**Main**方法：
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. 在解决方案资源管理器，打开**设置启动项目...**并确保**操作**为**AddTagsAndQuery**项目是**启动**。 生成解决方案。
1. 通过右键单击运行此应用程序**AddTagsAndQuery**项目并选择**调试**后, 跟**启动新实例**。 你应看到在结果中查询询问的另一台设备的所有设备都位于**Redmond43**和都不用于查询，将结果限制为使用移动电话网络的设备。
   
    ![在窗口中的查询结果][img-addtagapp]

在下一部分中，你将创建的设备应用程序报告的连接信息和更改的上一节中的查询结果。

## <a name="create-the-device-app"></a>创建设备应用
在本部分中，你创建的.NET 控制台应用程序连接到你的中心作为**myDeviceId**，然后更新其报告的属性，以包含该连接使用移动电话网络的信息。

1. 在 Visual Studio 中，Visual C# Windows 经典桌面将项目添加到当前解决方案通过使用**控制台应用程序**项目模板。 将项目**ReportConnectivity**。
   
    ![新的 Visual C# Windows 经典设备应用][img-createdeviceapp]
    
1. 在解决方案资源管理器，右键单击**ReportConnectivity**项目，，然后单击**管理 NuGet 包...**.
1. 在**NuGet 包管理器**窗口中，选择**浏览**并搜索**microsoft.azure.devices.client**。 选择**安装**安装**Microsoft.Azure.Devices.Client**包，并接受使用条款。 此过程下载、 安装，并添加对引用[Azure IoT 设备 SDK] [ lnk-nuget-client-sdk] NuGet 包和其依赖项。
   
    ![NuGet 包管理器窗口客户端应用程序][img-clientnuget]
1. 添加以下`using`语句的顶部**Program.cs**文件：
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. 添加到以下字段**程序**类。 将占位符值替换为上一节中所述的设备连接字符串。
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. 添加以下方法**程序**类：

       public static async void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
                Console.WriteLine("Retrieving twin");
                await Client.GetTwinAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    **客户端**对象公开所需具有从设备的设备 twins 交互的所有方法。 如上所示的代码初始化**客户端**对象，，然后检索有关设备双向**myDeviceId**。

1. 添加以下方法**程序**类：
   
        public static async void ReportConnectivity()
        {
            try
            {
                Console.WriteLine("Sending connectivity data as reported property");
                
                TwinCollection reportedProperties, connectivity;
                reportedProperties = new TwinCollection();
                connectivity = new TwinCollection();
                connectivity["type"] = "cellular";
                reportedProperties["connectivity"] = connectivity;
                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

   更新上面的代码**myDeviceId**的报告的连接信息的属性。

1. 最后，添加以下行**Main**方法：
   
       try
       {
            InitClient();
            ReportConnectivity();
       }
       catch (Exception ex)
       {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
       }
       Console.WriteLine("Press Enter to exit.");
       Console.ReadLine();

1. 在解决方案资源管理器，打开**设置启动项目...**并确保**操作**为**ReportConnectivity**项目是**启动**。 生成解决方案。
1. 通过右键单击运行此应用程序**ReportConnectivity**项目并选择**调试**后, 跟**启动新实例**。 获取双向信息，然后将发送的连接应看到它*报告属性*。
   
    ![运行报表连接到的设备应用][img-rundeviceapp]
    
    
1. 现在，该设备将报告其连接性信息，它应出现在两个查询。 运行.NET **AddTagsAndQuery**应用程序以重新运行查询。 这一次**myDeviceId**应出现在这两个查询结果。
   
    ![已成功报告的设备连接][img-tagappsuccess]

## <a name="next-steps"></a>后续步骤
在本教程中，可以在 Azure 门户中，配置新的 IoT 中心，然后在 IoT 中心标识注册表中创建设备标识。 你添加设备元数据标记为从后端应用程序，并模拟的设备应用程序写入报告设备双向设备连接的信息。 你还了解了如何查询使用类似 SQL 的 IoT 中心查询语言的此信息。

使用以下资源可了解如何：

* 从使用的设备将遥测发送[入门 IoT 中心][ lnk-iothub-getstarted]教程中，
* 配置设备使用设备双向具有所需的属性[使用所需的属性来配置设备][ lnk-twin-how-to-configure]教程中，
* 使用来控制设备以交互方式 （如打开从用户控制应用风扇）[使用直接方法][ lnk-methods-tutorial]教程。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

