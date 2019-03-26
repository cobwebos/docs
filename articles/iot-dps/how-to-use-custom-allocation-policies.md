---
title: 如何使用 Azure IoT 中心设备预配服务中的自定义分配策略 | Microsoft Docs
description: 如何使用 Azure IoT 中心设备预配服务中的自定义分配策略
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: b9c9973314ed0ec64504cd35daddf9a3ad945f99
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436976"
---
# <a name="how-to-use-custom-allocation-policies"></a>如何使用自定义分配策略


自定义分配策略让你能够对设备分配到 IoT 中心的方式进行更多地控制。 它是通过使用 [Azure 函数](../azure-functions/functions-overview.md)中的自定义代码将设备分配到 IoT 中心来实现的。 设备预配服务将调用 Azure 函数代码，提供有关设备和注册的所有相关信息。 将执行函数代码并返回用于预配设备的 IoT 中心信息。

当设备预配服务提供的策略不能满足你的方案的要求时，通过使用自定义分配策略，可以定义你自己的分配策略。

例如，你可能想要检查设备在预配过程中所使用的证书，并根据证书属性将该设备分配到 IoT 中心。 你可能在数据库中存储了你的设备的信息，并需要查询数据库以确定应将该设备分配到哪个 IoT 中心。


本文演示使用 C# 编写的 Azure 函数的自定义分配策略。 创建了两个新的 IoT 中心，分别表示 Contoso 烤箱分区和 Contoso 热泵分区。 请求预配的设备必须具有含以下后缀之一的注册 ID 才能被接受进行预配：

- **-contoso-tstrsd-007**：Contoso 烤箱分区
- **-contoso-hpsd-088**：Contoso 热泵分区

将基于注册 ID 上这些所需的后缀之一对设备进行预配。 将使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中包含的预配示例对这些设备进行模拟。 

你将在本文中执行以下步骤：

* 使用 Azure CLI 创建两个 Contoso 分区 IoT 中心（Contoso 烤箱分区和 Contoso 热泵分区）
* 使用 Azure 函数为自定义分配策略创建新的组注册
* 为两个设备模拟创建设备密钥。
* 为 Azure IoT C SDK 设置开发环境
* 模拟设备，以确保其根据自定义分配策略的示例代码进行预配。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必备组件

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)快速入门。
* 已启用[“使用 C++ 的桌面开发”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷的 Visual Studio 2015 或 [Visual Studio 2017](https://www.visualstudio.com/vs/)。
* 已安装最新版本的 [Git](https://git-scm.com/download/)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-two-divisional-iot-hubs"></a>创建两个分区 IoT 中心

在本部分，将使用 Azure Cloud Shell 创建表示 Contoso 烤箱分区和 Contoso 热泵分区的两个新的 IoT 中心。

1. 在 Azure Cloud Shell 中，使用 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

    以下示例在“eastus”区域创建名为“contoso-us-resource-group”的资源组。 建议对本文中创建的所有资源使用该组。 此方法使你能够在完成后更为轻松地进行清理。

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. 在 Azure Cloud Shell 中，使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令创建 Contoso 烤箱分区 IoT 中心。 IoT 中心将被添加到 contoso-us-resource-group。

    以下示例在“eastus”位置创建名为“contoso-toasters-hub-1098”的 IoT 中心。 必须使用你自己的唯一中心名称。 在中心名称中的 1098 位置构成你自己的后缀。 自定义分配策略的示例代码要求使用中心名称中的 `-toasters-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-toasters-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    此命令可能需要花费几分钟时间完成。

3. 在 Azure Cloud Shell 中，使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令创建 Contoso 热泵分区 IoT 中心。 此 IoT 中心也将被添加到 contoso-us-resource-group。

    以下示例在“eastus”位置创建名为“contoso-heatpumps-hub-1098”的 IoT 中心。 必须使用你自己的唯一中心名称。 在中心名称中的 1098 位置构成你自己的后缀。 自定义分配策略的示例代码要求使用中心名称中的 `-heatpumps-`。

    ```azurecli-interactive 
    az iot hub create --name contoso-heatpumps-hub-1098 --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    此命令可能同样需要花费几分钟时间完成。




## <a name="create-the-enrollment"></a>创建注册

在本部分中，将创建一个使用自定义分配策略的新注册组。 为简单起见，本文将在注册中使用[对称密钥证明](concepts-symmetric-key-attestation.md)。 对于更安全的解决方案，请考虑使用具有信任链的 [X.509 证书证明](concepts-security.md#x509-certificates)。

1. 登录到 [Azure 门户](https://portal.azure.com)，并打开你的设备预配服务实例。

2. 选择“管理注册”选项卡，然后单击页面顶部的“添加注册组”按钮。 

3. 在“添加注册组”中输入以下信息，然后单击“保存”按钮。

    **组名称**：输入 **contoso-custom-allocated-devices**。

    **证明类型**：选择“对称密钥”。

    **自动生成密钥**：此复选框应已处于选中状态。

    **选择要如何将设备分配到中心**：选择“自定义(使用 Azure Function)”。

    ![为对称密钥证明添加自定义分配注册组](./media/how-to-use-custom-allocation-policies/create-custom-allocation-enrollment.png)


4. 在“添加注册组”上，单击“链接新的 IoT 中心”以链接这两个新的分区 IoT 中心。 

    必须为这两个分区 IoT 中心执行此步骤。

    **订阅**：如果你有多个订阅，请选择创建分区 IoT 中心的订阅。

    **IoT 中心**：选择你创建的分区中心之一。

    **访问策略**：选择“iothubowner”。

    ![使用预配服务链接分区 IoT 中心](./media/how-to-use-custom-allocation-policies/link-divisional-hubs.png)


5. 在“添加注册组”上，一旦链接这两个分区 IoT 中心后，必须将其选择为注册组的 IoT 中心组，如下所示：

    ![为注册创建分区中心组](./media/how-to-use-custom-allocation-policies/enrollment-divisional-hub-group.png)


6. 在“添加注册组”上，向下滚动到“选择 Azure 函数”部分，然后单击“创建新的函数应用”。

7. 在打开的“函数应用”创建页上，为新函数输入以下设置，然后单击“创建”。

    **应用名称**：输入唯一函数应用名称。 contoso-function-app-1098 作为示例显示。

    **资源组**：选择“使用现有”和“contoso-us-resource-group”以将本文中创建的所有资源保留在一起。

    **Application Insights**：对于此练习，可以关闭该功能。

    ![创建函数应用](./media/how-to-use-custom-allocation-policies/function-app-create.png)


8. 回到你的“添加注册组”页，确保新函数应用处于选中状态。 可能需要重新选择订阅，以刷新函数应用列表。

    选中新函数应用后，单击“创建新函数”。

    ![创建函数应用](./media/how-to-use-custom-allocation-policies/click-create-new-function.png)

    将会打开新函数应用。

9. 在函数应用上，单击以创建新函数

    ![创建函数应用](./media/how-to-use-custom-allocation-policies/new-function.png)

    对于新函数，使用默认设置创建新的 Webhook + API（使用 CSharp 语言）。 单击“创建此函数”。

    这将创建一个名为 HttpTriggerCSharp1 的新 C# 函数。

10. 将新 C# 函数的代码替换为以下代码，然后单击“保存”：    

    ```C#
    #r "Newtonsoft.Json"
    using System.Net;
    using System.Text;
    using Newtonsoft.Json;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        // Just some diagnostic logging
        log.Info("C# HTTP trigger function processed a request.");
        log.Info("Request.Content:...");    
        log.Info(req.Content.ReadAsStringAsync().Result);

        // Get request body
        dynamic data = await req.Content.ReadAsAsync<object>();

        // Get registration ID of the device
        string regId = data?.deviceRuntimeContext?.registrationId;

        string message = "Uncaught error";
        bool fail = false;
        ResponseObj obj = new ResponseObj();

        if (regId == null)
        {
            message = "Registration ID not provided for the device.";
            log.Info("Registration ID : NULL");
            fail = true;
        }
        else
        {
            string[] hubs = data?.linkedHubs.ToObject<string[]>();

            // Must have hubs selected on the enrollment
            if (hubs == null)
            {
                message = "No hub group defined for the enrollment.";
                log.Info("linkedHubs : NULL");
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
                        log.Info(message);
                        fail = true;
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
                        log.Info(message);
                        fail = true;
                    }
                }
                // Unrecognized device.
                else
                {
                    fail = true;
                    message = "Unrecognized device registration.";
                    log.Info("Unknown device registration");
                }
            }
        }

        return (fail)
            ? req.CreateResponse(HttpStatusCode.BadRequest, message)
            : new HttpResponseMessage(HttpStatusCode.OK)
            {
                Content = new StringContent(JsonConvert.SerializeObject(obj, Formatting.Indented), Encoding.UTF8, "application/json")
            };
    }    

    public class DeviceTwinObj
    {
        public string deviceId {get; set;}
    }

    public class ResponseObj
    {
        public string iotHubHostName {get; set;}
        public string IoTHub {get; set;}
        public DeviceTwinObj initialTwin {get; set;}
        public string[] linkedHubs {get; set;}
        public string enrollment {get; set;}
    }
    ```


11. 返回到你的“添加注册组”页，并确保新函数处于选中状态。 可能需要重新选择函数应用，以刷新函数列表。

    选中新函数后，单击“保存”以保存注册组。

    ![最后，保存注册组](./media/how-to-use-custom-allocation-policies/save-enrollment.png)


12. 保存注册后，重新打开它，并记录“主键”。 必须先保存注册，才能生成密钥。 此密钥稍后将用于为模拟设备生成唯一设备密钥。


## <a name="derive-unique-device-keys"></a>派生唯一设备密钥

在本部分中，你将创建两个唯一的设备密钥。 一个密钥将用于模拟的烤箱设备。 另一个密钥将用于模拟的热泵设备。

为了生成设备密钥，将使用前面记录的“主密钥”来计算每个设备的设备注册 ID 的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)，并将结果转换为 Base64 格式。 有关使用注册组创建派生设备密钥的详细信息，请参阅[对称密钥证明](concepts-symmetric-key-attestation.md)的组注册部分。

对于本文中的示例，使用以下两个设备注册 ID 并计算这两个设备的设备密钥。 这两个注册 ID 都具有有效的后缀，以与自定义分配策略的示例代码结合使用：

- breakroom499-contoso-tstrsd-007
- mainbuilding167-contoso-hpsd-088

#### <a name="linux-workstations"></a>Linux 工作站

如果使用的是 Linux 工作站，可以使用 openssl 生成派生的设备密钥，如以下示例中所示。

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


#### <a name="windows-based-workstations"></a>基于 Windows 的工作站

如果使用的是基于 Windows 的工作站，可以使用 PowerShell 生成派生的设备密钥，如以下示例中所示。

1. 将“键”值替换为前面记录的“主键”。

    ```PowerShell
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

    ```PowerShell
    breakroom499-contoso-tstrsd-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
    mainbuilding167-contoso-hpsd-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
    ```


模拟设备将使用含有每个注册 ID 的派生的设备密钥，以执行对称密钥证明。




## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>准备 Azure IoT C SDK 开发环境

在本部分，你将准备一个用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 SDK 包含模拟设备的示例代码。 该模拟设备将尝试在设备启动顺序期间进行预配。

本部分面向基于 Windows 的工作站。 对于 Linux 示例，请参阅[如何进行多租户预配](how-to-provision-multitenant.md)中的 VM 的设置。

1. 下载[CMake 生成系统](https://cmake.org/download/)。 验证已下载的二进制文件使用你下载的版本相对应的加密哈希值。 加密哈希值位于，还从已提供的 CMake 下载链接。

    以下示例使用了 Windows PowerShell 来验证 x64 MSI 发行版本 3.13.4 的加密哈希：

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.13.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "64AC7DD5411B48C2717E15738B83EA0D4347CD51B940487DFF7F99A870656C09"
    True
    ```

    在撰写本文时，在 CMake 站点上列出了版本 3.13.4 的以下哈希值：

    ```
    563a39e0a7c7368f81bfa1c3aff8b590a0617cdfe51177ddc808f66cc0866c76  cmake-3.13.4-Linux-x86_64.tar.gz
    7c37235ece6ce85aab2ce169106e0e729504ad64707d56e4dbfc982cb4263847  cmake-3.13.4-win32-x86.msi
    64ac7dd5411b48c2717e15738b83ea0d4347cd51b940487dff7f99a870656c09  cmake-3.13.4-win64-x64.msi
    ```

    在进行 `CMake` 安装**之前**，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负荷）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。

2. 打开命令提示符或 Git Bash shell。 执行以下命令来克隆 Azure IoT C SDK GitHub 存储库：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    此存储库的大小目前大约为 220 MB。 应该预料到此操作需要几分钟才能完成。


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
    
    如果 `cmake` 找不到 C++ 编译器，则可能会在运行以上命令时出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。 

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

在本部分，你将更新你在较早前设置的位于 Azure IoT C SDK 中的名为 prov\_dev\_client\_sample 的预配示例。 

此示例代码模拟将预配请求发送到你的设备预配服务实例的设备启动序列。 启动序列将导致烤箱设备被识别，且使用自定义分配策略将其配置到 IoT 中心。

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡，记下“ID 范围”的值。

    ![从门户边栏选项卡中提取设备预配服务终结点信息](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在 Visual Studio 中，打开较早前通过运行 CMake 生成的 azure_iot_sdks.sln 解决方案文件。 解决方案文件应位于以下位置：

    ```
    \azure-iot-sdk-c\cmake\azure_iot_sdks.sln
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


#### <a name="simulate-the-contoso-toaster-device"></a>模拟 Contoso 烤箱设备

1. 若要模拟烤箱设备，请在 **prov\_dev\_client\_sample.c** 中找到已注释掉的对 `prov_dev_set_symmetric_key_info()` 的调用。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    取消注释该函数调用，并将占位符值（包括尖括号）替换为烤箱注册 ID 以及之前生成的派生设备密钥。 下面显示的密钥值 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** 仅作为示例提供。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("breakroom499-contoso-tstrsd-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```
   
    保存文件。

2. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。 在重新生成项目的提示中单击“是”，以便在运行项目之前重新生成项目。

    以下输出是模拟烤箱设备成功启动并连接到预配服务实例以通过自定义分配策略分配到烤箱 IoT 中心的一个示例：

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: breakroom499-contoso-tstrsd-007

    Press enter key to exit:
    ```


#### <a name="simulate-the-contoso-heat-pump-device"></a>模拟 Contoso 热泵设备

1. 若要模拟热泵设备，请使用热泵注册 ID 和之前生成的派生设备密钥更新 **prov\_dev\_client\_sample.c** 中的 `prov_dev_set_symmetric_key_info()` 调用。 下面显示的密钥值 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** 也仅作为示例提供。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("mainbuilding167-contoso-hpsd-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```
   
    保存文件。

2. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。 在重新生成项目的提示中单击“是”，以便在运行项目之前重新生成项目。

    以下输出是模拟热泵设备成功启动并连接到预配服务实例以通过自定义分配策略分配到 Contoso 热泵 IoT 中心的一个示例：

    ```cmd
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-heatpumps-hub-1098.azure-devices.net, deviceId: mainbuilding167-contoso-hpsd-088

    Press enter key to exit:
    ```


## <a name="troubleshooting-custom-allocation-policies"></a>自定义分配策略疑难解答

下表显示了预期方案和你可能遇到的结果错误代码。 使用此表来帮助你解决使用 Azure 函数时自定义分配策略失败的问题。


| 场景 | 预配服务的注册结果 | 预配 SDK 结果 |
| -------- | --------------------------------------------- | ------------------------ |
| Webhook 返回 200 OK，其中“iotHubHostName”被设置为有效的 IoT 中心主机名 | 结果状态：已分配  | SDK 返回 PROV_DEVICE_RESULT_OK 和中心信息 |
| Webhook 返回 200 OK，其中在响应中显示“iotHubHostName”，但被设置为空字符串或 null | 结果状态：已失败<br><br> 错误代码：CustomAllocationIotHubNotSpecified (400208) | SDK 返回 PROV_DEVICE_RESULT_HUB_NOT_SPECIFIED |
| Webhook 返回 401 未授权 | 结果状态：已失败<br><br>错误代码：CustomAllocationUnauthorizedAccess (400209) | SDK 返回 PROV_DEVICE_RESULT_UNAUTHORIZED |
| 创建了个人注册，以禁用设备 | 结果状态：已禁用 | SDK 返回 PROV_DEVICE_RESULT_DISABLED |
| Webhook 返回错误代码 > = 429 | DPS 业务流程将多次重试。 重试策略当前：<br><br>&nbsp;&nbsp;- 重试计数：10<br>&nbsp;&nbsp;- 初始时间间隔：1 秒<br>&nbsp;&nbsp;- 增量：9 秒 | SDK 将忽略错误并在指定时间提交另一个获取状态消息 |
| Webhook 返回任何其他状态代码 | 结果状态：已失败<br><br>错误代码：CustomAllocationFailed (400207) | SDK 返回 PROV_DEVICE_RESULT_DEV_AUTH_ERROR |


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用本文中创建的资源，则可以保留它们。 如果不打算继续使用这些资源，请使用以下步骤删除本文创建的所有资源，以避免不必要的费用。

此处的步骤假定你按照名为 contoso-us-resource-group 的同一资源组的指示创建了本文中的所有资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要按名称删除资源组：

1. 登录到 [Azure 门户](https://portal.azure.com)，并单击“资源组”。

2. 在“按名称筛选...”文本框中，键入包含资源的资源组名称“contoso-us-resource-group”。 

3. 在结果列表中的资源组右侧，单击“...”，然后单击“删除资源组”。

4. 系统会要求确认是否删除资源组。 再次键入资源组的名称进行确认，然后单击“删除”。 片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

- 若要了解更多 Reprovisioning，请参阅[IoT 中心设备重新预配概念](concepts-device-reprovision.md) 
- 若要了解有关取消设置的详细信息，请参阅[如何取消设置以前自动预配的设备](how-to-unprovision-devices.md) 











