---
title: Azure 操作方法 - 如何将不同的证明机制与 Azure 中的设备预配服务客户端 SDK 配合使用
description: Azure 操作方法 - 如何将不同的证明机制与 Azure 中的设备预配服务客户端 SDK 配合使用
author: yzhong94
ms.author: yizhon
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.custom: mvc
ms.openlocfilehash: 2d5bc3d0167c08c41b38bb324d55c239041f1fba
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34630419"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>如何将不同的证明机制与用于 C 的设备预配服务客户端 SDK 配合使用

本文展示了如何将不同的[证明机制](concepts-security.md#attestation-mechanism)与用于 C 的设备预配服务客户端 SDK 配合使用。可以使用物理设备，也可以使用模拟器。 预配服务支持下述两类证据机制的身份验证：X **.** 509 和受信任的平台模块 (TPM)。

## <a name="prerequisites"></a>先决条件

根据[创建和预配模拟设备](./quick-create-simulated-device.md)指南中“准备开发环境”部分的说明准备开发环境。

### <a name="choose-an-attestation-mechanism"></a>选择证明机制

作为设备制造商，首先需要基于受支持类型之一选择一种证明机制。 目前，[用于 C 的设备预配服务客户端 SDK](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) 支持以下证明机制： 

- [受信任的平台模块 (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module)：TPM 是一种确定的标准，适用于大多数基于 Windows 的设备平台和数种基于 Linux/Ubuntu 的设备。 作为设备制造商，如果其设备上运行以上任一种 OS，并且正在寻找某种已建立的标准，则可以选择此证明机制。 使用 TPM 芯片可以向设备预配服务单独注册每台设备。 出于开发目的，可以在 Windows 或 Linux 开发计算机上使用 TPM 模拟器。

- [X.509](https://cryptography.io/en/latest/x509/)：X.509 证书可以存储在称为[硬件安全模块 (HSM)](concepts-security.md#hardware-security-module) 的相对较新的芯片中。 Microsoft 内部也正开展基于 RIoT 或 DICE 芯片的工作，目的是实施 X.509 证书。 使用 X.509 芯片可以在门户中进行批量设备注册。 它还支持 Windows 以外的某些 OS，如 embedOS。 出于开发目的，设备预配服务客户端 SDK 支持 X.509 设备模拟器。 

有关详细信息，请参阅 IoT 中心设备预配服务[安全性概念](concepts-security.md)和[自动预配概念](/azure/iot-dps/concepts-auto-provisioning)。

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>为受支持的证明机制启用身份验证

必须先为物理设备或模拟器启用 SDK 身份验证模式（X **.** 509 或 TPM），然后才能在 Azure 门户中注册它们。 首先，导航到 azure-iot-sdk-c 的根文件夹。 然后，根据所选身份验证模式运行指定的命令：

### <a name="use-x509-with-simulator"></a>对模拟器使用 X **.** 509

预配服务配备了一个设备标识组合引擎 (DICE) 模拟器，该模拟器可生成用于对设备进行身份验证的 X **.** 509 证书。 若要启用 X **.** 509 身份验证，请运行以下命令： 

```
cmake -Ddps_auth_type=x509 ..
```

有关带 DICE 的硬件的信息，请参阅[此处](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)。

### <a name="use-x509-with-hardware"></a>对硬件使用 X **.** 509

在其他硬件上预配服务可与 X **.** 509 配合使用。 硬件和 SDK 之间的接口需要建立连接。 请咨询 HSM 制造商，了解接口上的信息。

### <a name="use-tpm"></a>使用 TPM

预配服务可以使用 SAS 令牌连接到 Windows 和 Linux 硬件 TPM 芯片。 若要启用 TPM 身份验证，请运行以下命令：

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>对模拟器使用 TPM

如果没有带 TPM 芯片的设备，可在 Windows OS 上将模拟器用于开发目的。 若要启用 TPM 身份验证并运行 TPM 模拟器，请运行以下命令：

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>生成 SDK
在创建设备注册之前生成 SDK。

### <a name="linux"></a>Linux
- 在 Linux 中生成 SDK：
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- 若要生成调试二进制文件，请将相应 CMake 选项添加到项目生成命令，例如：
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- 有多个 [CMake 配置选项](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html)可用于生成 SDK。 例如，可以通过将参数添加到 CMake 项目生成命令来禁用其中一个可用协议堆栈：
    ```
    cmake -Duse_amqp=OFF ..
    ```
- 也可以生成并运行单元测试：
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- 若要在 Windows 中生成 SDK，请执行以下步骤生成项目文件：
    - 打开“VS2015 开发人员命令提示符”
    - 从存储库的根目录运行以下 CMake 命令：
      ```
      cd azure-iot-sdk-c
      mkdir cmake
      cd cmake
      cmake -G "Visual Studio 14 2015" ..
      ```
    此命令生成 x86 库。 若要针对 x64 生成，请修改 cmake 生成器参数： 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- 如果项目生成成功完成，应在 `cmake` 文件夹下看到 Visual Studio 解决方案文件 (.sln)。 若要生成 SDK，请执行以下操作：
    - 在 Visual Studio 中打开 **cmake\azure_iot_sdks.sln** 并生成它，**或者**
    - 在用于生成项目文件的命令提示符下运行以下命令：
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- 若要生成调试二进制文件，请使用相应的 MSBuild 参数： 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- 有多个 CMake 配置选项可用于生成 SDK。 例如，可以通过将参数添加到 CMake 项目生成命令来禁用其中一个可用协议堆栈：
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- 另外，也可以生成并运行单元测试：
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>要包括的库
- 以下库应包括在 SDK 中：
    - 预配服务：dps_http_transport、dps_client、dps_security_client
    - IoTHub 安全：iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>在设备预配服务中创建设备注册条目

### <a name="tpm"></a>TPM
如果使用 TPM，请按照[使用 IoT 中心设备预配服务创建和预配模拟设备](./quick-create-simulated-device.md)中的说明，在设备预配服务中创建设备注册项目并模拟首次启动。

### <a name="x509"></a>X **.** 509
1. 若要通过预配服务注册设备，需要记下每个设备的认可密钥和注册 ID，客户端 SDK 提供的预配工具中显示了这些信息。 运行以下命令输出根 CA 证书（对于注册组）和叶证书（对于单个注册）：
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开 DPS 服务。
   - X **.** 509 单个注册：在“预配服务摘要”边栏选项卡上，选择“管理注册”。 选择“单个注册”选项卡，单击顶部的“添加”按钮。 选择 **X**.**509** 作为标识证明*机制*，根据边栏选项卡的要求上传叶证书。 完成后，单击“保存”按钮。 
   - X **.** 509 组注册：在“预配服务摘要”边栏选项卡上，选择“管理注册”。 选择“组注册”选项卡，单击顶部的“添加”按钮。 选择 **X**.**509** 作为标识证明*机制*，输入组名和证书名称，根据边栏选项卡的要求上传 CA/中间证书。 完成后，单击“保存”按钮。 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>使用自定义证明机制为设备启用身份验证（可选）

> [!NOTE]
> 本部分仅适用于需要支持（用于 C 的设备预配服务客户端 SDK 当前不支持的）自定义平台或证明机制的设备。另请注意，该 SDK 经常使用术语“HSM”作为“证明机制”的一般代用词。

首先，需要为你的自定义证明机制开发一个存储库和库：

1. 开发一个库来访问你的证明机制。 此项目需要生成可供设备预配 SDK 使用的静态库。

2. 在库中实现以下标头文件中定义的函数： 

    - 对于自定义 TPM：实现在 [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api) 下定义的函数。  
    - 对于自定义 X.509：实现在 [HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api) 下定义的函数。 

在库成功地自行生成以后，需要通过根据库进行链接来将其与设备预配服务客户端 SDK 进行集成。 :

1. 在下面的 `cmake` 命令中提供自定义 GitHub 存储库和库：
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. 打开由 CMake 生成的 Visual Studio 解决方案文件 (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`)，然后生成该文件。 

    - 生成过程会编译 SDK 库。
    - SDK 会尝试链接到 `cmake` 命令中定义的自定义库。

3. 若要验证是否正确实现了自定义证明机制，请运行“Provision_Samples”下的“prov_dev_client_ll_sample”示例应用（在 `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample` 下）。

## <a name="connecting-to-iot-hub-after-provisioning"></a>在预配后连接到 IoT 中心

使用预配服务预配设备后，此 API 便使用指定的身份验证模式（X **.** 509 或 TPM）来与 IoT 中心进行连接： 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```

