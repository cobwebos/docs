---
title: Azure IoT 中心设备预配服务的自定义分配策略
description: 如何在 Azure IoT 中心设备预配服务（DPS）中使用自定义分配策略
author: wesmc7777
ms.author: wesmc
ms.date: 11/14/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: b6b7d4614d3c63fe93e213fb830b85d0b7f9c474
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974864"
---
# <a name="how-to-use-custom-allocation-policies"></a>如何使用自定义分配策略

自定义分配策略让你能够对设备分配到 IoT 中心的方式进行更多地控制。 它是通过使用 [Azure 函数](../azure-functions/functions-overview.md)中的自定义代码将设备分配到 IoT 中心来实现的。 设备预配服务将调用 Azure 函数代码，提供有关设备和注册的所有相关信息。 将执行函数代码并返回用于预配设备的 IoT 中心信息。

通过使用自定义分配策略，可以在设备预配服务提供的策略不符合方案要求时定义自己的分配策略。

例如，你可能想要在预配过程中检查设备使用的证书，并基于证书属性将设备分配到 IoT 中心。 或者，您的设备可能会在数据库中存储信息，并且需要查询数据库，以确定应将设备分配到的 IoT 中心。

本文演示使用 C# 编写的 Azure 函数的自定义分配策略。 创建了两个新的 IoT 中心，分别表示 Contoso 烤箱分区和 Contoso 热泵分区。 请求预配的设备必须具有含以下后缀之一的注册 ID 才能被接受进行预配：

* -contoso-tstrsd-007：Contoso 烤箱分区
* -contoso-hpsd-088：Contoso 热泵分区

将基于注册 ID 上这些所需的后缀之一对设备进行预配。 将使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中包含的预配示例对这些设备进行模拟。

在本文中执行以下步骤：

* 使用 Azure CLI 创建两个 Contoso 分区 IoT 中心（Contoso 烤箱分区和 Contoso 热泵分区）
* 使用 Azure 函数为自定义分配策略创建新的组注册
* 为两个设备模拟创建设备密钥。
* 为 Azure IoT C SDK 设置开发环境
* 模拟设备并验证是否已根据自定义分配策略中的示例代码进行了设置

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必备组件

* 启用了[“使用 C++ 的桌面开发”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷的 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 或更高版本。
* 已安装最新版本的 [Git](https://git-scm.com/download/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-provisioning-service-and-two-divisional-iot-hubs"></a>创建预配服务和两个部门 IoT 中心

在本部分中，将使用 Azure Cloud Shell 创建预配服务，并使用两个 IoT 中心来表示**Contoso 烤面包机分部**和**contoso 热度泵**。

> [!TIP]
> 本文中使用的命令在 "美国西部" 位置创建预配服务和其他资源。 建议你在最接近支持设备预配服务的区域中创建资源。 若要查看可用位置的列表，可以运行 `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` 命令，也可以转到[Azure 状态](https://azure.microsoft.com/status/)页，在其中搜索“设备预配服务”。 在命令中，可以通过一个词或多字格式指定位置;例如： westus、美国西部、美国西部等。此值不区分大小写。 如果使用多个单词的格式来指定位置，请将值置于引号中，例如 `-- location "West US"`。
>

1. 在 Azure Cloud Shell 中，使用 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

    以下示例在*westus*区域中创建名为 " *contoso-us-资源组*" 的资源组。 建议对本文中创建的所有资源使用该组。 完成后，这种方法可以更轻松地进行清理。

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location westus
    ```

2. 使用 " [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create) " 命令通过 Azure Cloud Shell 创建设备预配服务。 预配服务将添加到 " *contoso-资源组*"。

    以下示例在*westus*位置创建名为 " *contoso-预配-1098* " 的预配服务。 必须使用唯一的服务名称。 在服务名称中创建你自己的后缀，替代**1098**。

    ```azurecli-interactive 
    az iot dps create --name contoso-provisioning-service-1098 --resource-group contoso-us-resource-group --location westus
    ```

    此命令可能需要花费几分钟时间完成。

3. 在 Azure Cloud Shell 中，使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令创建 Contoso 烤箱分区 IoT 中心。 IoT 中心将被添加到 contoso-us-resource-group。

    以下示例在*westus*位置创建名为*1098 烤面包机*的 IoT 中心。 必须使用唯一的中心名称。 在中心名称中的 1098 位置构成你自己的后缀。 自定义分配策略的示例代码要求使用中心名称中的 `-toasters-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    此命令可能需要花费几分钟时间完成。

4. 在 Azure Cloud Shell 中，使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令创建 Contoso 热泵分区 IoT 中心。 此 IoT 中心也将被添加到 contoso-us-resource-group。

    以下示例在*westus*位置创建名为*1098 heatpumps*的 IoT 中心。 必须使用唯一的中心名称。 在中心名称中的 1098 位置构成你自己的后缀。 自定义分配策略的示例代码要求使用中心名称中的 `-heatpumps-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    此命令可能需要花费几分钟时间完成。

## <a name="create-the-custom-allocation-function"></a>创建自定义分配函数

在本部分中，将创建一个实现自定义分配策略的 Azure 函数。 此函数根据设备的注册 ID 是否包含 string **-tstrsd-007**或 **-hpsd-088**，决定应将设备注册到哪个部门 IoT 中心。 它还根据设备是 toaster 还是热泵来设置设备克隆的初始状态。

1. 登录到 [Azure 门户](https://portal.azure.com)。 从主页中，选择 " **+ 创建资源**"。

2. 在 "*搜索 Marketplace*搜索" 框中，键入 "Function App"。 从下拉列表中选择 " **Function App**"，然后选择 "**创建**"。

3. 在**Function App**创建 "页上的"**基本**信息 "选项卡下，为新的 Function App 输入以下设置，然后选择"**查看 + 创建**"：

    **资源组**：选择 " **contoso-资源组**"，使本文中创建的所有资源保持同一个。

    **Function App 名称**：输入唯一的函数应用名称。 此示例使用**contoso**--1098。

    **发布**：验证是否选择了**代码**。

    **运行时堆栈**：从下拉选择 " **.net Core** "。

    **区域**：选择与资源组相同的区域。 此示例使用**美国西部**。

    > [!NOTE]
    > 默认情况下，Application Insights 处于启用状态。 本文不需要 Application Insights，但它可能会帮助你了解和调查自定义分配所遇到的任何问题。 如果愿意，可以通过选择 "**监视**" 选项卡，然后选择 "**否**" 以**启用 Application Insights**来禁用 Application Insights。

    ![创建 Azure Function App 以托管自定义分配函数](./media/how-to-use-custom-allocation-policies/create-function-app.png)

4. 在 "**摘要**" 页上，选择 "**创建**" 以创建函数应用。 部署可能需要几分钟的时间。 完成后，选择 "**前往资源**"。

5. 在 "函数应用**概述**" 页的左窗格中，选择 "**函数**" 旁边的 **+** 添加新函数。

    ![将函数添加到 Function App](./media/how-to-use-custom-allocation-policies/create-function.png)

6. 在 "**适用于 .net 的 Azure Functions-入门**" 页上，针对 "**选择部署环境**" 步骤，选择 "**门户**" 磁贴，然后选择 "**继续**"。

    ![选择门户开发环境](./media/how-to-use-custom-allocation-policies/function-choose-environment.png)

7. 在下一页上，对于 "**创建函数**" 步骤，选择 " **Webhook + API** " 磁贴，然后选择 "**创建**"。 创建名为**HttpTrigger1**的函数，门户将显示**run.csx**代码文件的内容。

8. 引用所需的 Nuget 包。 若要创建初始设备克隆，自定义分配函数使用在必须加载到宿主环境中的两个 Nuget 包中定义的类。 使用 Azure Functions，将使用*函数主机*文件引用 Nuget 包。 在此步骤中，您将保存并上传*函数。主机*文件。

    1. 将以下行复制到您喜爱的编辑器中，并将该文件保存在计算机上作为*函数。*

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

    2. 在**HttpTrigger1**函数上，展开窗口右侧的 "**查看文件**" 选项卡。

        ![打开视图文件](./media/how-to-use-custom-allocation-policies/function-open-view-files.png)

    3. 选择 "**上传**"，浏览到**函数 proj**文件，然后选择 "**打开**" 上传文件。

        ![选择上传文件](./media/how-to-use-custom-allocation-policies/function-choose-upload-file.png)

9. 将**HttpTrigger1**函数的代码替换为以下代码，然后选择 "**保存**"：

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

在本部分中，你将创建一个使用自定义分配策略的新注册组。 为简单起见，本文将在注册中使用[对称密钥证明](concepts-symmetric-key-attestation.md)。 对于更安全的解决方案，请考虑使用具有信任链的 [X.509 证书证明](concepts-security.md#x509-certificates)。

1. 继续[Azure 门户](https://portal.azure.com)，请打开预配服务。

2. 在左侧窗格中选择 "**管理**注册"，然后选择页面顶部的 "**添加注册组**" 按钮。

3. 在 "**添加注册组**" 中输入以下信息，然后选择 "**保存**" 按钮。

    组名称：输入 contoso-custom-allocated-devices。

    证明类型：选择“对称密钥”。

    自动生成密钥：此复选框应已处于选中状态。

    选择要如何将设备分配到中心：选择“自定义(使用 Azure 函数)”。

    ![为对称密钥证明添加自定义分配注册组](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. 在 "**添加注册组**" 上，选择 "**链接新的 IoT 中心**" 链接这两个新的部门 iot 中心。

    为这两个部门的 IoT 中心执行此步骤。

    订阅：如果你有多个订阅，请选择创建分区 IoT 中心的订阅。

    IoT 中心：选择你创建的分区中心之一。

    访问策略：选择“iothubowner”。

    ![使用预配服务链接分区 IoT 中心](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)

5. 在“添加注册组”上，一旦链接这两个分区 IoT 中心后，必须将其选择为注册组的 IoT 中心组，如下所示：

    ![为注册创建分区中心组](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)

6. 在 "**添加注册组**" 下，向下滚动到 "**选择 Azure 函数**" 部分，选择在上一部分中创建的函数应用。 然后选择创建的函数，然后选择 "保存" 以保存注册组。

    ![选择函数并保存注册组](./media/how-to-use-custom-allocation-policies/save-enrollment.png)

7. 保存注册后，重新打开它，并记录“主键”。 必须先保存注册，才能生成密钥。 此密钥稍后将用于为模拟设备生成唯一设备密钥。

## <a name="derive-unique-device-keys"></a>派生唯一设备密钥

在本部分中，将创建两个唯一的设备密钥。 一个密钥将用于模拟的烤箱设备。 另一个密钥将用于模拟的热泵设备。

若要生成设备密钥，请使用前面记**下的主密钥**来计算每个设备的设备注册 ID 的[HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ，并将结果转换为 Base64 格式。 有关使用注册组创建派生设备密钥的详细信息，请参阅[对称密钥证明](concepts-symmetric-key-attestation.md)的组注册部分。

对于本文中的示例，使用以下两个设备注册 ID 并计算这两个设备的设备密钥。 这两个注册 ID 都具有有效的后缀，以与自定义分配策略的示例代码结合使用：

* breakroom499-contoso-tstrsd-007
* mainbuilding167-contoso-hpsd-088

### <a name="linux-workstations"></a>Linux 工作站

如果你使用的是 Linux 工作站，则可以使用 openssl 生成派生设备密钥，如以下示例中所示。

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

如果你使用的是基于 Windows 的工作站，则可以使用 PowerShell 生成派生设备密钥，如以下示例中所示。

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

在本部分中，将准备用于构建[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)的开发环境。 SDK 包含模拟设备的示例代码。 该模拟设备将尝试在设备启动顺序期间进行预配。

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

    如果 `cmake` 找不到C++编译器，则在运行命令时可能会出现生成错误。 如果发生这种情况，请尝试在[Visual Studio 命令提示符下](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)运行该命令。

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

在本部分中，将更新名为 "\_prov" 的预配示例 **\_客户端\_示例**，该示例位于你之前设置的 Azure IOT C SDK 中。

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

    取消对函数调用的注释，并将占位符值（包括尖括号）替换为之前生成的 toaster 注册 ID 和派生设备密钥。 下面显示的密钥值 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** 仅作为示例提供。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    保存文件。

2. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。 对于重新生成项目的提示，请选择“是”，以便在运行项目之前重新生成项目。

    以下输出为模拟 toaster 设备的一个示例，它成功启动并连接到预配服务实例，以便通过自定义分配策略分配给烤面包机 IoT 中心：

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

2. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。 在提示重新生成项目时，选择 **"是"** 以在运行前重新生成项目。

    下面的输出是一个模拟的热交换设备的示例，该设备已成功启动并连接到预配服务实例，可通过自定义分配策略分配给 Contoso 热度泵 IoT 中心：

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

下表显示了预期的方案以及可能会收到的结果错误代码。 使用此表来帮助你解决使用 Azure 函数时自定义分配策略失败的问题。

| 场景 | 预配服务的注册结果 | 预配 SDK 结果 |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook 返回 200 OK，其中“iotHubHostName”被设置为有效的 IoT 中心主机名 | 结果状态：已分配  | SDK 返回 PROV_DEVICE_RESULT_OK 和中心信息 |
| Webhook 返回 200 OK，其中在响应中显示“iotHubHostName”，但被设置为空字符串或 null | 结果状态：失败<br><br> 错误代码：CustomAllocationIotHubNotSpecified (400208) | SDK 返回 PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook 返回 401 未授权 | 结果状态：失败<br><br>错误代码：CustomAllocationUnauthorizedAccess (400209) | SDK 返回 PROV_DEVICE_RESULT_UNAUTHORIZED |
| 创建了个人注册，以禁用设备 | 结果状态：已禁用 | SDK 返回 PROV_DEVICE_RESULT_DISABLED |
| Webhook 返回错误代码 > = 429 | DPS 业务流程将多次重试。 重试策略当前：<br><br>&nbsp;&nbsp;- 重试计数：10<br>&nbsp;&nbsp;- 初始时间间隔：1 秒<br>&nbsp;&nbsp;- 增量：9 秒 | SDK 将忽略错误并在指定时间提交另一个获取状态消息 |
| Webhook 返回任何其他状态代码 | 结果状态：失败<br><br>错误代码：CustomAllocationFailed (400207) | SDK 返回 PROV_DEVICE_RESULT_DEV_AUTH_ERROR |

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用本文中创建的资源，可以保留这些资源。 如果不打算继续使用这些资源，请使用以下步骤删除本文中创建的所有资源，以避免产生不必要的费用。

此处的步骤假定你按照名为 contoso-us-resource-group 的同一资源组的指示创建了本文中的所有资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不要意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要按名称删除资源组：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。

2. 在“按名称筛选...”文本框中，键入包含资源的资源组名称“contoso-us-resource-group”。 

3. 在结果列表中的资源组右侧，选择“...”，然后选择“删除资源组”。

4. 系统会要求你确认是否要删除该资源组。 再次键入资源组的名称进行确认，然后选择“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

* 若要了解更多重新设置，请参阅[IoT 中心设备重新设置概念](concepts-device-reprovision.md) 
* 若要了解更多取消设置，请参阅如何取消预[配以前 autoprovisioned 的设备](how-to-unprovision-devices.md) 
