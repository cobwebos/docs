---
title: Provision legacy devices using symmetric keys - Azure IoT Hub Device Provisioning Service
description: 如何使用对称密钥通过设备预配服务实例预配旧设备
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.openlocfilehash: 3e3b54592608f5c39d618f5ceda40747ad4fd0fe
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209915"
---
# <a name="how-to-provision-legacy-devices-using-symmetric-keys"></a>使用对称密钥预配旧设备

许多旧设备的常见问题是它们的标识通常由单段信息组成。 该信息通常为 MAC 地址或序列号。 旧设备可能没有证书、TPM 或可用于安全地识别设备的任何其他安全功能。 IoT 中心设备预配服务包含对称密钥证明。 对称密钥证明可以用于根据 MAC 地址或序列号等信息标识设备。

如果安装[硬件安全模块 (HSM)](concepts-security.md#hardware-security-module) 和证书比较轻松，这可能是标识和预配设备更好的方式。 这种方式可避免更新部署到所有设备上的代码，并且不必在设备映像中嵌入密钥。

本文假定：HSM 或证书都是不可行的选择。 但你有一些更新设备代码的方法，可供使用设备预配服务来预配设备。 

本文还假定，在安全的环境中进行设备更新，以防发生对组主密钥或派生的设备密钥未经授权的访问。

本文面向基于 Windows 的工作站。 但是，你也可以在 Linux 上执行过程。 对于 Linux 示例，请参阅[如何进行多租户预配](how-to-provision-multitenant.md)。

> [!NOTE]
> The sample used in this article is written in C. There is also a [C# device provisioning symmetric key sample](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/SymmetricKeySample) available. To use this sample, download or clone the [azure-iot-samples-csharp](https://github.com/Azure-Samples/azure-iot-samples-csharp) repository and follow the in-line instructions in the sample code. You can follow the instructions in this article to create a symmetric key enrollment group using the portal and to find the ID Scope and enrollment group primary and secondary keys needed to run the sample. You can also create individual enrollments using the sample.

## <a name="overview"></a>概述

将基于标识每个设备的信息为该设备定义唯一注册 ID。 例如，MAC 地址或序列号。

将通过设备预配服务创建使用[对称密钥证明](concepts-symmetric-key-attestation.md)的注册组。 注册组包括组的主密钥。 将使用该主密钥对每个唯一注册 ID 进行哈希处理，为各设备生成一个唯一设备密钥。 设备将该派生设备密钥用于其唯一注册 ID，在设备预配服务中用作证明并且该设备被分配给 IoT 中心。

本文所示的设备代码采用[快速入门：使用对称密钥预配模拟设备](quick-create-simulated-device-symm-key.md)中所用的模式。 该代码使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的示例来模拟设备。 如快速入门文章中所示，模拟设备将使用注册组（而不是各个注册）进行证明。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>必备组件

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)快速入门。
* 启用了[“使用 C++ 的桌面开发”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷的 [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 或更高版本。
* 已安装最新版本的 [Git](https://git-scm.com/download/)。


## <a name="prepare-an-azure-iot-c-sdk-development-environment"></a>准备 Azure IoT C SDK 开发环境

在本部分，你将准备一个用于生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 

SDK 包含模拟设备的示例代码。 该模拟设备将尝试在设备启动顺序期间进行预配。

1. 下载 [CMake 生成系统](https://cmake.org/download/)。

    在进行 `CMake` 安装**之前**，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负荷）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。

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


## <a name="create-a-symmetric-key-enrollment-group"></a>创建对称密钥注册组

1. 登录到 [Azure 门户](https://portal.azure.com)，并打开你的设备预配服务实例。

2. 选择“管理注册”选项卡，然后单击页面顶部的“添加注册组”按钮。 

3. 在“添加注册组”中输入以下信息，然后单击“保存”按钮。

   - **组名**：输入 **mylegacydevices**。

   - 证明类型：选择“对称密钥”。

   - 自动生成密钥：选中此复选框。

   - **选择将设备分配到中心的方式**：选择**静态配置**以便可以分配到特定的中心。

   - **选择可将该组分配到的 IoT 中心**：选择某一个中心。

     ![为对称密钥证明添加注册组](./media/how-to-legacy-device-symm-key/symm-key-enrollment-group.png)

4. 保存注册后，将生成“主要密钥”和“辅助密钥”，并将其添加到注册条目。 对称密钥注册组在“注册组”选项卡的“组名”栏下显示为“mylegacydevices”。 

    打开注册并复制生成的“主要密钥”的值。 此密钥是组主密钥。


## <a name="choose-a-unique-registration-id-for-the-device"></a>选择设备的唯一注册 ID

必须定义唯一注册 ID 来标识每个设备。 可以使用 MAC 地址、序列号或设备中的任何唯一信息。 

在此示例中，我们使用 MAC 地址和序列号的组合，构成以下注册 ID 字符串。

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

为设备创建一个唯一注册 ID。 有效字符为小写字母数字和短划线（“-”）。


## <a name="derive-a-device-key"></a>派生一个设备密钥 

若要生成设备密钥，请使用组主键计算设备的唯一注册 ID 的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)，并将结果转换为 Base64 格式。

不要在设备代码中包含你的组主键。


#### <a name="linux-workstations"></a>Linux 工作站

如果使用的是 Linux 工作站，可以使用 openssl 生成派生的设备密钥，如以下示例中所示。

将“键”值替换为前面记录的“主键”。

用注册 ID 替换 REG_ID 值。

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


#### <a name="windows-based-workstations"></a>基于 Windows 的工作站

如果使用的是基于 Windows 的工作站，可以使用 PowerShell 生成派生的设备密钥，如以下示例中所示。

将“键”值替换为前面记录的“主键”。

用注册 ID 替换 REG_ID 值。

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```


设备将使用派生的设备密钥和唯一注册 ID，于预配期间在注册组中执行对称密钥证明。



## <a name="create-a-device-image-to-provision"></a>创建用于预配的设备映像

在本部分，你将更新你在较早前设置的位于 Azure IoT C SDK 中的名为 prov\_dev\_client\_sample 的预配示例。 

此示例代码模拟将预配请求发送到你的设备预配服务实例的设备启动序列。 启动序列将会使设备被识别并分配到你在注册组上配置的 IoT 中心。

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

6. 在“prov\_dev\_client\_sample.c”中，查找对 `prov_dev_set_symmetric_key_info()` 的调用并添加注释。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    取消注释该函数调用，并将占位符值（包括尖括号）替换为设备的唯一注册 ID 以及生成的派生设备密钥。

    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6", "Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=");
    ```
   
    保存文件。

7. 右键单击“prov\_dev\_client\_sample”项目，然后选择“设为启动项目”。 

8. 在 Visual Studio 菜单中，选择“调试” > “开始执行(不调试)”以运行该解决方案。 在重新生成项目的提示中单击“是”，以便在运行项目之前重新生成项目。

    以下输出是模拟设备成功启动并连接到要分配到 IoT 中心的预配服务实例的示例：

    ```cmd
    Provisioning API Version: 1.2.8

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: 
    test-docs-hub.azure-devices.net, deviceId: sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

    Press enter key to exit:
    ```

9. In the portal, navigate to the IoT hub your simulated device was assigned to and click the **IoT Devices** tab. On successful provisioning of the simulated to the hub, its device ID appears on the **IoT Devices** blade, with *STATUS* as **enabled**. 你可能需要单击顶部的“刷新”按钮。 

    ![设备注册到 IoT 中心](./media/how-to-legacy-device-symm-key/hub-registration.png) 



## <a name="security-concerns"></a>安全注意事项

请注意，这会使派生的设备密钥成为映像的一部分，不是推荐的安全最佳做法。 充分体现了安全性和易用性各有利弊。 





## <a name="next-steps"></a>后续步骤

* To learn more Reprovisioning, see [IoT Hub Device reprovisioning concepts](concepts-device-reprovision.md) 
* [快速入门：使用对称密钥预配模拟设备](quick-create-simulated-device-symm-key.md)
* To learn more Deprovisioning, see [How to deprovision devices that were previously auto-provisioned](how-to-unprovision-devices.md) 











