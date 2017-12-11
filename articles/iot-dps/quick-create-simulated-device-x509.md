---
title: "将 X.509 模拟设备预配到 Azure IoT 中心 | Microsoft Docs"
description: "Azure 快速入门 - 使用 Azure IoT 中心设备预配服务创建和预配 X.509 模拟设备"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/08/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: a51debfc80c28cd7d7e271448e30619f2bc72f86
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="create-and-provision-an-x509-simulated-device-using-iot-hub-device-provisioning-service"></a>使用 IoT 中心设备预配服务创建和预配 X.509 模拟设备
> [!div class="op_single_selector"]
> * [TPM](quick-create-simulated-device.md)
> * [X.509](quick-create-simulated-device-x509.md)

以下步骤演示了如何在运行 Windows OS 的开发计算机上模拟 X.509 设备，以及如何使用代码示例通过设备预配服务和 IoT 中心连接该模拟设备。 

在继续操作之前，请确保完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>准备开发环境 

1. 确保已在计算机上安装 Visual Studio 2015 或 [Visual Studio 2017](https://www.visualstudio.com/vs/)。 必须启用“使用 C++ 进行桌面开发”工作负荷才能进行 Visual Studio 安装。

2. 下载并安装 [CMake 生成系统](https://cmake.org/download/)。

3. 确保在计算机上安装 `git` 并将其添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。 

4. 打开命令提示符或 Git Bash。 为设备模拟代码示例克隆 GitHub 存储库：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. 在该 GitHub 存储库的本地副本中创建一个用于 CMake 生成过程的文件夹。 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. 运行以下命令，为预配客户端创建 Visual Studio 解决方案。

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON ..
    ```


## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>在设备预配服务中创建设备注册条目

1. 打开在 cmake 文件夹中生成的名为 `azure_iot_sdks.sln` 的解决方案，将其内置到 Visual Studio 中。

2. 右键单击 **Provision\_Samples** 文件夹中的 **dice\_device\_enrollment** 项目，然后选择“设置为启动项目”。 运行解决方案。 在输出窗口中，当系统提示时输入 `i` 进行单独注册。 输出窗口会显示在本地为模拟设备生成的 X.509 证书。 将输出（从 -----BEGIN CERTIFICATE----- 开始，到 -----END PUBLIC KEY----- 结束）复制到剪贴板，确保将这两行也包括进去。 
 
3. 在 Windows 计算机上创建名为 **_X509testcertificate.pem_** 的文件，在所选编辑器中将其打开，然后将剪贴板内容复制到该文件中。 保存文件。 

4. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开预配服务。

4. 在“设备预配服务摘要”边栏选项卡上，选择“管理注册”。 选择“单个注册”选项卡，单击顶部的“添加”按钮。 

5. 在“添加注册列表项”下，输入以下信息：
    - 选择“X.509”作为标识证明机制。
    - 使用“文件资源管理器”小组件，在“证书 .pem 或 .cer 文件”下选择在前述步骤中创建的证书文件 **_X509testcertificate.pem_**。
    - （可选）可以提供以下信息：
        - 选择与预配服务链接的 IoT 中心。
        - 输入唯一设备 ID。 为设备命名时，请确保避免使用敏感数据。 
        - 使用设备所需的初始配置更新“初始设备孪生状态”。
    - 完成后，单击“保存”按钮。 

    ![在门户边栏选项卡中输入 X.509 设备注册信息](./media/quick-create-simulated-device-x509/enter-device-enrollment.png)  

   成功注册以后，X.509 设备会在“单独注册”选项卡的“注册 ID”列下显示为 **riot-device-cert**。 



<a id="firstbootsequence"></a>
## <a name="simulate-first-boot-sequence-for-the-device"></a>模拟设备的首次启动顺序

1. 在 Azure 门户中，选择设备预配服务的“概览”边栏选项卡，记下“ID 范围”的值。

    ![从门户边栏选项卡提取 DPS 终结点信息](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在计算机的 Visual Studio 中，导航到 **Provision\_Samples** 文件夹下名为 **prov\_dev\_client\_sample** 的示例项目，然后打开 **prov\_dev\_client\_sample.c** 文件。

3. 为 `scope_id` 变量指定“ID 范围”值。 

    ```c
    static const char* scope_id = "[ID Scope]";
    ```

4. 右键单击“prov\_dev\_client\_sample”项目，然后选择“设为启动项目”。 运行示例。 请注意相关消息，这些消息模拟设备启动后连接到设备预配服务以获取 IoT 中心信息的情况。 查找指示已成功注册到中心的消息：已从 yourhuburl 服务收到注册信息!。 系统提示时，关闭该窗口。

5. 在门户中导航到已链接到预配服务的 IoT 中心，然后打开“Device Explorer”边栏选项卡。 将模拟的 X.509 设备成功预配到中心以后，设备 ID 会显示在“Device Explorer”边栏选项卡上，“状态”为“已启用”。 请注意，如果在运行示例设备应用程序之前已打开边栏选项卡，则可能需要单击顶部的“刷新”按钮。 

    ![设备注册到 IoT 中心](./media/quick-create-simulated-device/hub-registration.png) 

> [!NOTE]
> 如果从设备的注册项中的默认值更改了“初始设备孪生状态”，则它会从中心拉取所需的孪生状态，并执行相应的操作。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。
>


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请通过以下步骤删除通过本快速入门创建的所有资源。

1. 关闭计算机上的设备客户端示例输出窗口。
1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择设备预配服务。 在“所有资源”边栏选项卡的顶部单击“删除”。  
1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心。 在“所有资源”边栏选项卡的顶部单击“删除”。  

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已在 Windows 计算机上创建模拟 X.509 设备，并已使用 Azure IoT 中心设备预配服务将其预配到 IoT 中心。 若要深入了解设备预配，请继续学习本教程有关如何在 Azure 门户中进行设备预配服务设置的内容。 

> [!div class="nextstepaction"]
> [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)
