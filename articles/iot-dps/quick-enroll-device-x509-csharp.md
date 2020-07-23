---
title: 使用 C# 将 X.509 设备注册到 Azure 设备预配服务
description: 本快速入门使用组注册。 本快速入门使用 C# 将 X.509 设备注册到 Azure IoT 中心设备预配服务 (DPS)。
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 64bc3921a606ab3211173b46b268ded53952c8bb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "75434663"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>快速入门：使用 C# 将 X.509 设备注册到设备预配服务

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

本快速入门展示了如何使用 C# 以编程方式创建使用中间或根 CA X.509 证书的[注册组](concepts-service.md#enrollment-group)。 该注册组是使用[用于 .NET 的 Microsoft Azure SDK](https://github.com/Azure/azure-iot-sdk-csharp) 和一个示例 C# .NET Core 应用程序创建的。 注册组可以控制对设备的预配服务的访问，此类设备在其证书链中共享常用签名证书。 若要了解详细信息，请参阅[使用 X.509 证书控制设备对预配服务的访问](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)。 若要详细了解如何将基于 X.509 证书的公钥基础结构 (PKI) 与 Azure IoT 中心和设备预配服务配合使用，请参阅 [X.509 CA 证书安全概述](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview)。 

本快速入门假设你已创建了 IoT 中心和设备预配服务实例。 如果尚未创建这些资源，请先完成[使用 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)快速入门，然后再继续学习本文。

本文使用 Windows 开发计算机，不过，本文中的步骤在 Windows 和 Linux 计算机上均适用。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必备条件

* 安装 [Visual Studio 2019](https://www.visualstudio.com/vs/)。
* 安装 [.NET Core SDK](https://www.microsoft.com/net/download/windows)。
* 安装 [Git](https://git-scm.com/download/)。

## <a name="prepare-test-certificates"></a>准备测试证书

对于本快速入门，必须具有一个包含中间或根 CA X.509 证书的公共部分的 .pem 或.cer 文件。 此证书必须上传到预配服务，并由该服务进行验证。

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 包含的测试工具可以帮助你创建 X.509 证书链、从该链上传根证书或中间证书，以及通过服务执行所有权证明操作，对证书进行验证。

> [!CAUTION]
> 使用 SDK 工具创建的证书只能用于开发测试。
> 请不要在生产环境中使用这些证书。
> 它们包含硬编码的密码（例如 *1234*），在 30 天后过期。
> 若要了解如何获取适用于生产用途的证书，请参阅 Azure IoT 中心文档中的[如何获取 X.509 CA 证书](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate)。
>

若要使用此测试工具来生成证书，请执行以下步骤：

1. 找到[最新版](https://github.com/Azure/azure-iot-sdk-c/releases/latest) Azure IoT C SDK 的标记名称。

2. 打开命令提示符或 Git Bash shell，并切换到计算机上的某个工作文件夹。 运行以下命令，克隆最新版 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 使用在上一步找到的标记作为 `-b` 参数的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    应该预料到此操作需要几分钟才能完成。

   测试工具位于你克隆的存储库的 *azure-iot-sdk-c/tools/CACertificates* 中。

3. 根据[管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步骤进行操作。

除了使用 C SDK 中的工具之外，*用于 .NET 的 Microsoft Azure IoT SDK* 中的[组证书验证示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)演示了如何使用现有的 X.509 中间或根 CA 证书采用 C# 执行所有权证明操作。

## <a name="get-the-connection-string-for-your-provisioning-service"></a>获取适用于预配服务的连接字符串

对于本快速入门中的示例，需要适用于预配服务的连接字符串。

1. 登录到 Azure 门户，选择“所有资源”，然后选择你的设备预配服务。 

1. 选择“共享访问策略”，然后选择需要用来打开其属性的访问策略。  在“访问策略”中，复制并保存主密钥连接字符串。 

    ![从门户获取预配服务连接字符串](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>创建注册组示例 

本部分介绍如何创建一个 .NET Core 控制台应用，用于将注册组添加到预配服务。 进行一些修改后，还可以按这些步骤创建 [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) 控制台应用，以便添加注册组。 若要详细了解如何使用 IoT Core 进行开发，请参阅 [Windows IoT Core developer documentation](https://docs.microsoft.com/windows/iot-core/)（Windows IoT Core 开发人员文档）。

1. 打开 Visual Studio 并选择“创建新项目”  。 在“创建新项目”中，为 C# 项目模板选择“控制台应用(.NET Core)”，然后选择“下一步”。   

1. 将项目命名为 *CreateEnrollmentGroup*，然后按“创建”  。

    ![配置 Visual C# Windows 经典桌面项目](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. 解决方案在 Visual Studio 中打开时，在“解决方案资源管理器”窗格中，右键单击“CreateEnrollmentGroup”项目，然后选择“管理 NuGet 包”    。

1. 在“NuGet 包管理器”中选择“浏览”，搜索并选择“Microsoft.Azure.Devices.Provisioning.Service”，然后按“安装”     。

    ![“NuGet 包管理器”窗口](media//quick-enroll-device-x509-csharp/add-nuget.png)

   此步骤会下载、安装 [Azure IoT 预配服务客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet 包及其依赖项并添加对它的引用。

1. 在 `Program.cs` 顶部的其他 `using` 语句之后添加以下 `using` 语句：

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. 将以下字段添加到 `Program` 类，并按所列内容进行更改。  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * 将 `ProvisioningServiceConnectionString` 占位符值替换为需要为其创建注册的预配服务的连接字符串。

   * 将 `X509RootCertPath` 占位符值替换为 .pem 或 .cer 文件的路径。 此文件代表中间或根 CA X.509 证书的公用部分，而该证书此前已通过预配服务上传和验证。

   * 可以选择性地更改 `EnrollmentGroupId` 值。 字符串只能包含小写字符和连字符。

   > [!IMPORTANT]
   > 在生产代码中，请注意以下安全注意事项：
   >
   > * 为预配服务管理员硬编码连接字符串不符合安全最佳做法。 与硬编码相反，连接字符串应采用安全方式进行存储，例如存储在安全配置文件或注册表中。
   > * 确保只上传签名证书的公用部分。 不要将包含私钥的 .pfx (PKCS12) 或 .pem 文件上传到预配服务。

1. 将以下方法添加到 `Program` 类。 此代码创建一个注册组条目，然后调用 `ProvisioningServiceClient` 中的 `CreateOrUpdateEnrollmentGroupAsync` 方法，将注册组添加到预配服务。

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
           #endregion
 
       }
   }
   ```

1. 最后，将 `Main` 方法的主体替换为以下行：

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. 生成解决方案。

## <a name="run-the-enrollment-group-sample"></a>运行注册组示例
  
运行 Visual Studio 中的示例，创建注册组。 系统将显示命令提示符窗口并开始显示确认消息。 成功创建后，命令提示符窗口会显示新注册组的属性。

可以验证注册组是否已创建。 转到设备预配服务的摘要，依次选择“管理注册”、“注册组”。   此时会看到一个新的注册条目，对应于示例中使用的注册 ID。

![门户中的注册属性](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

选择该条目即可验证证书指纹以及该条目的其他属性。

## <a name="clean-up-resources"></a>清理资源

如果你打算学习 C# 服务示例，请勿清理本快速入门中创建的资源。 否则，请使用以下步骤删除本快速入门创建的所有资源。

1. 关闭计算机上的 C# 示例输出窗口。

1. 在 Azure 门户中导航到设备预配服务，依次选择“管理注册”、“注册组”。   选择使用本快速入门创建的注册项的“注册 ID”，然后按“删除”   。

1. 在 Azure 门户上的设备预配服务中选择“证书”，选择为本快速入门上传的证书，然后按“证书详细信息”顶部的“删除”    。  

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Azure IoT 中心设备预配服务为 X.509 中间或根 CA 证书创建了一个注册组。 若要深入了解设备预配，请继续学习本教程有关如何在 Azure 门户中进行设备预配服务设置的内容。

> [!div class="nextstepaction"]
> [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)
