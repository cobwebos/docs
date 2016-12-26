---
title: "使用 Azure IoT 中心的直接方法 (C#) | Microsoft Docs"
description: "本教程介绍如何使用直接方法"
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 00746fa67292fa6858980e364c88921d60b29460
ms.openlocfilehash: f2af421140f4f6640578a372993c2553f8c590a4


---
# <a name="tutorial-use-direct-methods-c"></a>教程：使用直接方法 (C#)
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

在本教程结束时，你将具有 .NET 和 Node.js 控制台应用程序：

* **CallMethodOnDevice.js**，一个应从后端运行的 .NET 应用，调用模拟设备应用中的方法并显示响应。
* **SimulatedDevice.js**，一个 Node.js 应用，可模拟使用早先创建的设备标识连接到 IoT 中心的设备，并响应通过云调用的方法。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供了各种 Azure IoT SDK 的相关信息，用户可以使用这些 SDK 构建可在设备和解决方案后端上运行的应用程序。
> 
> 

若要完成本教程，需要满足以下条件：

* Microsoft Visual Studio 2015。
* Node.js 版本 0.10.x 或更高版本。
* 有效的 Azure 帐户。 （如果没有帐户，只需花费几分钟就能创建一个[免费帐户][lnk-free-trial]。）

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>创建模拟设备应用程序
在本部分，用户需创建一个 Node.js 控制台应用，用于响应通过后端调用的方法。

1. 新建名为 **simulateddevice**的空文件夹。 在命令提示符下的 **simulateddevice** 文件夹中，使用以下命令创建 package.json 文件。 接受所有默认值：
   
    ```
    npm init
    ```
2. 在 **simulateddevice** 文件夹的命令提示符处，运行下述命令以安装 **azure-iot-device** 和 **azure-iot-device-mqtt** 包：
   
    ```
        npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 在 **SimulatedDevice.js** 文件夹中，利用文本编辑器创建新的 **simulateddevice** 文件。
4. 在 **SimulatedDevice.js** 文件的开头添加以下 `require` 语句：
   
    ```
    'use strict';
   
    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```
5. 添加 **connectionString** 变量，并使用它创建一个设备客户端。 将 **{device connection string}** 替换为在*创建设备标识*部分创建的连接字符串：
   
    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```
6. 添加以下函数，实现设备上的直接方法：
   
    ```
    function onWriteLine(request, response) {
        console.log(request.payload);
   
        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```
7. 打开与 IoT 中心的连接并初始化方法侦听器：
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```
8. 保存并关闭 **SimulatedDevice.js** 文件。

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中，应按 MSDN 文章 [暂时性故障处理][lnk-transient-faults]中所述实施重试策略（例如连接重试）。
> 
> 

## <a name="call-a-direct-method-on-a-device"></a>在设备上调用直接方法
在本部分中，用户需创建一个 .NET 控制台应用，以便调用模拟设备应用中的方法，然后显示响应。

1. 在 Visual Studio 中，使用“ **控制台应用程序** ”项目模板将 Visual C# Windows 经典桌面项目添加到当前解决方案。 确保 .NET Framework 版本为 4.5.1 或更高。 将项目命名为 **CallMethodOnDevice**。
   
    ![新的 Visual C# Windows 经典桌面项目][10]
2. 在“解决方案资源管理器”中，右键单击“CallMethodOnDevice”项目，然后单击“管理 Nuget 包”。
3. 在“Nuget 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices**，选择“安装”以安装 **Microsoft.Azure.Devices** 包，然后接受使用条款。 此过程将下载、安装 [Microsoft Azure IoT Service SDK][lnk-nuget-service-sdk]（Microsoft Azure IoT 服务 SDK）NuGet 包及其依赖项并添加对它的引用。
   
    ![“Nuget 包管理器”窗口][11]

4. 在 **Program.cs** 文件顶部添加以下 `using` 语句：
   
        using System.Threading.Tasks;
        using Microsoft.Azure.Devices;
5. 将以下字段添加到 **Program** 类。 将占位符值替换为在上一部分中为 IoT 中心创建的连接字符串。
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. 将以下方法添加到 **Program** 类：
   
        private static async Task InvokeMethod()
        {
            var methodInvocation = new CloudToDeviceMethod("writeLine") { ResponseTimeout = TimeSpan.FromSeconds(30) };
            methodInvocation.SetPayloadJson("'a line to be written'");

            var response = await serviceClient.InvokeDeviceMethodAsync("myDeviceId", methodInvocation);

            Console.WriteLine("Response status: {0}, payload:", response.Status);
            Console.WriteLine(response.GetPayloadAsJson());
        }
   
    此方法在 `myDeviceId` 设备上调用名为 `writeLine` 的直接方法。 然后，在控制台上写入设备提供的响应。 请注意，如何指定设备响应的超时值。
7. 最后，在 **Main** 方法中添加以下行：
   
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        InvokeMethod().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

## <a name="run-the-applications"></a>运行应用程序
现在，你已准备就绪，可以运行应用程序了。

1. 在 **simulateddevice** 文件夹的命令提示符处运行以下命令，开始侦听向从 IoT 中心发出的方法调用：
   
    ```
    node SimulatedDevice.js
    ```
   
    ![][7]
2. 设备已连接，正在等待方法调用，此时可运行 .NET **CallMethodOnDevice** 应用，调用模拟设备应用中的方法。 此时会看到写入控制台的设备响应。
   
    ![][8]
3. 此时会看到设备通过输出消息对方法进行响应，而调用该方法的应用程序则会显示来自设备的响应：
   
    ![][9]

## <a name="next-steps"></a>后续步骤
在本教程中，你已在 Azure 门户中配置了新的 IoT 中心，然后在 IoT 中心的标识注册表中创建了设备标识。 你已通过此设备标识启用模拟设备应用的相关功能，使之能够响应通过云调用的方法。 你还创建了一个应用，用于调用设备上的方法并显示来自设备的响应。 

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

* [IoT 中心入门]
* [在多台设备上安排作业][lnk-devguide-jobs]

若要了解如何扩展 IoT 解决方案以及在多个设备上计划方法调用，请参阅[计划和广播作业][lnk-tutorial-jobs]教程。

<!-- Images. -->
[7]: ./media/iot-hub-csharp-node-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-csharp-node-direct-methods/netserviceapp.png
[9]: ./media/iot-hub-csharp-node-direct-methods/methods-output.png

[10]: ./media/iot-hub-csharp-node-direct-methods/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-node-direct-methods/create-identity-csharp2.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[IoT 中心入门]: iot-hub-node-node-getstarted.md



<!--HONumber=Nov16_HO5-->


