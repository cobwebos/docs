---
title: 使用 Azure IoT 中心设备预配服务预配设备 (.NET) | Microsoft Docs
description: 使用 Azure IoT 中心设备预配服务将设备预配到单个 IoT 中心 (.NET)
author: wesmc7777
ms.author: wesmc
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 1d82ccdf85b34416dc630b9fcad969d87cc53ff1
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520660"
---
# <a name="enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>使用 Azure IoT 中心设备预配服务客户端将设备登记到 IoT 中心 (.NET)

前面的教程介绍了设置设备以连接到设备预配服务的方法。 本教程介绍如何使用此服务通过**_单独登记_** 和**_登记组_** 将设备预配到单个 IoT 中心。 本教程演示如何：

> [!div class="checklist"]
> * 注册设备
> * 启动设备
> * 验证设备已注册

## <a name="prerequisites"></a>先决条件

继续之前，请确保已配置设备及其“硬件安全模块”（按[使用 Azure IoT 中心设备预配服务设置设备以进行预配](./tutorial-set-up-device.md)教程所述）。

* Visual Studio 2015 或 Visual Studio 2017

> [!NOTE]
> 不需要 Visual Studio。 只需安装 [.NET](https://www.microsoft.com/net) 便已足够，开发人员可以在 Windows 或 Linux 上使用其偏好的编辑器。  

本教程模拟在将设备信息添加到预配服务时，硬件制造过程中或紧接在该过程之后的情况。 此代码通常在电脑上，或者在可以运行 .NET 代码的工厂设备上运行，不应添加到设备本身。


## <a name="enroll-the-device"></a>注册设备

此步骤需要将设备的唯一安全项目添加到设备预配服务。 这些安全项目如下所示：

- 对于基于 TPM 的设备：
    - 对每个 TPM 芯片或模拟唯一的“认可密钥”。 请阅读[了解 TPM 认可密钥](https://technet.microsoft.com/library/cc770443.aspx)获取详细信息。
    - 注册 ID，用于在命名空间/作用域内唯一标识设备。 可能与设备 ID 相同或不同。 此 ID 是每台设备的必备项。 对于基于 TPM 的设备，可能从 TPM 本身派生注册 ID，例如 TPM 认可密钥的 SHA-256 哈希。

- 对于基于 X.509 的设备：
    - [颁发给设备的 X.509 证书](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx)，采用 *.pem* 或 *.cer* 文件格式。 对于单独登记，需要对 X.509 系统使用叶证书；对于登记组，需要使用根证书或同等的签名人证书。
    - 注册 ID，用于在命名空间/作用域内唯一标识设备。 可能与设备 ID 相同或不同。 此 ID 是每台设备的必备项。 对于基于 X.509 的设备，注册 ID 派生自证书的公用名 (CN)。 有关这些要求的详细信息，请参阅[设备概念](https://docs.microsoft.com/azure/iot-dps/concepts-device)。

可通过两种方法向设备预配服务注册设备：

- **单独注册** 表示可使用设备预配服务进行注册的单一设备条目。 单独注册可使用 X.509 证书或 SAS 令牌（在真实或虚拟 TPM 中）作为证明机制。 建议对需要唯一初始配置的设备或仅能通过 TPM 使用 SAS 令牌作为证明机制的设备使用单独登记。 单独注册可能会指定所需 IoT 中心设备 ID。

- **注册组** 表示共享特定证明机制的一组设备。 对于共享所需初始配置的大量设备，或者全部转到同一租户的设备，建议使用注册组。 登记组只使用 X.509，所有组共享其 X.509 证书链中的签名证书。

### <a name="enroll-the-device-using-individual-enrollments"></a>使用单独登记来登记设备

1. 在 Visual Studio 中，使用“控制台应用”项目模板创建一个 Visual C# 控制台应用程序项目。 将项目命名为 **DeviceProvisioning**。
    
1. 在解决方案资源管理器中，右键单击“DeviceProvisioning”项目，然后单击“管理 NuGet 包...”。

1. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **microsoft.azure.devices.provisioning.service**。 选择该项，单击“安装”以安装 **Microsoft.Azure.Devices.Provisioning.Service** 包，并接受使用条款。 此过程会下载、安装 [Azure IoT 设备预配服务 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet 包及其依赖项并添加对它的引用。

1. 在 Program.cs 文件顶部添加以下 `using` 语句：
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. 将以下字段添加到 Program 类。 将占位符值替换为上一部分所述的 DPS 连接字符串。
   
    ```csharp
    static readonly string ServiceConnectionString = "{DPS connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. 添加以下代码，用于执行设备登记：

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. 最后，将以下代码添加到 **Main** 方法，打开与 IoT 中心的连接并开始组登记：
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. 在 Visual Studio 的“解决方案资源管理器”中右键单击解决方案，并单击“设置启动项目...”。选择“单个启动项目”，并在下拉菜单中选择“DeviceProvisioning”项目。  

1. 运行 .NET 设备应用 **DeviceProvisiong**。 该应用应会设置设备预配： 

    ![运行单独注册](./media/tutorial-net-provision-device-to-hub/individual.png)

成功注册后，设备应显示在门户中，如下所示：

   ![门户中的成功登记消息](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>使用登记组来登记设备

> [!NOTE]
> 登记组示例需要 X.509 证书。

1. 在 Visual Studio 的解决方案资源管理器中，打开前面创建的“DeviceProvisioning”项目。 

1. 在 Program.cs 文件顶部添加以下 `using` 语句：
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. 将以下字段添加到 Program 类。 将占位符值替换为 X509 证书位置。
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. 将以下代码添加到 **Program.cs**，用于执行组登记：

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. 最后，在 **Main** 方法中替换以下代码，打开与 IoT 中心的连接并开始登记：
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. 运行 .NET 设备应用 **DeviceProvisiong**。 该应用应会设置设备的组预配： 

    ![运行组注册](./media/tutorial-net-provision-device-to-hub/group.png)

    成功登记设备组后，该组应会显示在门户中，如下所示：

   ![门户中的登记组成功消息](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>启动设备

现在，以下设置已可用于设备注册：

1. 设备或设备组已登记到设备预配服务，并且 
2. 现在可以使用设备预配服务客户端 SDK 通过应用程序在设备中配置安全性并对其进行访问。

启动该设备可让客户端应用程序开始注册到设备预配服务。  


## <a name="verify-the-device-is-registered"></a>验证设备已注册

设备启动后，应进行以下操作。 有关详细信息，请参阅 TPM 模拟器示例应用程序 [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c)。 

1. 设备会向设备预配服务发送注册请求。
2. 对于 TPM 设备，设备预配服务将回复注册质询，设备需对此进行答复。 
3. 注册成功后，设备预配服务会向设备发送 IoT 中心 URI、设备 ID 和加密密钥。 
4. 设备上的 IoT 中心客户端应用程序随后会连接到你的中心。 
5. 成功连接到中心后，设备应出现在 IoT 中心的“Device Explorer”中。 

    ![成功连接到门户中的中心](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 注册设备
> * 启动设备
> * 验证设备已注册

前往下一教程，了解如何跨负载均衡的中心预配多台设备。 

> [!div class="nextstepaction"]
> [跨负载均衡的 IoT 中心预配设备](./tutorial-provision-multiple-hubs.md)
