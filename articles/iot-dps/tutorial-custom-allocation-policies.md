---
title: 有关通过 Azure IoT 中心设备预配服务 (DPS) 使用自定义分配策略的教程
description: 有关通过 Azure IoT 中心设备预配服务 (DPS) 使用自定义分配策略的教程
author: wesmc7777
ms.author: wesmc
ms.date: 09/23/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: ae43e0ed1bf3f64ce851e9ae779d54b82269a7be
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91405559"
---
# <a name="tutorial-use-custom-allocation-policies-with-device-provisioning-service-dps"></a>教程：通过设备预配服务 (DPS) 使用自定义分配策略

自定义分配策略让你能够对设备分配到 IoT 中心的方式进行更多地控制。 这是通过使用 [Azure 函数](../azure-functions/functions-overview.md)中的自定义代码实现的，该代码在预配期间运行后会将设备分配到 IoT 中心。 设备预配服务将调用 Azure 函数代码，提供有关设备和注册的所有相关信息。 将执行函数代码并返回用于预配设备的 IoT 中心信息。

当设备预配服务提供的策略不能满足你的方案的要求时，可通过使用自定义分配策略定义你自己的分配策略。

例如，你可能想要检查设备在预配过程中所使用的证书，并根据证书属性将该设备分配到 IoT 中心。 或者，你可能在数据库中存储了设备的信息，并需要查询数据库以确定应将该设备分配到哪个 IoT 中心。

本文展示了一个具有自定义分配策略的注册组，该策略使用以 C# 编写的 Azure 函数通过对称密钥预配烤箱设备。 不会将任何未被识别为烤箱设备的设备预配到 IoT 中心。

设备会请求使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中包含的预配示例代码进行预配。


在本教程中，你将执行以下操作：

> [!div class="checklist"]
> * 创建新的 Azure 函数应用来支持自定义分配函数
> * 使用 Azure 函数为自定义分配策略创建新的组注册
> * 为两个设备创建设备密钥
> * 为 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的示例设备代码设置开发环境
> * 运行设备，并验证是否已根据自定义分配策略预配它们


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 本文假设你已完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤来创建 IoT 中心和 DPS 实例。

* 已安装最新版本的 [Git](https://git-scm.com/download/)。

* 对于 Windows 开发环境，需要使用启用了[“采用 C++ 的桌面开发”](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作负荷的 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019。 Visual Studio 2015 和 Visual Studio 2017 也受支持。

* 对于 Linux 或 macOS，请参阅 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 文档的[准备开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的相应部分。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-custom-allocation-function"></a>创建自定义分配函数

在本部分，你将创建一个实现自定义分配策略的 Azure 函数。 此函数决定了是否应根据设备的注册 ID 是否包含字符串前缀 **contoso-toaster** 将设备注册到 IoT 中心。

1. 登录 [Azure 门户](https://portal.azure.com)。 在主页中选择“+ 创建资源”。****

2. 在“搜索市场”搜索框中键入“函数应用”。** 从下拉列表中选择“函数应用”，然后选择“创建”。********

3. 在“函数应用”创建页上的“基本信息”选项卡下，输入新函数应用的以下设置，然后选择“查看 + 创建”：************

    订阅：如果你有多个订阅，但未选择所需的订阅，请选择要使用的订阅。

    **资源组**：此字段允许你创建新的资源组或选择现有资源组来包含函数应用。 选择你之前创建的用于测试的 IoT 中心所在的资源组，例如 **TestResources**。 通过将所有相关资源都放在一个组中，可以一起管理它们。

    **函数应用名称**：输入唯一函数应用名称。 本示例使用 **contoso-function-app**。

    **发布**：验证是否选择了“代码”。

    **运行时堆栈**：从下拉列表中选择“.NET Core”。

    **区域**：选择你的资源组所在的区域。 此示例使用“美国西部”。

    > [!NOTE]
    > 默认已启用 Application Insights。 本文不需要 Application Insights，但它可以帮助你了解和调查处理自定义分配时遇到的任何问题。 如果需要，可以禁用 Application Insights，方法是选择“监视”选项卡，然后对“启用 Application Insights”选择“否”。************

    ![创建用于托管自定义分配函数的 Azure 函数应用](./media/tutorial-custom-allocation-policies/create-function-app.png)

4. 在“摘要”页上，选择“创建”以创建函数应用。******** 部署可能需要几分钟的时间。 完成后，选择“转到资源”。****

5. 在左侧窗格的“函数”下单击“函数”，然后单击“+ 添加”以添加新函数。

6. 在模板页上，选择“HTTP 触发器”磁贴，然后选择“创建函数”。 将创建一个名为 **HttpTrigger1** 的函数，而门户则会显示该函数的概览页。

7. 针对你的新函数单击“代码 + 测试”。 门户会显示 **run.csx** 代码文件的内容。 

8. 将 **HttpTrigger1** 函数的代码替换为以下代码，然后选择“保存”。 你的自定义分配代码已准备就绪，可供使用。

    ```csharp
    #r "Newtonsoft.Json"

    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

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
                // This is a Contoso Toaster 
                if (regId.Contains("contoso-toaster"))
                {
                    //Log IoT hubs configured for the enrollment
                    foreach(string hubString in hubs)
                    {
                        log.LogInformation("linkedHub : " + hubString);
                    }

                    obj.iotHubHostName = hubs[0];
                    log.LogInformation("Selected hub : " + obj.iotHubHostName);
                }
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
    }
    ```

9. 在 **run.csx** 代码文件底部的下方，单击“日志”以监视来自自定义分配函数的日志记录。 


## <a name="create-the-enrollment"></a>创建注册

在本部分，你将创建一个使用自定义分配策略的新注册组。 为简单起见，本文将在注册中使用[对称密钥证明](concepts-symmetric-key-attestation.md)。 对于更安全的解决方案，请考虑使用具有信任链的 [X.509 证书证明](concepts-x509-attestation.md)。

1. 仍在 [Azure 门户](https://portal.azure.com)中操作，打开预配服务。

2. 在左窗格中选择“管理注册”，然后在页面顶部选择“添加注册组”按钮。********

3. 在“添加注册组”中输入下表中的信息，然后单击“保存”按钮。

    | 字段 | 说明和/或建议的值 |
    | :---- | :----------------------------- |
    | **组名** | 输入 **contoso-custom-allocated-devices** |
    | **证明类型** | 选择“对称密钥” |
    | **自动生成密钥** | 此复选框应已处于选中状态。 |
    | **选择要如何将设备分配到中心** | 选择“自定义(使用 Azure 函数)” |
    | **选择此组可分配到的 IoT 中心** | 选择你之前在完成快速入门时创建的 IoT 中心。 |
    | **选择 Azure 函数** | 选择包含你创建的函数应用的订阅。 然后，为该函数选择 **contoso-function-app** 和 **HttpTrigger1**。 |

    ![为对称密钥证明添加自定义分配注册组](./media/tutorial-custom-allocation-policies/create-custom-allocation-enrollment.png)

4. 保存注册后，重新打开它，并记录“主键”****。 必须先保存注册，才能生成密钥。 此主要对称密钥将用于为稍后会尝试进行预配的设备生成唯一的设备密钥。 

## <a name="derive-unique-device-keys"></a>派生唯一设备密钥

设备不直接使用主对称密钥。 你可以改为使用主密钥来派生每个设备的设备密钥。 在本部分，你将创建两个唯一的设备密钥。 一个密钥将用于模拟的烤箱设备。 另一个密钥将用于模拟的热泵设备。 生成的密钥会允许这两个设备尝试注册。 只有一个设备注册 ID 将具有自定义分配策略示例代码可接受的有效后缀。 因此，一个会被接受，另一个会被拒绝

若要派生设备密钥，请使用前面记下的对称密钥来计算每个设备的设备注册 ID 的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)，并将结果转换为 Base64 格式。 有关使用注册组创建派生设备密钥的详细信息，请参阅[对称密钥证明](concepts-symmetric-key-attestation.md)的组注册部分。

对于本文中的示例，请使用以下两个设备注册 ID 和下面的代码来计算这两个设备的设备密钥：

* **contoso-toaster-007**
* **contoso-heatpump-088**

将 **KEY** 变量的值替换为你之前在创建注册组后记下的**主密钥**。 下面的代码显示的密钥值和输出只是一个示例。

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

如果使用的是基于 Windows 的工作站，可以使用 PowerShell 生成派生的设备密钥，如以下示例中所示。

```powershell
$KEY='oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA=='

$REG_ID1='contoso-toaster-007'
$REG_ID2='contoso-heatpump-088'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($key)
$sig1 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID1))
$sig2 = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID2))
$derivedkey1 = [Convert]::ToBase64String($sig1)
$derivedkey2 = [Convert]::ToBase64String($sig2)

echo "`n`n$REG_ID1 : $derivedkey1`n$REG_ID2 : $derivedkey2`n`n"
```

```powershell
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

#### <a name="bash"></a>[Bash](#tab/bash)

如果你使用的是 Linux 工作站，可以使用 openssl 生成派生的设备密钥，如以下 Bash 示例所示。

```bash
KEY=oiK77Oy7rBw8YB6IS6ukRChAw+Yq6GC61RMrPLSTiOOtdI+XDu0LmLuNm11p+qv2I+adqGUdZHm46zXAQdZoOA==

REG_ID1=contoso-toaster-007
REG_ID2=contoso-heatpump-088

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
devkey1=$(echo -n $REG_ID1 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)
devkey2=$(echo -n $REG_ID2 | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64)

echo -e $"\n\n$REG_ID1 : $devkey1\n$REG_ID2 : $devkey2\n\n"
```

```bash
contoso-toaster-007 : JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=
contoso-heatpump-088 : 6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=
```

---


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>准备 Azure IoT C SDK 开发环境

设备会请求使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中包含的预配示例代码进行预配。

在本部分，你将准备一个用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 SDK 包含模拟设备的示例代码。 该模拟设备将尝试在设备启动顺序期间进行预配。

本部分面向基于 Windows 的工作站。 对于 Linux 示例，请参阅[如何进行多租户预配](how-to-provision-multitenant.md)中的 VM 的设置。

1. 下载 [CMake 生成系统](https://cmake.org/download/)。

    在进行 `CMake` 安装**之前**，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负荷）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。

2. 找到[最新版](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK 的标记名称。

3. 打开命令提示符或 Git Bash shell。 运行以下命令，克隆最新版 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 使用在上一步找到的标记作为 `-b` 参数的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    应该预料到此操作需要几分钟才能完成。

4. 在 git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。 从 `azure-iot-sdk-c` 目录运行以下命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 运行以下命令，生成特定于你的开发客户端平台的 SDK 版本。 将在 `cmake` 目录中生成模拟设备的 Visual Studio 解决方案。 

    ```cmd
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    如果 `cmake` 找不到 C++ 编译器，则在运行该命令时可能会出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。

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

在本部分，你将更新前面设置的、位于 Azure IoT C SDK 中的名为 **prov\_dev\_client\_sample** 的预配示例。

此示例代码模拟将预配请求发送到你的设备预配服务实例的设备启动序列。 启动序列将导致烤箱设备被识别，且使用自定义分配策略将其配置到 IoT 中心。

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡，记下“ID 范围”的值。******__**

    ![从门户边栏选项卡中提取设备预配服务终结点信息](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在 Visual Studio 中，打开较早前通过运行 CMake 生成的 azure_iot_sdks.sln**** 解决方案文件。 解决方案文件应位于以下位置：

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

    取消注释该函数调用，并将占位符值（包括尖括号）替换为烤箱注册 ID 以及前面生成的派生设备密钥。 下面显示的密钥值 **JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=** 仅作为示例提供。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-toaster-007", "JC8F96eayuQwwz+PkE7IzjH2lIAjCUnAa61tDigBnSs=");
    ```

    保存文件。

2. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。  出现重新生成项目的提示时，请选择“是”，以便在运行项目之前重新生成项目。

    下面是示例日志记录输出，它来自针对烤箱设备运行的自定义分配函数代码。 请注意，已成功为烤箱设备选择了一个中心。 在门户中的函数代码下单击“日志”可以获得此日志记录：

    ```cmd
    2020-09-23T11:44:37.505 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=4596d45e-086f-4e86-929b-4a02814eee40)
    2020-09-23T11:44:41.380 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:44:41.381 [Information] Request.Body:...
    2020-09-23T11:44:41.381 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-toaster-007","symmetricKey":{}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:44:41.687 [Information] linkedHub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Selected hub : contoso-toasters-hub-1098.azure-devices.net
    2020-09-23T11:44:41.688 [Information] Response
    2020-09-23T11:44:41.688 [Information] {"iotHubHostName":"contoso-toasters-hub-1098.azure-devices.net"}
    2020-09-23T11:44:41.689 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=4596d45e-086f-4e86-929b-4a02814eee40, Duration=4347ms)    
    ```

    以下示例设备输出显示模拟的烤箱设备成功启动并连接到预配服务实例，因此可以通过自定义分配策略分配到烤箱 IoT 中心：

    ```cmd
    Provisioning API Version: 1.3.6

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-toasters-hub-1098.azure-devices.net, deviceId: contoso-toaster-007

    Press enter key to exit:
    ```

### <a name="simulate-the-contoso-heat-pump-device"></a>模拟 Contoso 热泵设备

1. 若要模拟热泵设备，请使用热泵注册 ID 和之前生成的派生设备密钥更新 **prov\_dev\_client\_sample.c** 中的 `prov_dev_set_symmetric_key_info()` 调用。 下面显示的密钥值 **6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=** 也仅作为示例提供。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-heatpump-088", "6uejA9PfkQgmYylj8Zerp3kcbeVrGZ172YLa7VSnJzg=");
    ```

    保存文件。

2. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。  对于重新生成项目的提示，请选择“是”，以便在运行项目之前重新生成项目。

    下面是示例日志记录输出，它来自针对热泵设备运行的自定义分配函数代码。 自定义分配策略拒绝此注册并显示 HTTP 错误“400 错误的请求”。 在门户中的函数代码下单击“日志”可以获得此日志记录：

    ```cmd
    2020-09-23T11:50:23.652 [Information] Executing 'Functions.HttpTrigger1' (Reason='This function was programmatically called via the host APIs.', Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68)
    2020-09-23T11:50:23.653 [Information] C# HTTP trigger function processed a request.
    2020-09-23T11:50:23.654 [Information] Request.Body:...
    2020-09-23T11:50:23.654 [Information] {"enrollmentGroup":{"enrollmentGroupId":"contoso-custom-allocated-devices","attestation":{"type":"symmetricKey"},"capabilities":{"iotEdge":false},"etag":"\"e8002126-0000-0100-0000-5f6b2a570000\"","provisioningStatus":"enabled","reprovisionPolicy":{"updateHubAssignment":true,"migrateDeviceData":true},"createdDateTimeUtc":"2020-09-23T10:58:31.62286Z","lastUpdatedDateTimeUtc":"2020-09-23T10:58:31.62286Z","allocationPolicy":"custom","iotHubs":["contoso-toasters-hub-1098.azure-devices.net"],"customAllocationDefinition":{"webhookUrl":"https://contoso-function-app.azurewebsites.net/api/HttpTrigger1?****","apiVersion":"2019-03-31"}},"deviceRuntimeContext":{"registrationId":"contoso-heatpump-088","symmetricKey":{}},"linkedHubs":["contoso-toasters-hub-1098.azure-devices.net"]}
    2020-09-23T11:50:23.654 [Information] Unknown device registration
    2020-09-23T11:50:23.654 [Information] Response
    2020-09-23T11:50:23.654 [Information] Unrecognized device registration.
    2020-09-23T11:50:23.655 [Information] Executed 'Functions.HttpTrigger1' (Succeeded, Id=2fa77f10-42f8-43fe-88d9-a8c01d4d3f68, Duration=11ms)
    ```

    以下示例设备输出显示模拟的热泵设备启动并连接到预配服务实例，因此可以尝试使用自定义分配策略注册到 IoT 中心。 此操作失败并出现错误“`Custom allocation failed with status code: 400`”，因为自定义分配策略设计为仅允许烤箱设备：


    ```cmd
    Provisioning API Version: 1.3.7
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_prov_transport_process_json_reply Line:658 Provisioning Failure: OperationId: 4.eb89f3e8407a3711.2525bd34-02e9-4e91-a9c0-4dbc4ad5de66 - Date: 2020-09-23T17:05:58.2363145Z - Msg: Custom allocation failed with status code: 400
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_transport_mqtt_common.c Func:_prov_transport_common_mqtt_dowork Line:1014 Unable to process registration reply.
    Error: Time:Wed Sep 23 13:06:01 2020 File:d:\testing\azure-iot-sdk-c\provisioning_client\src\prov_device_ll_client.c Func:_on_transport_registration_data Line:770 Failure retrieving data from the provisioning service
    
    Failure registering device: PROV_DEVICE_RESULT_DEV_AUTH_ERROR
    Press enter key to exit:    
    ```
    
## <a name="clean-up-resources"></a>清理资源

如果你打算继续使用本文中创建的资源，可以保留它们。 如果你不打算继续使用这些资源，请使用以下步骤删除本文创建的所有资源，以避免不必要的费用。

此处的步骤假定你按照名为 contoso-us-resource-group**** 的同一资源组的指示创建了本文中的所有资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不要意外删除错误的资源组或资源。 如果在现有的包含要保留资源的资源组中创建了 IoT 中心，则只删除 IoT 中心资源本身，而不要删除资源组。
>

若要按名称删除资源组：

1. 登录到 [Azure 门户](https://portal.azure.com)，然后选择“资源组”。 

2. 在“按名称筛选...”**** 文本框中，键入包含资源的资源组名称“contoso-us-resource-group”****。 

3. 在结果列表中的资源组右侧，选择“...”，然后选择“删除资源组”********。

4. 系统会要求确认是否删除该资源组。 再次键入资源组的名称进行确认，然后选择“删除”****。 片刻之后，将会删除该资源组及其包含的所有资源。

## <a name="next-steps"></a>后续步骤

* 如需更深入的自定义分配策略示例，请参阅[如何使用自定义分配策略](how-to-use-custom-allocation-policies.md)。
* 若要详细了解重新预配，请参阅 [IoT 中心设备重新预配概念](concepts-device-reprovision.md)。
* 若要详细了解解除预配，请参阅[如何解除预配以前自动预配的设备](how-to-unprovision-devices.md)。
