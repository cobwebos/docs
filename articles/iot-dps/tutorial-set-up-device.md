---
title: "针对 Azure IoT 中心设备预配服务设置设备 | Microsoft Docs"
description: "在设备制造过程中通过 IoT 中心设备预配服务设置设备以进行预配"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: c99279413b50e7bf1e6058a4151890e3a8f83892
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---

# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中心设备预配服务设置设备以进行预配

前面的教程介绍了设置 Azure IoT 中心设备预配服务以将设备自动预配到 IoT 中心的方法。 本教程将介绍如何在制造过程中设置设备，以便能基于设备的[硬件安全模块 (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security) 为设备配置设备预配服务，并使设备可在首次启动时连接到你的设备预配服务。 本教程讨论以下过程：

> [!div class="checklist"]
> * 选择硬件安全模块
> * 为所选 HSM 生成设备预配客户端 SDK
> * 提取安全项目
> * 在设备上设置设备预配服务配置

## <a name="prerequisites"></a>先决条件

继续之前，请按照[设置云以预配设备](./tutorial-set-up-cloud.md)教程中所述的说明，创建设备预配服务实例和 IoT 中心。


## <a name="select-a-hardware-security-module"></a>选择硬件安全模块

[设备预配服务客户端 SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/dps_client) 支持两种类型的硬件安全模块（或称为 HSM）： 

- [受信任的平台模块 (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module)。
    - TPM 是适用于大多数基于 Windows 的设备平台和几种基于 Linux/Ubuntu 的设备的标准模块。 对于设备制造商，如果其设备上运行以上任一种 OS，并且正在寻找标准 HSM，则可以选择此 HSM。 使用 TPM 芯片可以向设备预配服务单独注册每台设备。 出于开发目的，可以在 Windows 或 Linux 开发计算机上使用 TPM 模拟器。

- 基于 [X.509](https://cryptography.io/en/latest/x509/) 的硬件安全模块。 
    - 基于 X.509 的 HSM 是相对较新的芯片，目前在 Microsoft 中用于实现 X.509 证书的 RIoT 或 DICE 芯片上运行。 使用 X.509 芯片可以在门户中进行批量注册。 它还支持 Windows 以外的某些 OS，如 embedOS。 出于开发目的，设备预配服务客户端 SDK 支持 X.509 设备模拟器。 

设备制造商需要选择基于以上一种类型的硬件安全模块/芯片。 设备预配服务客户端 SDK 中当前不支持其他类型的 HSM。   


## <a name="build-device-provisioning-client-sdk-for-the-selected-hsm"></a>为所选 HSM 生成设备预配客户端 SDK

设备预配服务客户端 SDK 可帮助在软件中实现所选的安全机制。 以下步骤演示如何将 SDK 用于所选的 HSM 芯片：

1. 如果已执行了[创建模拟设备快速入门](./quick-create-simulated-device.md)中的步骤，则已具备了生成 SDK 的设置。 如果没有，请执行标题为[准备开发环境](./quick-create-simulated-device.md#setupdevbox)部分中的前 4 个步骤。 这些步骤将克隆设备预配服务客户端 SDK 的 GitHub 存储库并安装 `cmake` 生成工具。 

1. 要为设备生成适用于所选 HSM 类型的 SDK，请在命令提示符中使用以下一种命令：
    - 对于 TPM 设备：
        ```cmd/sh
        cmake -Ddps_auth_type=tpm ..
        ```

    - 对于 TPM 模拟器：
        ```cmd/sh
        cmake -Ddps_auth_type=tpm_simulator ..
        ```

    - 对于 X.509 设备和模拟器：
        ```cmd/sh
        cmake -Ddps_auth_type=x509 ..
        ```

1. 此 SDK 默认支持运行适用于 TPM 和 X.509 HSM 的 Windows 或 Ubuntu 实现的设备。 对于这些受支持的 HSM，请转到下文标题为[提取安全项目](#extractsecurity)的部分。 
 
## <a name="support-custom-tpm-and-x509-devices"></a>支持自定义 TPM 和 X.509 设备

对于不运行 Windows 或 Ubuntu 的任何 TPM 和 X.509 设备，设备预配系统客户端 SDK 不提供默认支持。 对于此类设备，需要为特定 HSM 芯片编写自定义代码，如以下步骤所示：

### <a name="develop-your-custom-repository"></a>开发自定义存储库

1. 开发 GitHub 存储库以访问 HSM。 此项目需要生成可供设备预配 SDK 使用的静态库。
1. 库必须实现以下标头文件中定义的函数：a. 对于自定义 TPM，需实现 `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_tpm_impl.h` 中定义的函数。
    b. 对于自定义 X.509，需实现 `\azure-iot-sdk-c\dps_client\adapters\custom_hsm_x509_impl.h` 中定义的函数。 
1. HSM 存储库还必须在应生成的存储库根目录中包含 `CMakeLists.txt` 文件。

### <a name="integrate-with-the-device-provisioning-service-client"></a>与设备预配服务客户端集成

库独立完成生成后，可以移动到 IoThub C-SDK 并在存储库中拉取：

1. 在以下 cmake 命令中提供自定义 HSM GitHub 存储库、库路径及其名称：
    ```cmd/sh
    cmake -Ddps_auth_type=<custom_hsm> -Ddps_hsm_custom_repo=<github_repo_name> -Ddps_hsm_custom_lib=<path_and_name_of library> <PATH_TO_AZURE_IOT_SDK>
    ```
   将此命令中的 `<custom_hsm>` 替换为 `tpm` 或 `x509`。 此命令在 `cmake` 目录中为自定义 HSM 存储库创建标记。 请注意，自定义 HSM 仍应基于 TPM 或 X.509 安全机制。

1. 在 Visual Studio 中打开 SDK 并生成它。 

    - 生成过程将克隆自定义存储库并生成库。
    - SDK 将尝试链接到 cmake 命令中定义的自定义 HSM。

1. 运行 `\azure-iot-sdk-c\dps_client\samples\dps_client_sample\dps_client_sample.c` 示例，验证 HSM 是否已正确实现。

<a id="extractsecurity"></a>
## <a name="extract-the-security-artifacts"></a>提取安全项目

下一步是在设备上提取 HSM 的安全项目。

1. 对于 TPM 设备，需要先通过 TPM 芯片制造商找到与其关联的“认可密钥”。 通过对认可密钥进行哈希处理，可为 TPM 设备派生唯一的“注册 ID”。 
2. 对于 X.509 设备，需要获取为设备颁发的证书 - 单个设备注册的最终实体证书和设备组注册的根证书。

必须具备这些安全项目才能向设备预配服务注册设备。 然后，预配服务将等待任何设备启动并随后与其连接。 有关如何使用这些安全项目创建注册的信息，请参阅[如何管理设备注册](how-to-manage-enrollments.md)。 

设备首次启动时，客户端 SDK 将与芯片交互以提取设备中的安全项目，并验证是否已向设备预配服务注册。 


## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>在设备上设置设备预配服务配置

设备制造过程中，最后一步是编写使用设备预配服务客户端 SDK 的应用程序，以向该服务注册设备。 此 SDK 为应用程序提供以下可用 API：

```C
typedef void(*DPS_REGISTER_DEVICE_CALLBACK)(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* user_context); // Callback to notify user of device registration results.
DPS_CLIENT_LL_HANDLE DPS_Client_LL_Create (const char* dps_uri, const char* scope_id, DPS_TRANSPORT_PROVIDER_FUNCTION protocol, DPS_CLIENT_ON_ERROR_CALLBACK on_error_callback, void* user_ctx); // Creates the IOTHUB_DPS_LL_HANDLE to be used in subsequent calls.
void DPS_Client_LL_Destroy(DPS_CLIENT_LL_HANDLE handle); // Frees any resources created by the IoTHub Device Provisioning Service module.
DPS_RESULT DPS_LL_Register_Device(DPS_LL_HANDLE handle, DPS_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, DPS_CLIENT_REGISTER_STATUS_CALLBACK status_cb, void* status_ctx); // Registers a device that has been previously registered with Device Provisioning Service
void DPS_Client_LL_DoWork(DPS_LL_HANDLE handle); // Processes the communications with the Device Provisioning Service and calls any user callbacks that are required.
```

使用前，请初始化变量 `dps_uri` 和`dps_scope_id`，如[本快速入门中“模拟设备的首次启动顺序”部分](./quick-create-simulated-device.md#firstbootsequence)中所述。 设备预配客户端注册 API `DPS_Client_LL_Create` 将连接到全局设备预配服务。 ID 范围由此服务生成，可保证唯一性。 它是不可变的，可用于唯一标识注册 ID。 `iothub_uri` 使 IoT 中心客户端注册 API `IoTHubClient_LL_CreateFromDeviceAuth` 能与正确的 IoT 中心连接。 


设备启动时，这些 API 可帮助设备连接设备预配服务并向其注册，获取 IoT 中心的相关信息并连接到此中心。 `dps_client/samples/dps_client_sample/dps_client_sample.c` 文件演示如何使用这些 API。 一般情况下，需要为客户端注册创建以下框架：

```C
static const char* dps_uri = "global.azure-devices-provisioning.net";
static const char* dps_scope_id = "[ID scope for your provisioning service]";
...
static void register_callback(DPS_RESULT register_result, const char* iothub_uri, const char* device_id, void* context)
{
    USER_DEFINED_INFO* user_info = (USER_DEFINED_INFO *)user_context;
    ...
    user_info. reg_complete = 1;
}
static void registation_status(DPS_REGISTRATION_STATUS reg_status, void* user_context)
{
}
int main()
{
    ...    
    security_device_init(); // initialize your HSM 

    DPS_CLIENT_LL_HANDLE handle = DPS_Client_LL_Create(dps_uri, dps_scope_id, dps_transport, on_dps_error_callback, &user_info); // Create your DPS client

    if (DPS_Client_LL_Register_Device(handle, register_callback, &user_info, register_status, &user_info) == IOTHUB_DPS_OK) {
        do {
            // The dps_register_callback is called when registration is complete or fails
            DPS_Client_LL_DoWork(handle);
        } while (user_info.reg_complete == 0);
    }
    DPS_Client_LL_Destroy(handle); // Clean up the DPS client
    ...
    iothub_client = IoTHubClient_LL_CreateFromDeviceAuth(user_info.iothub_uri, user_info.device_id, transport); // Create your IoT hub client and connect to your hub
    ...
}
```

可能需要先后使用模拟设备和测试服务安装程序来优化设备预配服务客户端注册应用程序。 应用程序在测试环境中正常运行后，便可为特定设备生成此应用程序并将可执行文件复制到设备映像。 此时还不能启动设备，需要[向设备预配服务注册设备](./tutorial-provision-device-to-hub.md#enrolldevice)，然后才能启动设备。 请参阅下面的步骤了解此过程。 

## <a name="clean-up-resources"></a>清理资源

此时可能已在门户中设置了设备预配和 IoT 中心服务。 如果要放弃设备预配设置，和/或延迟使用其中的任何服务，我们建议将它们关闭，避免产生不必要的费用。

1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择设备预配服务。 在“所有资源”边栏选项卡的顶部单击“删除”。  
1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心。 在“所有资源”边栏选项卡的顶部单击“删除”。  


## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 选择硬件安全模块
> * 为所选 HSM 生成设备预配客户端 SDK
> * 提取安全项目
> * 在设备上设置设备预配服务配置

前往下一教程，了解如何向 Azure IoT 中心设备预配服务注册设备以进行自动预配，从而将设备预配到 IoT 中心。

> [!div class="nextstepaction"]
> [将设备预配到 IoT 中心](tutorial-provision-device-to-hub.md)


