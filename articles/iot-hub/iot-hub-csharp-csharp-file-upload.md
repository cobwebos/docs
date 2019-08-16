---
title: 使用 .NET 将文件从设备上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用用于 .NET 的 Azure IoT 设备 SDK 从设备将文件上传到云中。 上传的文件存储在 Azure 存储 Blob 容器中。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: 98b5fb06bf018c9176fc989786d8bd1821914f43
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558476"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>用 IoT 中心 (.NET) 将文件从设备上传到云

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教程基于使用[Iot 中心发送云到设备的消息](iot-hub-csharp-csharp-c2d.md)教程中的代码, 演示如何使用 iot 中心的文件上传功能。 其中了说明了如何：

* 安全提供具有 Azure blob URI 的设备，用于上传文件。

* 使用 IoT 中心文件上传通知触发处理应用后端中的文件。

[从设备将遥测数据发送到 IoT 中心](quickstart-send-telemetry-dotnet.md)快速入门和[使用 IoT 中心发送云到设备的消息](iot-hub-csharp-csharp-c2d.md)教程介绍了 IoT 中心提供的基本的设备到云和云到设备的消息传送功能。 [使用 IoT 中心配置消息路由](tutorial-routing.md)教程介绍了一种在 Microsoft Azure Blob 存储中可靠地存储设备到云消息的方法。 但是, 在某些情况下, 无法轻松地将设备发送的数据映射到 IoT 中心接受的相对较小的设备到云消息。 例如：

* 包含图像的大型文件

* 视频

* 以高频率采样的振动数据

* 某种形式的预处理数据

通常使用 [Azure 数据工厂](../data-factory/introduction.md)或 [Hadoop](../hdinsight/index.yml) 堆栈等工具在云中批处理这些文件。 需要从设备上传文件时，仍可以使用 IoT 中心的安全性和可靠性。

在本教程结束时，会运行 2 个 .NET 控制台应用：

* **SimulatedDevice**。 该应用使用 IoT 中心提供的 SAS URI 将文件上传到存储。 它是在[使用 IoT 中心发送云到设备的消息](iot-hub-csharp-csharp-c2d.md)教程中创建的应用的修改版本。

* **ReadFileUploadNotification**。 此应用接收来自 IoT 中心的文件上传通知。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 Sdk 来支持许多设备平台和语言 (包括 C、Java、Python 和 Javascript)。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot)。

若要完成本教程，需要具备以下先决条件：

* Visual Studio

* 有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>从设备应用上传文件

在本部分中, 会修改在[通过 Iot 中心发送云到设备消息](iot-hub-csharp-csharp-c2d.md)中创建的设备应用, 以接收来自 iot 中心的云到设备消息。

1. 在 Visual Studio 解决方案资源管理器中, 右键单击**SimulatedDevice**项目, 然后选择 "**添加** > **现有项**"。 查找映像文件, 并将其包含在项目中。 本教程假设图像名称为 `image.jpg`。

1. 右键单击该映像, 然后选择 "**属性**"。 确保“**复制到输出目录**”设置为“**始终复制**”。

    ![显示更新“复制到输出目录”图像属性的位置](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. 在 **Program.cs** 文件的顶部添加以下语句：

    ```csharp
    using System.IO;
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync` 方法获取要上传的文件的文件名与流源，并处理上传到存储的任务。 控制台应用会显示上传文件所需的时间。

1. 在**Main**方法中, 在之前`Console.ReadLine()`添加以下行:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> 为简单起见，本教程不实现任何重试策略。 在生产代码中, 应按照[暂时性故障处理](/azure/architecture/best-practices/transient-faults)中的建议, 实现重试策略 (如指数回退)。

## <a name="get-the-iot-hub-connection-string"></a>获取 IoT 中心连接字符串

在本文中, 你将创建一个后端服务, 用于从从[设备发送遥测到 iot 中心](quickstart-send-telemetry-dotnet.md)创建的 IoT 中心接收文件上传通知消息。 若要接收文件上传通知消息, 您的服务需要**服务连接**权限。 默认情况下, 每个 IoT 中心都创建有一个名为 "**服务**" 的共享访问策略, 该策略将授予此权限。

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>接收文件上传通知

在本部分中，会编写一个 .NET 控制台应用，用于接收来自 IoT 中心的文件上传通知消息。

1. 在当前的 Visual Studio 解决方案中, 选择 "**文件** > " "**新建** > **项目**"。 在 "**创建新项目**" 中, 选择 "**控制台应用 (.NET Framework)** ", 然后选择 "**下一步**"。

1. 将项目命名为 *ReadFileUploadNotification*。 在 "**解决方案**" 下, 选择 "**添加到解决方案**"。 选择“创建”来创建项目。

    ![在 Visual Studio 中配置 ReadFileUploadNotification 项目](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. 在解决方案资源管理器中, 右键单击**ReadFileUploadNotification**项目, 然后选择 "**管理 NuGet 包**"。

1. 在**NuGet 包管理器**中, 选择 "**浏览**"。 搜索并选择 " **Microsoft Azure. 设备**", 然后选择 "**安装**"。

    此步骤将在**ReadFileUploadNotification**项目中下载、安装[AZURE IoT 服务 SDK NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Devices/)并添加对它的引用。

1. 在此项目的**Program.cs**文件中, 在文件顶部添加以下语句:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. 将以下字段添加到 Program 类。 将`{iot hub connection string}`占位符值替换为之前在[获取 iot 中心连接字符串](#get-the-iot-hub-connection-string)中复制的 iot 中心连接字符串:

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. 将以下方法添加到 **Program** 类：

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    请注意，此接收模式与用于从设备应用接收云到设备消息的模式相同。

1. 最后，在 **Main** 方法中添加以下行：

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>运行应用程序

现在，已准备就绪，可以运行应用程序了。

1. 在 "解决方案资源管理器" 中, 右键单击解决方案, 然后选择 "**设置启动项目**"。

1. 在 "**通用属性** > " "**启动项目**" 中, 选择 "**多启动项目**", 然后选择 " **ReadFileUploadNotification** " 和 " **SimulatedDevice**" 的**启动**操作。 选择“确定”保存更改。

1. 按 **F5**。 这两个应用程序应该都会启动。 你将在其中一个控制台应用中看到上传已完成，同时还会看到另一个控制台应用收到的上传通知消息。 可使用 [Azure 门户](https://portal.azure.com/)或 Visual Studio 服务器资源管理器检查 Azure 存储帐户中是否存在上传的文件。

    ![显示输出屏幕的屏幕截图](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 你可以继续在以下文章中探索 IoT 中心功能和方案:

* [以编程方式创建 IoT 中心](iot-hub-rm-template-powershell.md)

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)
