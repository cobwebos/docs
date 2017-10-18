---
title: "Azure 操作方法 - 如何将不同的硬件安全模型与 Azure 中的设备预配服务客户端配合使用 | Microsoft Docs"
description: "Azure 操作方法 - 如何将不同的硬件安全模型与 Azure 中的设备预配服务客户端配合使用"
services: iot-dps
keywords: 
author: yzhong94
ms.author: yizhon
ms.date: 08/28/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: 
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 620d86b62cf43c3e1a5f7f5c724fcf00174f30e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-different-hardware-security-modules-with-device-provisioning-service-client-sdk"></a>如何将不同硬件安全模块与设备预配服务客户端 SDK 配合使用
这些步骤演示如何使用物理设备和模拟器在 C 中将不同[硬件安全模块 (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) 与设备预配服务客户端 SDK 配合使用。  预配服务支持两种身份验证模式：X**.**509 和受信任的平台模块 (TPM)。

## <a name="prerequisites"></a>先决条件

根据 [创建和预配模拟设备] (./quick-create-simulated-device.md) 指南中标题为“准备开发环境”一节准备开发环境。

## <a name="enable-authentication-with-different-hsms"></a>使用不同 HSM 启用身份验证

必须先为物理设备或模拟器启用身份验证模式（X**.**509 或 TPM），然后它们才能在 Azure 门户中注册。  导航到 azure-iot-sdk-c 的根文件夹。  根据所选的身份验证模式，运行指定的命令。

### <a name="use-x509-with-simulator"></a>对模拟器使用 X**.**509

预配服务配备了一个设备标识组合引擎 (DICE) 模拟器，该模拟器可生成用于对设备进行身份验证的 X**.**509 证书。  运行以下命令以启用 X**.**509 身份验证：

```
cmake -Ddps_auth_type=x509 ..
```

有关带 DICE 的硬件的信息，请参阅[此处](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/)。

### <a name="use-x509-with-hardware"></a>对硬件使用 X**.**509

在其他硬件上预配服务可与 X**.**509 配合使用。  硬件和 SDK 之间的接口需要建立连接。  请咨询 HSM 制造商，了解接口上的信息。

### <a name="use-tpm"></a>使用 TPM

预配服务可以使用 SAS 令牌连接到 Windows 和 Linux 硬件 TPM 芯片。  运行以下命令以启用 TPM 身份验证：

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>对模拟器使用 TPM

如果没有带 TPM 芯片的设备，可在 Windows OS 上将模拟器用于开发目的。  运行以下命令以启用 TPM 身份验证，并运行 TPM 模拟器：

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

## <a name="create-a-device-enrollment-entry-in-dps"></a>在 DPS 中创建设备注册项目

### <a name="tpm"></a>TPM
如果使用 TPM，请按照[使用 IoT 中心设备预配服务创建和预配模拟设备](./quick-create-simulated-device.md)中的说明在 DPS 中创建设备注册项目并模拟首次启动。

### <a name="x509"></a>X**.**509
1. 若要通过预配服务注册设备，需要记下每个设备的认可密钥和注册 ID，客户端 SDK 提供的预配工具中显示了这些信息。 运行以下命令输出根 CA 证书（适用于注册组）和签名者证书（适用于单个注册）：
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开 DPS 服务。
   - X**.**509 单个注册：在“预配服务摘要”边栏选项卡上，选择“管理注册”。 选择“单个注册”选项卡，单击顶部的“添加”按钮。 选择 **X**.**509** 作为标识证明*机制*，上传边栏选项卡所需的签名者证书。 完成后，单击“保存”按钮。 
   - X**.**509 组注册：在“预配服务摘要”边栏选项卡上，选择“管理注册”。 选择“组注册”选项卡，单击顶部的“添加”按钮。 选择 **X**.**509** 作为标识证明*机制*，输入组名和证书名称，上传边栏选项卡所需的根 CA 证书。 完成后，单击“保存”按钮。 

## <a name="connecting-to-iot-hub-after-provisioning"></a>在预配后连接到 IoT 中心

使用预配服务预配设备后，此 API 便立即使用 HSM 身份验证模式与 IoT 中心连接： 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
