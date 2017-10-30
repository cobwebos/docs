---
title: "Azure IoT 中心内的 X.509 安全性教程 | Microsoft Docs"
description: "在模拟环境中开始体验 Azure IoT 中心内的基于 X.509 的安全性。"
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: 99fb80d5cafc8fd20f5048de305fef99bf49c286
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2017
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>在 Azure IoT 中心设置 X.509 安全性

本教程模拟使用 *X.509 证书身份验证*保护 Azure IoT 中心所要执行的步骤。 我们将演示如何使用开源工具 OpenSSL 在 Windows 计算机本地创建证书。 我们建议仅将本教程用于测试目的。 对于生产环境，应该从*根证书颁发机构 (CA)* 购买证书。 

## <a name="prerequisites"></a>先决条件
本教程要求准备好以下资源：

- 已使用 Azure 订阅创建一个 IoT 中心。 有关详细步骤，请参阅[通过门户创建 IoT 中心](iot-hub-create-through-portal.md)。 
- 已在计算机上安装 [Visual Studio 2015 或 Visual Studio 2017](https://www.visualstudio.com/vs/)。 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>获取 X.509 CA 证书
IoT 中心基于 X.509 证书的安全性需从 [X.509 证书链](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)开始，其中包括根证书、任何中间证书，及至叶证书。 

可以选择以下方法之一获取证书：
- 从*根证书颁发机构 (CA)* 购买 X.509 证书。 建议在生产环境中使用此方法。
或者，
- 使用 [OpenSSL](https://www.openssl.org/) 等第三方工具创建自己的 X.509 证书。 此方法适用于测试和开发目的。 [如何使用 PowerShell 创建 X.509 证书](iot-hub-security-x509-create-certificates.md)一文中标题为*创建 X.509 证书*和*创建 X.509 证书链*的部分逐步讲解了一个用于通过 OpenSSL 和 PowerShell 创建证书的示例 PowerShell 脚本。 如果希望使用 **Bash** shell（而不是 PowerShell），请参阅[管理 CA 证书示例](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)的相关部分。 本教程的余下部分使用此*操作方法*指南中设置的 OpenSSL 环境来逐步讲解 Azure IoT 中心内的端到端 X.509 安全性。


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>将 X.509 CA 证书注册到 IoT 中心

这些步骤说明如何通过门户将新的证书颁发机构添加到 IoT 中心。

1. 在 Azure 门户中，导航到自己的 IoT 中心并打开“设置” > “证书”菜单。 
2. 单击“添加”以添加新证书。
3. 输入证书的友好显示名称。 从计算机中选择在上一部分创建的名为 *RootCA.cer* 的根证书文件。 单击“上传” 。
4. 收到已成功上传证书的通知后，单击“保存”。

    ![上传证书](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   该证书随即会显示在“证书资源管理器”列表中。 请注意，此证书的“状态”为“未验证”。

5. 单击在上一步骤中添加的证书。

6. 在“证书详细信息”边栏选项卡中，单击“生成验证码”。

7. 随后会创建一个**验证码**用于验证证书所有权。 将此代码复制到剪贴板。 

   ![验证证书](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. 现在，需要使用与 X.509 CA 证书关联的、可生成签名的私钥来为此*验证码*签名。 有一些工具（例如 OpenSSL）可执行此签名过程。 此过程称为[所有权证明](https://tools.ietf.org/html/rfc5280#section-3.1)。 如果在上一部分中使用了示例 PowerShell 脚本，请运行标题为 [X.509 CA 证书的所有权证明](iot-hub-security-x509-create-certificates.md#signverificationcode)的部分中所述的脚本。
 
9. 在门户中将上述步骤 8 生成的签名上传到 IoT 中心。 在 Azure 门户上的“证书详细信息”边栏选项卡中，导航到“验证证书 .pem 或 .cer 文件”，并使用签名旁边的“文件资源管理器”图标选择签名，例如，示例 PowerShell 命令创建的 *VerifyCert4.cer*。

10. 成功上传证书后，单击“验证”。 在“证书”边栏选项卡中，证书的“状态”会更改为“已验证”。 如果状态未自动更新，请单击“刷新”。

   ![上传证书验证](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>为 IoT 中心创建 X.509 设备

1. 在 Azure 门户中导航到 IoT 中心的“Device Explorer”。

2. 单击“添加”以添加新设备。 

3. 为“设备 ID”提供友好显示名称，选择“已签名的 X.509 CA”作为“身份验证类型”。 单击“保存” 。

   ![在门户中创建 X.509 设备](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>使用 X.509 证书对 X.509 设备进行身份验证

若要对 X.509 设备进行身份验证，首先需要使用 CA 证书为该设备签名。 叶设备的签名通常在已相应地启用了制造工具的制造车间完成。 随着设备从一家制造商转移到另一家制造商，每家制造商的签名操作都捕获为链中的中间证书。 最终结果是建立了从 CA 证书到设备叶证书的证书链。 如果在前面的部分中一直使用 PowerShell 脚本，则可以运行[用于管理 CA 签名的 X.509 证书的 PowerShell 脚本](iot-hub-security-x509-create-certificates.md)一文中标题为*创建设备的 X.509 叶证书*的部分中所述的脚本来模拟此过程。

接下来，我们演示如何创建一个 C# 应用程序来模拟针对 IoT 中心注册的 X.509 设备。 我们会将模拟设备提供的温度和湿度值发送到中心。 请注意，本教程只创建设备应用程序。 至于如何创建 IoT 中心服务应用程序用于向此模拟设备发送的事件发送响应，是留给读者的练习。 该 C# 应用程序假设已执行[用于管理 CA 签名的 X.509 证书的 PowerShell 脚本](iot-hub-security-x509-create-certificates.md)一文中所述的 PowerShell 脚本

1. 在 Visual Studio 中，使用“控制台应用程序”项目模板创建新的 Visual C# Windows 经典桌面项目。 将项目命名为 **SimulateX509Device**。
   ![在 Visual Studio 中创建 X.509 设备项目](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. 在解决方案资源管理器中，右键单击“SimulateX509Device”项目，再单击“管理 NuGet 包...”。在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices.client**。 选择“安装”以安装“Microsoft.Azure.Devices.Client”包，并接受使用条款。 此过程会下载、安装 Azure IoT 设备 SDK NuGet 包及其依赖项并添加对它的引用。
   ![在 Visual Studio 中添加设备 SDK NuGet 包](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. 在 *Program.cs* 文件的顶部添加以下代码行：
    
    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. 在 **Program** 类中添加以下代码行：
    
    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```
   将 _< your_device_id >_ 占位符替换为在前一部分中所用的友好设备名。

5. 添加以下函数，以创建温度和湿度的随机数并将这些值发送到中心：
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

6. 最后，将以下代码行添加到 **Main** 函数，并根据设置需要替换占位符 _device-id_、_your-iot-hub-name_ 和 _absolute-path-to-your-device-pfx-file_。
    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "123");
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
7. 由于此应用程序访问 *.pfx* 文件，因此需要以“管理员”模式执行它。 生成 Visual Studio 解决方案。 以**管理员**身份打开新的命令窗口，导航到包含此解决方案的文件夹。 导航到解决方案文件夹中的 *bin/Debug* 路径。 在“管理员”命令窗口中运行应用程序 **SimulateX509Device.exe**。 应会看到，设备已成功连接到中心并在发送事件。 
   ![运行设备应用](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>另请参阅
若要详细了解如何保护 IoT 解决方案，请参阅：

* [IoT 安全最佳实践][lnk-security-best-practices]
* [IoT 安全体系结构][lnk-security-architecture]
* [保护 IoT 部署][lnk-security-deployment]

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 Azure IoT Edge 模拟设备][lnk-iotedge]

[lnk-security-best-practices]: iot-hub-security-best-practices.md
[lnk-security-architecture]: iot-hub-security-architecture.md
[lnk-security-deployment]: iot-hub-security-deployment.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
