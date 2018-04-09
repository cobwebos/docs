---
title: 针对 Azure IoT 中心设备预配服务设置设备
description: 在设备制造过程中通过 IoT 中心设备预配服务设置设备以进行预配
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c885e4d5d747d913eaf0b7137b240950e920e7ff
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>使用 Azure IoT 中心设备预配服务设置设备以进行预配

前面的教程介绍了设置 Azure IoT 中心设备预配服务以将设备自动预配到 IoT 中心的方法。 本教程介绍如何在制造过程中设置设备，使之能够通过 IoT 中心进行自动预配。 设备在首先启动并连接到预配服务之后，即可根据其[证明机制](concepts-device.md#attestation-mechanism)进行预配。 本教程讨论以下过程：

> [!div class="checklist"]
> * 生成特定于平台的设备预配服务客户端 SDK
> * 提取安全项目
> * 创建设备注册软件

## <a name="prerequisites"></a>先决条件

继续之前，请按照以前的 [1 - 设置云资源](./tutorial-set-up-cloud.md)教程中的说明创建设备预配服务实例和 IoT 中心。

本教程使用[用于 C 存储库的 Azure IoT SDK 和库](https://github.com/Azure/azure-iot-sdk-c)，该存储库包含用于 C 的设备预配服务客户端 SDK。此 SDK 目前为运行在 Windows 或 Ubuntu 实现上的设备提供 TPM 和 X.509 支持。 本教程以 Windows 开发客户端的使用为基础，而使用该客户端的前提是基本熟悉 Visual Studio 2017 的使用。 

如果不熟悉自动预配过程，请务必在继续操作之前查看[自动预配概念](concepts-auto-provisioning.md)。 

## <a name="build-a-platform-specific-version-of-the-sdk"></a>生成特定于平台的 SDK 版本

设备预配服务客户端 SDK 有助于实现设备注册软件。 但在使用它之前，需根据开发客户端平台和证明机制生成一个 SDK 版本。 在本教程中，请针对支持的证明类型生成一个使用 Visual Studio 2017（基于 Windows 开发平台）的 SDK：

1. 安装所需的工具并克隆 GitHub 存储库，后者包含用于 C 的预配服务客户端 SDK：

   a. 确保已在计算机上安装 Visual Studio 2015 或 [Visual Studio 2017](https://www.visualstudio.com/vs/)。 必须启用[“使用 C++ 进行桌面开发”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷才能进行 Visual Studio 安装。

   b. 下载并安装 [CMake 生成系统](https://cmake.org/download/)。 在进行 CMake 安装**之前**，必须在计算机上安装包含“使用 C++ 进行桌面开发”工作负荷的 Visual Studio。

   c. 确保在计算机上安装 `git` 并将其添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解最新的 `git` 工具，其中包括 **Git Bash**，这是一个可以与本地 Git 存储库交互的命令行 Bash shell。 

   d. 打开 Git Bash，克隆“用于 C 的 Azure IoT SDK 和库”存储库。 克隆命令可能需要数分钟才能运行完毕，因为它还下载多个依赖性的子模块：
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   e. 在新创建的存储库子目录中创建新的 `cmake` 子目录：

   ```cmd/sh
   mkdir azure-iot-sdk-c/cmake
   ``` 

2. 从 Git Bash 命令提示符处转到 azure-iot-sdk-c 存储库的 `cmake` 子目录：

   ```cmd/sh
   cd azure-iot-sdk-c/cmake
   ```

3. 使用下述某个命令，针对开发平台和支持的证明机制之一生成 SDK（另请注意下面这两个尾随的句点符号）。 在完成后，CMake 会使用特定于设备的内容生成 `/cmake` 子目录：
    - 对于使用物理 TPM/HSM 或模拟 X.509 证书进行证明的设备：
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - 对于使用 TPM 模拟器进行证明的设备：
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

现在可以使用 SDK 生成设备注册代码了。 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>提取安全项目 

下一步是提取设备所用的证明机制的安全项目。 

### <a name="physical-device"></a>物理设备 

如果生成了可以通过物理 TPM/HSM 来使用证明的 SDK：

- 对于 TPM 设备，需要通过 TPM 芯片制造商确定与其关联的“认可密钥”。 通过对认可密钥进行哈希处理，可为 TPM 设备派生唯一的“注册 ID”。  

- 对于 X.509 设备，需要获取为设备颁发的证书 - 单个设备注册的最终实体证书和设备组注册的根证书。 

### <a name="simulated-device"></a>模拟设备

如果生成了可以通过模拟 TPM 或 X.509 证书来使用证明的 SDK：

- 对于模拟 TPM 设备：
   1. 在单独的/新的命令提示符处，导航到 `azure-iot-sdk-c` 子目录，然后运行 TPM 模拟器。 该模拟器通过套接字在端口 2321 和 2322 上进行侦听。 请勿关闭此命令窗口；以下快速入门自始至终都需让该模拟器保持运行状态。 

      从 `azure-iot-sdk-c` 子目录运行以下命令，以启动模拟器：

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

   2. 使用 Visual Studio 打开在 *cmake* 文件夹中创建的名为 `azure_iot_sdks.sln` 的解决方案，然后在“生成”菜单上使用“生成解决方案”命令来生成它。

   3. 在 Visual Studio 的“解决方案资源管理器”窗格中，导航到 **Provision\_Tools** 文件夹。 右键单击“tpm_device_provision”项目，然后选择“设为启动项目”。 

   4. 使用“调试”菜单上的任一“启动”命令来运行此解决方案。 输出窗口会显示 TPM 模拟器的“注册 ID”和“认可密钥”，这是进行设备登记和注册所需的。 复制这些值，供以后使用。 可以关闭此窗口（包含注册 ID 和认可密钥），但让在步骤 1 中启动的 TPM 模拟器窗口保持运行状态。

- 对于模拟 X.509 设备：
  1. 使用 Visual Studio 打开在 *cmake* 文件夹中创建的名为 `azure_iot_sdks.sln` 的解决方案，然后在“生成”菜单上使用“生成解决方案”命令来生成它。

  2. 在 Visual Studio 的“解决方案资源管理器”窗格中，导航到 **Provision\_Tools** 文件夹。 右键单击“dice\_device\_enrollment”项目，然后选择“设置为启动项目”。 
  
  3. 使用“调试”菜单上的任一“启动”命令来运行此解决方案。 在输出窗口中，当系统提示时输入 **i** 完成单个注册。 输出窗口会显示在本地为模拟设备生成的 X.509 证书。 将输出（从 *-----BEGIN CERTIFICATE-----* 开始，到第一个 *-----END CERTIFICATE-----* 结束）复制到剪贴板，确保将这两行也包括进去。 请注意，只需要输出窗口中的第一个证书。
 
  4. 创建名为 **_X509testcert.pem_** 的文件，在所选文本编辑器中将其打开，然后将剪贴板内容复制到该文件中。 保存此文件，因为稍后需要用它来进行设备注册。 注册软件在运行时使用自动预配期间使用的证书。    

在将设备注册到设备预配服务的过程中，这些安全项目是必需的。 预配服务会等待设备启动并随后与其连接。 设备首次启动时，客户端 SDK 逻辑会与芯片（或模拟器）交互以提取设备中的安全项目，并验证是否已向设备预配服务注册。 

## <a name="create-the-device-registration-software"></a>创建设备注册软件

最后一步是编写一个注册应用程序，以便使用设备预配服务客户端 SDK 将设备注册到 IoT 中心服务。 

> [!NOTE]
> 对于这一步，我们假定使用的是模拟设备，通过从工作站运行 SDK 示例注册应用程序的方式来完成。 不过，如果生成需要部署到物理设备的注册应用程序，则适用的概念是相同的。 

1. 在 Azure 门户中，选择设备预配服务的“概览”边栏选项卡，复制“ID 范围”值。 ID 范围由此服务生成，可保证唯一性。 它是不可变的，可用于唯一标识注册 ID。

    ![从门户边栏选项卡提取 DPS 终结点信息](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

2. 在计算机上的 Visual Studio *解决方案资源管理器*中，导航到 **Provision\_Samples** 文件夹。 选择名为 **prov\_dev\_client\_sample** 的示例项目，打开 **prov\_dev\_client\_sample.c** 源文件。

3. 将步骤 1 中获得的“ID 范围”值分配给 `id_scope` 变量（删除左侧的 /`[` 和右侧的 /`]` 方括号）： 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    例如，IoT 中心客户端注册 API `IoTHubClient_LL_CreateFromDeviceAuth` 可以使用 `global_prov_uri` 变量通过指定的设备预配服务实例进行连接。

4. 在同一文件的 **main()** 函数中，注释/取消注释与设备的注册软件所用证明机制（TPM 或 X.509）相匹配的 `hsm_type` 变量： 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. 保存所做的更改，然后从“生成”菜单中选择“生成解决方案”，以便重新生成 **prov\_dev\_client\_sample** 示例。 

6. 右键单击 **Provision\_Samples** 文件夹中的 **prov\_dev\_client\_sample** 项目，然后选择“设置为启动项目”。 目前请勿运行示例应用程序。

> [!IMPORTANT]
> 目前请勿运行/启动设备！ 在启动设备之前，需先将设备注册到设备预配服务，以便完成此过程。 下面的“后续步骤”部分会引导你阅读下一篇文章。

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>在注册过程中使用的 SDK API（仅供参考）

例如，此 SDK 提供下述可供应用程序在注册过程中使用的 API。 设备启动时，这些 API 可帮助设备连接设备预配服务并向其注册。 反过来，设备可以接收必要的信息，以便建立到 IoT 中心实例的连接：

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

你可能还会发现，需要先后使用模拟设备和测试性服务安装程序来优化设备预配服务客户端注册应用程序。 应用程序在测试环境中正常运行后，便可为特定设备生成此应用程序并将可执行文件复制到设备映像。 

## <a name="clean-up-resources"></a>清理资源

目前，你可能正在门户中预配设备并运行 IoT 中心服务。 若要放弃设备预配设置并且/或者将本教程系列的完成时间延后，建议将它们关闭，避免产生不必要的费用。

1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择设备预配服务。 在“所有资源”边栏选项卡的顶部单击“删除”。  
2. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心。 在“所有资源”边栏选项卡的顶部单击“删除”。  

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 生成特定于平台的设备预配服务客户端 SDK
> * 提取安全项目
> * 创建设备注册软件

前往下一教程，了解如何向 Azure IoT 中心设备预配服务注册设备以进行自动预配，从而将设备预配到 IoT 中心。

> [!div class="nextstepaction"]
> [将设备预配到 IoT 中心](tutorial-provision-device-to-hub.md)

