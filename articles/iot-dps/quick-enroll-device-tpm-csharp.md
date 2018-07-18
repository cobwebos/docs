---
title: 使用 C# 将 TPM 设备注册到 Azure 设备预配服务 | Microsoft Docs
description: Azure 快速入门 - 使用 C# 服务 SDK 将 TPM 设备注册到 Azure IoT 中心设备预配服务
author: bryanla
ms.author: bryanla
ms.date: 01/16/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 68ef2c3c13dcf3c193ce1dd5e9b14c73113b5c9b
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971051"
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>使用 C# 服务 SDK 将 TPM 设备注册到 IoT 中心设备预配服务

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]


以下步骤演示了如何使用 [C# 服务 SDK](https://github.com/Azure/azure-iot-sdk-csharp) 和示例 C# .NET Core 应用程序，在 Azure IoT 中心设备预配服务中以编程方式为 TPM 设备创建单个注册。 可以通过该单个注册项，选择性地将模拟的 TPM 设备注册到预配服务。 虽然这些步骤在 Windows 和 Linux 计算机上均适用，但本文使用 Windows 开发计算机。

## <a name="prepare-the-development-environment"></a>准备开发环境

1. 确保已在计算机上安装 [Visual Studio 2017](https://www.visualstudio.com/vs/)。 
2. 确保已在计算机上安装 [.NET Core SDK](https://www.microsoft.com/net/download/windows)。 
3. 在继续操作之前，请确保完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。
4. （可选）如需在本快速入门末尾注册模拟设备，请执行[使用 C# 设备 SDK 创建和预配模拟的 TPM 设备](quick-create-simulated-device-tpm-csharp.md)中的步骤，一直到获取设备的认可密钥那一步。 记下认可密钥和注册 ID，也可以记下设备 ID。稍后需要在本快速入门中使用它们。 **请勿执行通过 Azure 门户创建单个注册的步骤。**

## <a name="get-the-connection-string-for-your-provisioning-service"></a>获取适用于预配服务的连接字符串

对于本快速入门中的示例，需要适用于预配服务的连接字符串。
1. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开设备预配服务。 
2. 单击“共享访问策略”，然后单击需要用来打开其属性的访问策略。 在“访问策略”窗口中，复制并记下主密钥连接字符串。 

    ![从门户获取预配服务连接字符串](media/quick-enroll-device-tpm-csharp/get-service-connection-string.png)

## <a name="create-the-individual-enrollment-sample"></a>创建单个注册示例 

此部分的步骤演示如何创建一个 .NET Core 控制台应用，以便向预配服务添加 TPM 设备的单个注册。 进行一些修改后，还可以按这些步骤创建 [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) 控制台应用，以便添加单个注册。 若要详细了解如何使用 IoT Core 进行开发，请参阅 [Windows IoT Core developer documentation](https://docs.microsoft.com/windows/iot-core/)（Windows IoT Core 开发人员文档）。
1. 在 Visual Studio 中，使用“控制台应用(.NET Core)”项目模板将 Visual C# .NET Core 控制台应用项目添加到新解决方案。 确保 .NET Framework 版本为 4.5.1 或更高。 将项目命名为 **CreateTpmEnrollment**。

    ![新的 Visual C# Windows 经典桌面项目](media//quick-enroll-device-tpm-csharp/create-app.png)

2. 在解决方案资源管理器中，右键单击“CreateTpmEnrollment”项目，然后单击“管理 NuGet 包”。
3. 在“NuGet 包管理器”窗口中，选择“浏览”，搜索 **Microsoft.Azure.Devices.Provisioning.Service**，选择“安装”以安装 **Microsoft.Azure.Devices.Provisioning.Service** 包，然后接受使用条款。 此过程会下载、安装 [Azure IoT 预配服务客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet 包及其依赖项并添加对它的引用。

    ![“NuGet 包管理器”窗口](media//quick-enroll-device-tpm-csharp/add-nuget.png)

4. 在 **Program.cs** 文件顶部其他 `using` 语句之后添加以下 `using` 语句：
   
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```
    
5. 将以下字段添加到 Program 类。  
   - 将 **ProvisioningConnectionString** 占位符值替换为需要为其创建注册的预配服务的连接字符串。
   - 可以选择更改注册 ID、认可密钥、设备 ID 和预配状态。 
   - 如果将此快速入门与[使用 C# 设备 SDK 创建和预配模拟的 TPM 设备](quick-create-simulated-device-tpm-csharp.md)快速入门结合使用来预配模拟设备，请将认可密钥和注册 ID 替换为在该快速入门中记下的值。 可以将设备 ID 替换为在该快速入门中建议的值，可以使用自己的值，也可以使用此示例中的默认值。
        
   ```csharp
   private static string ProvisioningConnectionString = "{Your provisioning service connection string}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```
    
6. 将以下方法添加到 **Program** 类。  此代码创建单个注册条目，然后调用 **ProvisioningServiceClient** 上的 **CreateOrUpdateIndividualEnrollmentAsync** 方法，将单个注册添加到预配服务。
   
   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```
       
7. 最后，将 **Main** 方法的主体替换为以下行：
   
   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```
        
8. 生成解决方案。

## <a name="run-the-individual-enrollment-sample"></a>运行单个注册示例
  
1. 在 Visual Studio 中运行此示例，为 TPM 设备创建单个注册。
 
2. 成功创建以后，命令窗口会显示新的单个注册的属性。

    ![命令输出中的注册属性](media/quick-enroll-device-tpm-csharp/output.png)

3. 若要验证单个注册是否已创建，请在 Azure 门户的设备预配服务摘要边栏选项卡中选择“管理注册”，然后选择“单个注册”选项卡。此时会看到一个新的注册条目，对应于示例中使用的注册 ID。 单击该条目即可验证认可密钥以及该条目的其他属性。

    ![门户中的注册属性](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal.png)
 
4. （可选）如果一直在按[使用 C# 设备 SDK 创建和预配模拟的 TPM 设备](quick-create-simulated-device-tpm-csharp.md)快速入门中的步骤操作，可以继续执行该快速入门中的其余步骤，以便注册模拟设备。 务必跳过使用 Azure 门户创建单个注册的步骤。

## <a name="clean-up-resources"></a>清理资源
如果打算学习 C# 服务示例，请勿清理本快速入门中创建的资源。 如果不打算继续学习，请通过以下步骤删除通过本快速入门创建的所有资源。

1. 关闭计算机上的 C# 示例输出窗口。
2. 在 Azure 门户中导航到设备预配服务，单击“管理注册”，然后选择“单个注册”选项卡。选择通过本快速入门创建的注册项的“注册 ID”，然后单击边栏选项卡顶部的“删除”按钮。 
3. 如果已按照[使用 C# 设备 SDK 创建和预配模拟的 TPM 设备](quick-create-simulated-device-tpm-csharp.md)快速入门中的步骤创建模拟的 TPM 设备，请执行以下操作： 

    1. 关闭 TPM 模拟器窗口以及模拟设备的示例输出窗口。
    2. 在 Azure 门户中，导航到预配了设备的 IoT 中心。 在“资源管理器”下的左侧菜单中，单击“IoT 设备”，选中设备旁边的复选框，然后单击窗口顶部的“删除”。
 
## <a name="next-steps"></a>后续步骤
本快速入门介绍了如何以编程方式为 TPM 设备创建单个注册项，以及如何选择性地在计算机上创建 TPM 模拟设备，并使用 Azure IoT 中心设备预配服务将其预配到 IoT 中心。 若要深入了解设备预配，请继续学习本教程有关如何在 Azure 门户中进行设备预配服务设置的内容。 
 
> [!div class="nextstepaction"]
> [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)

