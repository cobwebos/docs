---
title: Custom allocation policies with Azure IoT Hub Device Provisioning Service
description: 如何使用 Azure IoT 中心设备预配服务中的自定义分配策略
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 2a17cc6c9f2211de31d4551bd12e6c832d4eee38
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228735"
---
# <a name="how-to-use-custom-allocation-policies"></a>如何使用自定义分配策略

自定义分配策略让你能够对设备分配到 IoT 中心的方式进行更多地控制。 它是通过使用 [Azure 函数](../azure-functions/functions-overview.md)中的自定义代码将设备分配到 IoT 中心来实现的。 设备预配服务将调用 Azure 函数代码，提供有关设备和注册的所有相关信息。 将执行函数代码并返回用于预配设备的 IoT 中心信息。

By using custom allocation policies, you define your own allocation policies when the policies provided by the Device Provisioning Service don't meet the requirements of your scenario.

For example, maybe you want to examine the certificate a device is using during provisioning and assign the device to an IoT hub based on a certificate property. Or, maybe you have information stored in a database for your devices and need to query the database to determine which IoT hub a device should be assigned to.

本文演示使用 C# 编写的 Azure 函数的自定义分配策略。 创建了两个新的 IoT 中心，分别表示 Contoso 烤箱分区和 Contoso 热泵分区。 请求预配的设备必须具有含以下后缀之一的注册 ID 才能被接受进行预配：

* -contoso-tstrsd-007：Contoso 烤箱分区
* -contoso-hpsd-088：Contoso 热泵分区

将基于注册 ID 上这些所需的后缀之一对设备进行预配。 将使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中包含的预配示例对这些设备进行模拟。

You perform the following steps in this article:

* 使用 Azure CLI 创建两个 Contoso 分区 IoT 中心（Contoso 烤箱分区和 Contoso 热泵分区）
* 使用 Azure 函数为自定义分配策略创建新的组注册
* 为两个设备模拟创建设备密钥。
* 为 Azure IoT C SDK 设置开发环境
* Simulate the devices and verify that they are provisioned according to the example code in the custom allocation policy

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必备组件

* 启用了[“使用 C++ 的桌面开发”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷的 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 或更高版本。
* 已安装最新版本的 [Git](https://git-scm.com/download/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>Create the provisioning service and two divisional IoT hubs

In this section, you use the Azure Cloud Shell to create a provisioning service and two IoT hubs representing the **Contoso Toasters Division** and the **Contoso Heat Pumps division**.

> [!TIP]
> The commands used in this article create the provisioning service and other resources in the West US location. We recommend that you create your resources in the region nearest you that supports Device Provisioning Service. 若要查看可用位置的列表，可以运行 `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` 命令，也可以转到[Azure 状态](https://azure.microsoft.com/status/)页，在其中搜索“设备预配服务”。 In commands, locations can be specified either in one word or multi-word format; for example: westus, West US, WEST US, etc. The value is not case sensitive. 如果使用多个单词的格式来指定位置，请将值置于引号中，例如 `-- location "West US"`。
>

1. 在 Azure Cloud Shell 中，使用 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

    The following example creates a resource group named *contoso-us-resource-group* in the *westus* region. 建议对本文中创建的所有资源使用该组。 This approach will make clean up easier after you're finished.

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. Use the Azure Cloud Shell to create a device provisioning service with the [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) command. The provisioning service will be added to *contoso-us-resource-group*.

    The following example creates a provisioning service named *contoso-provisioning-service-1098* in the *westus* location. You must use a unique service name. Make up your own suffix in the service name in place of **1098**.

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    此命令可能需要花费几分钟时间完成。

3. 在 Azure Cloud Shell 中，使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令创建 Contoso 烤箱分区 IoT 中心。 IoT 中心将被添加到 contoso-us-resource-group。

    The following example creates an IoT hub named *contoso-toasters-hub-1098* in the *westus* location. You must use a unique hub name. 在中心名称中的 1098 位置构成你自己的后缀。 自定义分配策略的示例代码要求使用中心名称中的 `-toasters-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    此命令可能需要花费几分钟时间完成。

4. 在 Azure Cloud Shell 中，使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令创建 Contoso 热泵分区 IoT 中心。 此 IoT 中心也将被添加到 contoso-us-resource-group。

    The following example creates an IoT hub named *contoso-heatpumps-hub-1098* in the *westus* location. You must use a unique hub name. 在中心名称中的 1098 位置构成你自己的后缀。 自定义分配策略的示例代码要求使用中心名称中的 `-heatpumps-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    此命令可能需要花费几分钟时间完成。

## <a name="create-the-custom-allocation-function"></a>Create the custom allocation function

In this section, you create an Azure function that implements your custom allocation policy. This function decides which divisional IoT hub a device should be registered to based on whether its registration ID contains the string **-contoso-tstrsd-007** or **-contoso-hpsd-088**. It also sets the initial state of the device twin based on whether the device is a toaster or a heat pump.

1. 登录到 [Azure 门户](https://portal.azure.com)。 From your home page, select **+ Create a resource**.

2. In the *Search the Marketplace* search box, type "Function App". From the drop-down list select **Function App**, and then select **Create**.

3. On **Function App** create page, under the **Basics** tab, enter the following settings for your new function app and select **Review + create**:

    **Resource Group**: Select the **contoso-us-resource-group** to keep all resources created in this article together.

    **Function App name**: Enter a unique function app name. This example uses **contoso-function-app-1098**.

    **Publish**: Verify that **Code** is selected.

    **Runtime Stack**: Select **.NET Core** from the drop-down.

    **Region**: Select the same region as your resource group. This example uses **West US**.

    > [!NOTE]
    > By default, Application Insights is enabled. Application Insights is not necessary for this article, but it might help you understand and investigate any issues you encounter with the custom allocation. If you prefer, you can disable Application Insights by selecting the **Monitoring** tab and then selecting **No** for **Enable Application Insights**.

    ![Create an Azure Function App to host the custom allocation function](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. On the **Summary** page, select **Create** to create the function app. Deployment may take several minutes. When it completes, select **Go to resource**.

5. On the left pane of the function app **Overview** page, select **+** next to **Functions** to add a new function.

    ![Add a function to the Function App](./media/how-to-use-custom-allocation-policies/create-function.png)

6. On the **Azure Functions for .NET - getting started** page, for the **CHOOSE A DEPLOYMENT ENVIRONMENT** step, select the **In-portal** tile, then select **Continue**.

    ![Select the portal development environment](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. On the next page, for the **CREATE A FUNCTION** step, select the **Webhook + API** tile, then select **Create**. A function named **HttpTrigger1** is created, and the portal displays the contents of the **run.csx** code file.

8. Reference required Nuget packages. To create the initial device twin, the custom allocation function uses classes that are defined in two Nuget packages that must be loaded into the hosting environment. With Azure Functions, Nuget packages are referenced using a *function.host* file. In this step, you save and upload a *function.host* file.

    1. Copy the following lines into your favorite editor and save the file on your computer as *function.host*.

        ```xml
        <Project Sdk="Microsoft.NET.Sdk">  
            <PropertyGroup>  
                <TargetFramework>netstandard2.0</TargetFramework>  
            </PropertyGroup>  
            <ItemGroup>  
                <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Service" Version="1.5.0" />  
                <PackageReference Include="Microsoft.Azure.Devices.Shared" Version="1.16.0" />  
            </ItemGroup>  
        </Project>
        ```

    2. On the **HttpTrigger1** function, expand the **View Files** tab on the right side of the window.

        ![Open view files](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. Select **Upload**, browse to the **function.proj** file, and select **Open** to upload the file.

        ![Select upload file](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. Replace the code for the **HttpTrigger1** function with the following code and select **Save**:

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    using Microsoft.Azure.Devices.Shared;               // For TwinCollection
    using Microsoft.Azure.Devices.Provisioning.Service; // For TwinState

    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        // Get request body
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        log.LogInformation("Request.Body:...");
        log.LogInformation(requestBody);

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.LogInformation("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.LogInformation("linkedHubs : NULL");
                fail = true;
            }
            else
            {
                // This is a Contoso Toaster Model 007
                if (regId.Contains("-contoso-tstrsd-007"))
                {
                    //Find the "-toasters-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-toasters-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No toasters hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "toaster";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "ready";
                        properties["darknessSetting"] = "medium";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // This is a Contoso Heat pump Model 008
                else if (regId.Contains("-contoso-hpsd-088"))
                {
                    //Find the "-heatpumps-" IoT hub configured on the enrollment
                    foreach(string hubString in hubs)
                    {
                        if (hubString.Contains("-heatpumps-"))
                            obj.iotHubHostName = hubString;
                    }

                    if (obj.iotHubHostName == null)
                    {
                        message = "No heat pumps hub found for the enrollment.";
                        log.LogInformation(message);
                        fail = true;
                    }
                    else
                    {
                        // Specify the initial tags for the device.
                        TwinCollection tags = new TwinCollection();
                        tags["deviceType"] = "heatpump";

                        // Specify the initial desired properties for the device.
                        TwinCollection properties = new TwinCollection();
                        properties["state"] = "on";
                        properties["temperatureSetting"] = "65";

                        // Add the initial twin state to the response.
                        TwinState twinState = new TwinState(tags, properties);
                        obj.initialTwin = twinState;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.LogInformation("Unknown device registration");
                }
            }
        }

        log.LogInformation("\nResponse");
        log.LogInformation((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message);

        return (fail)
            ? new BadRequestObjectResult(message) 
            : (ActionResult)new OkObjectResult(obj);
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public TwinState initialTwin {get; set;}
    }
    ```

## <a name="create-the-enrollment"></a>创建注册

In this section, you'll create a new enrollment group that uses the custom allocation policy. 为简单起见，本文将在注册中使用[对称密钥证明](concepts-symmetric-key-attestation.md)。 对于更安全的解决方案，请考虑使用具有信任链的 [X.509 证书证明](concepts-security.md#x509-certificates)。

1. Still on the [Azure portal](https://portal.azure.com), open your provisioning service.

2. Select **Manage enrollments** on the left pane, and then select the **Add enrollment group** button at the top of the page.

3. On **Add Enrollment Group**, enter the following information, and select the **Save** button.

    组名称：输入 contoso-custom-allocated-devices。

    证明类型：选择“对称密钥”。

    自动生成密钥：此复选框应已处于选中状态。

    选择要如何将设备分配到中心：选择“自定义(使用 Azure 函数)”。

    ![为对称密钥证明添加自定义分配注册组](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. On **Add Enrollment Group**, select **Link a new IoT hub** to link both of your new divisional IoT hubs.

    Execute this step for both of your divisional IoT hubs.

    订阅：如果你有多个订阅，请选择创建分区 IoT 中心的订阅。

    IoT 中心：选择你创建的分区中心之一。

    访问策略：选择“iothubowner”。

    ![使用预配服务链接分区 IoT 中心](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. 在“添加注册组”上，一旦链接这两个分区 IoT 中心后，必须将其选择为注册组的 IoT 中心组，如下所示：

    ![为注册创建分区中心组](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. On **Add Enrollment Group**, scroll down to the **Select Azure Function** section, select the Function app you created in the previous section. Then select the function you created and select Save to save the enrollment group.

    ![Select the function and save the enrollment group](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. 保存注册后，重新打开它，并记录“主键”。 必须先保存注册，才能生成密钥。 此密钥稍后将用于为模拟设备生成唯一设备密钥。

## <a name="derive-unique-device-keys"></a>派生唯一设备密钥

In this section, you create two unique device keys. 一个密钥将用于模拟的烤箱设备。 另一个密钥将用于模拟的热泵设备。

To generate the device key, you use the **Primary Key** you noted earlier to compute the [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) of the device registration ID for each device and convert the result into Base64 format. 有关使用注册组创建派生设备密钥的详细信息，请参阅[对称密钥证明](concepts-symmetric-key-attestation.md)的组注册部分。

对于本文中的示例，使用以下两个设备注册 ID 并计算这两个设备的设备密钥。 这两个注册 ID 都具有有效的后缀，以与自定义分配策略的示例代码结合使用：

* breakroom499-contoso-tstrsd-007
* mainbuilding167-contoso-hpsd-088

### <a name="linux-workstations"></a>Linux 工作站

If you're using a Linux workstation, you can use openssl to generate your derived device keys as shown in the following example.

1. 将“键”值替换为前面记录的“主键”。

    ```bash
    KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

    REG_ID1=breakroom499-contoso-tstrsd-007
    REG_ID2=mainbuilding167-contoso-hpsd-088

    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
    devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

    echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
    ```

    ```bash
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

### <a name="windows-based-workstations"></a>基于 Windows 的工作站

If you're using a Windows-based workstation, you can use PowerShell to generate your derived device key as shown in the following example.

1. 将“键”值替换为前面记录的“主键”。

    ```powershell
    $KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

    $REG_ID1='breakroom499-contoso-tstrsd-007'
    $REG_ID2='mainbuilding167-contoso-hpsd-088'

    $hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
    $hmacsha256.key = [Convert]::FromBase64String($key)
    $sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
    $sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
    $derivedkey1 = [Convert]::ToBase64String($sig1)
    $derivedkey2 = [Convert]::ToBase64String($sig2)

    echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
    ```

    ```powershell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```

模拟设备将使用含有每个注册 ID 的派生的设备密钥，以执行对称密钥证明。

## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>准备 Azure IoT C SDK 开发环境

In this section, you prepare the development environment used to build the [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). SDK 包含模拟设备的示例代码。 该模拟设备将尝试在设备启动顺序期间进行预配。

本部分面向基于 Windows 的工作站。 对于 Linux 示例，请参阅[如何进行多租户预配](how-to-provision-multitenant.md)中的 VM 的设置。

1. 下载 [CMake 生成系统](https://cmake.org/download/)。

    在进行 `CMake` 安装之前，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负载）。 必备组件到位并验证下载内容后，安装 CMake 生成系统。

2. 打开命令提示符或 Git Bash shell。 执行以下命令来克隆 Azure IoT C SDK GitHub 存储库：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    应该预料到此操作需要几分钟才能完成。

3. 在 git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. 运行以下命令，生成特定于你的开发客户端平台的 SDK 版本。 将在 `cmake` 目录中生成模拟设备的 Visual Studio 解决方案。 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    If `cmake` doesn't find your C++ compiler, you might get build errors while running the command. If that happens, try running the command in the [Visual Studio command prompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    生成成功后，最后的几个输出行如下所示：

    ```cmd/sh
    $ cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="simulate-the-devices"></a>模拟设备

In this section, you update a provisioning sample named **prov\_dev\_client\_sample** located in the Azure IoT C SDK you set up previously.

此示例代码模拟将预配请求发送到你的设备预配服务实例的设备启动序列。 启动序列将导致烤箱设备被识别，且使用自定义分配策略将其配置到 IoT 中心。

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡，记下“ID 范围”的值。

    ![从门户边栏选项卡中提取设备预配服务终结点信息](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在 Visual Studio 中，打开较早前通过运行 CMake 生成的 azure_iot_sdks.sln 解决方案文件。 解决方案文件应位于以下位置：

    ```
    azure-iot-sdk-c\cmake\azure_iot_sdks.sln
    ```

3. 在 Visual Studio 的“解决方案资源管理器”窗口中，导航到 **Provision\_Samples** 文件夹。 展开名为 **prov\_dev\_client\_sample** 的示例项目。 展开“源文件”，打开 **prov\_dev\_client\_sample.c**。

4. 找到 `id_scope` 常量，将值替换为前面复制的“ID 范围”值。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. 在同一文件中找到 `main()` 函数的定义。 确保 `hsm_type` 变量设置为 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`，如下所示：

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

6. 右键单击“prov\_dev\_client\_sample”项目，然后选择“设为启动项目”。

### <a name="simulate-the-contoso-toaster-device"></a>模拟 Contoso 烤箱设备

1. 若要模拟烤箱设备，请在 **prov\_dev\_client\_sample.c** 中找到已注释掉的对 `prov_dev_set_symmetric_key_info()` 的调用。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Uncomment the function call and replace the placeholder values (including the angle brackets) with the toaster registration ID and derived device key you generated previously. 下面显示的密钥值 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** 仅作为示例提供。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    保存文件。

2. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。 In the prompt to rebuild the project, select **Yes**, to rebuild the project before running.

    The following output is an example of the simulated toaster device successfully booting up and connecting to the provisioning service instance to be assigned to the toasters IoT hub by the custom allocation policy:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>模拟 Contoso 热泵设备

1. 若要模拟热泵设备，请使用热泵注册 ID 和之前生成的派生设备密钥更新 **prov\_dev\_client\_sample.c** 中的 `prov_dev_set_symmetric_key_info()` 调用。 下面显示的密钥值 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** 也仅作为示例提供。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    保存文件。

2. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。 In the prompt to rebuild the project, select **Yes** to rebuild the project before running.

    The following output is an example of the simulated heat pump device successfully booting up and connecting to the provisioning service instance to be assigned to the Contoso heat pumps IoT hub by the custom allocation policy:

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```

## <a name="troubleshooting-custom-allocation-policies"></a>自定义分配策略疑难解答

The following table shows expected scenarios and the results error codes you might receive. 使用此表来帮助你解决使用 Azure 函数时自定义分配策略失败的问题。

| 场景 | 预配服务的注册结果 | 预配 SDK 结果 |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook 返回 200 OK，其中“iotHubHostName”被设置为有效的 IoT 中心主机名 | 结果状态：已分配  | SDK 返回 PROV_DEVICE_RESULT_OK 和中心信息 |
| Webhook 返回 200 OK，其中在响应中显示“iotHubHostName”，但被设置为空字符串或 null | 结果状态：失败<br><br> 错误代码：CustomAllocationIotHubNotSpecified (400208) | SDK 返回 PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook 返回 401 未授权 | 结果状态：失败<br><br>错误代码：CustomAllocationUnauthorizedAccess (400209) | SDK 返回 PROV_DEVICE_RESULT_UNAUTHORIZED |
| 创建了个人注册，以禁用设备 | 结果状态：已禁用 | SDK 返回 PROV_DEVICE_RESULT_DISABLED |
| Webhook 返回错误代码 > = 429 | DPS 业务流程将多次重试。 重试策略当前：<br><br>&nbsp;&nbsp;- 重试计数：10<br>&nbsp;&nbsp;- 初始时间间隔：1 秒<br>&nbsp;&nbsp;- 增量：9 秒 | SDK 将忽略错误并在指定时间提交另一个获取状态消息 |
| Webhook 返回任何其他状态代码 | 结果状态：失败<br><br>错误代码：CustomAllocationFailed (400207) | SDK 返回 PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>清理资源

If you plan to continue working with the resources created in this article, you can leave them. If you don't plan to continue using the resources, use the following steps to delete all of the resources created in this article to avoid unnecessary charges.

此处的步骤假定你按照名为 contoso-us-resource-group 的同一资源组的指示创建了本文中的所有资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不要意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要按名称删除资源组：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。

2. 在“按名称筛选...”文本框中，键入包含资源的资源组名称“contoso-us-resource-group”。 

3. 在结果列表中的资源组右侧，选择“...”，然后选择“删除资源组”。

4. You'll be asked to confirm the deletion of the resource group. 再次键入资源组的名称进行确认，然后选择“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

* To learn more Reprovisioning, see [IoT Hub Device reprovisioning concepts](concepts-device-reprovision.md) 
* To learn more Deprovisioning, see [How to deprovision devices that were previously autoprovisioned](how-to-unprovision-devices.md) 
