---
title: Azure IoT 中心内的 X.509 安全性教程 | Microsoft Docs
description: 在模拟环境中开始体验 Azure IoT 中心内的基于 X.509 的安全性。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 32219eeaee7980b685ac3453c6af3beff716abe2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824087"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>在 Azure IoT 中心设置 X.509 安全性

本教程介绍使用 *X.509 证书身份验证*保护 Azure IoT 中心所要执行的步骤。 在演示过程中，我们将使用开源工具 OpenSSL 在 Windows 计算机本地创建证书。 我们建议仅将本教程用于测试目的。 对于生产环境，应该从*根证书颁发机构 (CA)* 购买证书。

## <a name="prerequisites"></a>先决条件

本教程要求准备好以下资源：

* 已使用 Azure 订阅创建一个 IoT 中心。 有关详细步骤，请参阅[通过门户创建 IoT 中心](iot-hub-create-through-portal.md)。

* 已安装 [Visual Studio 2017 或 Visual Studio 2019](https://www.visualstudio.com/vs/)。

## <a name="get-x509-ca-certificates"></a>获取 X.509 CA 证书

IoT 中心基于 X.509 证书的安全性需从 [X.509 证书链](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)开始，其中包括根证书、任何中间证书，及至叶证书。

可以选择以下任一方法获取证书：

* 从*根证书颁发机构 (CA)* 购买 X.509 证书。 建议在生产环境中使用此方法。

* 使用 [OpenSSL](https://www.openssl.org/) 等第三方工具创建自己的 X.509 证书。 此方法适用于测试和开发目的。 有关使用 PowerShell 或 Bash 生成测试 CA 证书的信息，请参阅[管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)。 本教程的其余部分使用按照[管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的说明生成的测试 CA 证书。

* 生成由某个现有根 CA 证书签名的 [X.509 中间 CA 证书](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust)并将其上传到中心。 在上传并验证中间证书后，可以使用它来替代下面提到的根 CA 证书，如下所述。 可以使用诸如 OpenSSL（[openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) 和 [openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)）的工具来生成中间 CA 证书并对其进行签名。

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>将 X.509 CA 证书注册到 IoT 中心

这些步骤说明如何通过门户将新的证书颁发机构添加到 IoT 中心。

1. 在 Azure 门户中，导航到你的 IoT 中心，并选择中心的“设置” **“证书”。**  > 

1. 选择“添加”以添加新证书。

1. 在“证书名称”中输入一个易记的显示名称，并从计算机中选择在上一部分创建的证书文件。

1. 收到已成功上传证书的通知后，选择“保存”。

    ![上传证书](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   该证书将显示在证书列表中，其状态为“未验证”。

1. 选择刚刚添加的证书以显示“证书详细信息”，然后选择“生成验证码”。

   ![验证证书](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. 将“验证码”复制到剪贴板。 稍后要使用它来验证证书所有权。

1. 遵循[管理用于示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的“步骤 3”。  此过程将使用与 X.509 CA 证书关联的、可生成签名的私钥来为验证码签名。 有一些工具（例如 OpenSSL）可执行此签名过程。 此过程称为[所有权证明](https://tools.ietf.org/html/rfc5280#section-3.1)。

1. 在“证书详细信息”中的“验证证书 .pem 或 .cer 文件”下，找到并打开签名文件。 然后选择“验证”。

   证书状态将更改为“已验证”。 如果证书未自动更新，请选择“刷新”。

## <a name="create-an-x509-device-for-your-iot-hub"></a>为 IoT 中心创建 X.509 设备

1. 在 Azure 门户中导航到你的 IoT 中心，然后选择“资源管理器” **“IoT 设备”。**  > 

1. 选择“新建”以添加新设备。

1. 在“设备 ID”中，输入易记的显示名称。 对于“身份验证类型”，请选择“已由 X.509 CA 签名”，然后选择“保存”。

   ![在门户中创建 X.509 设备](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>使用 X.509 证书对 X.509 设备进行身份验证

若要对 X.509 设备进行身份验证，首先需要使用 CA 证书为该设备签名。 叶设备的签名通常在已相应地启用了制造工具的制造车间完成。 随着设备从一家制造商转移到另一家制造商，每家制造商的签名操作都捕获为链中的中间证书。 结果是建立了从 CA 证书到设备叶证书的证书链。 [管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步骤 4 会生成设备证书。

接下来，我们演示如何创建一个 C# 应用程序来模拟针对 IoT 中心注册的 X.509 设备。 我们会将模拟设备提供的温度和湿度值发送到中心。 本教程只创建设备应用程序。 至于如何创建 IoT 中心服务应用程序用于向此模拟设备发送的事件发送响应，是留给读者的练习。 C# 应用程序假定你已按照[管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步骤进行操作。

1. 打开 Visual Studio，选择“创建新项目”，然后选择“控制台应用(.NET Framework)”项目模板。 选择“**下一步**”。

1. 在“配置新项目”中，将项目命名为 **SimulateX509Device**，然后选择“创建”。

   ![在 Visual Studio 中创建 X.509 设备项目](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. 在解决方案资源管理器中，右键单击“SimulateX509Device”项目，然后选择“管理 NuGet 包”。

1. 在“NuGet 包管理器”中，选择“浏览”，然后搜索并选择“Microsoft.Azure.Devices.Client”。 选择“安装”。

   ![在 Visual Studio 中添加设备 SDK NuGet 包](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    此步骤将下载、安装 Azure IoT 设备 SDK NuGet 包及其依赖项并添加对它的引用。

1. 在 `using`Program.cs**文件顶部添加以下** 语句：

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. 将以下字段添加到 **Program** 类：

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    请将 _< your_device_id >_ 替换为在前一部分中所用的易记设备名称。

1. 添加以下函数，以创建温度和湿度的随机数并将这些值发送到中心：

    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. 最后，将以下代码行添加到**Main**函数，并根据安装程序的要求替换占位符_设备 id_、_你的 iot 中心名称_和_绝对路径到设备-pfx 文件_。

    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   此代码通过创建 X.509 设备的连接字符串连接到 IoT 中心。 成功连接后，此代码将温度和湿度事件发送到中心，并等待其响应。

1. 运行应用。 由于此应用程序访问 *.pfx* 文件，因此你可能需要以管理员身份运行此应用。

   1. 生成 Visual Studio 解决方案。

   1. 使用“以管理员身份运行”打开新的命令提示符窗口。  

   1. 导航到包含你的解决方案的文件夹，然后导航到解决方案文件夹中的 *bin/Debug* 路径。

   1. 在命令提示符下运行应用程序 **SimulateX509Device.exe**。

   应会看到，设备已成功连接到中心并在发送事件。

   ![运行设备应用](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>后续步骤

若要详细了解如何保护 IoT 解决方案，请参阅：

* [IoT 安全最佳做法](../iot-fundamentals/iot-security-best-practices.md)

* [IoT 安全体系结构](../iot-fundamentals/iot-security-architecture.md)

* [保护 IoT 部署](../iot-fundamentals/iot-security-deployment.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)
