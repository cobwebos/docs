---
title: "将模拟设备预配到 Azure IoT 中心 | Microsoft Docs"
description: "Azure 快速入门 - 使用 Azure IoT 中心设备预配服务创建和预配模拟设备"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: d4eeb7a77d6336e241c196e4ad48af52d57af1d4
ms.contentlocale: zh-cn
ms.lasthandoff: 09/07/2017

---

# <a name="create-and-provision-a-simulated-device-using-iot-hub-device-provisioning-service-preview"></a>使用 IoT 中心设备预配服务（预览版）创建和预配模拟设备

以下步骤演示了如何在运行 Windows OS 的开发计算机上创建模拟设备、如何将 Windows TPM 模拟器作为设备的[硬件安全模块 (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) 运行，以及如何使用代码示例通过设备预配服务和 IoT 中心连接该模拟设备。 

在继续操作之前，请确保完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>准备开发环境 

1. 确保已在计算机上安装 Visual Studio 2015 或 [Visual Studio 2017](https://www.visualstudio.com/vs/)。 

2. 下载并安装 [CMake 生成系统](https://cmake.org/download/)。

3. 确保在计算机上安装 `git` 并将其添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。 

4. 打开命令提示符或 Git Bash。 为设备模拟代码示例克隆 GitHub 存储库：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. 在该 GitHub 存储库的本地副本中创建一个用于 CMake 生成过程的文件夹。 

    ```cmd/sh
    cd azure-iot-device-auth
    mkdir cmake
    cd cmake
    ```

6. 代码示例使用 Windows TPM 模拟器。 运行以下命令，启用 SAS 令牌身份验证。 该命令还会为模拟设备生成 Visual Studio 解决方案。

    ```cmd/sh
    cmake -Ddps_auth_type=tpm_simulator ..
    ```

7. 在单独的命令提示符处，导航到 GitHub 根文件夹，然后运行 [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) 模拟器。 该模拟器通过套接字在端口 2321 和 2322 上进行侦听。 请勿关闭此命令窗口；本快速入门指南自始至终都需让该模拟器保持运行状态。 

    ```cmd/sh
    .\azure-iot-device-auth\dps_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>在设备预配服务中创建设备注册条目

1. 打开在 cmake 文件夹中生成的名为 `azure_iot_sdks.sln` 的解决方案，将其内置到 Visual Studio 中。

2. 右键单击“tpm_device_provision”项目，然后选择“设为启动项目”。 运行解决方案。 输出窗口会显示进行设备注册所需的“认可密钥”和“注册 ID”。 记下这些值。 

3. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开设备预配服务。

4. 在设备预配服务摘要边栏选项卡上，选择“管理注册”。 选择“单个注册”选项卡，单击顶部的“添加”按钮。 选择“TPM”作为标识证明“机制”，并根据边栏选项卡的要求输入“注册 ID”和“认可密钥”。 完成后，单击“保存”按钮。 

    ![在门户边栏选项卡中输入设备注册信息](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   成功注册以后，设备的“注册 ID”会显示在“单个注册”选项卡下的列表中。 

<a id="firstbootsequence"></a>
## <a name="simulate-first-boot-sequence-for-the-device"></a>模拟设备的首次启动顺序

1. 在 Azure 门户中，选择设备预配服务的“概览”边栏选项卡，记下“全局设备终结点”和“ID 范围”的值。

    ![从门户边栏选项卡提取 DPS 终结点信息](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. 在计算机上的 Visual Studio 中，选择名为“dps_client_sample”的示例项目，打开 dps_client_sample.c 文件。

3. 为 `dps_scope_id` 变量指定“ID 范围”值。 请注意，`dps_uri` 变量的值与“全局设备终结点”的值相同。 

    ```c
    static const char* dps_uri = "global.azure-devices-provisioning.net";
    static const char* dps_scope_id = "[DPS Id Scope]";
    ```

4. 右键单击“dps_client_sample”项目，然后选择“设为启动项目”。 运行示例。 请注意相关消息，这些消息模拟设备启动后连接到设备预配服务以获取 IoT 中心信息的情况。 将模拟设备成功预配到与预配服务链接的 IoT 中心以后，设备 ID 会显示在该中心的“Device Explorer”边栏选项卡上。 

    ![设备注册到 IoT 中心](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请通过以下步骤删除通过本快速入门创建的所有资源。

1. 关闭计算机上的设备客户端示例输出窗口。
1. 关闭计算机上的 TPM 模拟器窗口。
1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择设备预配服务。 在“所有资源”边栏选项卡的顶部单击“删除”。  
1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心。 在“所有资源”边栏选项卡的顶部单击“删除”。  

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已在计算机上创建 TPM 模拟设备，并已使用 Azure IoT 中心设备预配服务将其预配到 IoT 中心。 若要深入了解设备预配，请继续学习本教程有关如何在 Azure 门户中进行设备预配服务设置的内容。 

> [!div class="nextstepaction"]
> [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)

