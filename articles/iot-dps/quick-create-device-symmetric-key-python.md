---
title: 快速入门 - 通过 Python 使用对称密钥将设备预配到 Azure IoT 中心
description: 在本快速入门中，你将通过 Azure IoT 中心设备预配服务 (DPS) 使用 Azure IoT Python SDK 将对称密钥设备预配到 IoT 中心
author: wesmc7777
ms.author: wesmc
ms.date: 06/29/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom: mvc
ms.openlocfilehash: 7c8254609b8db9c2796d431485507de7083509aa
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2020
ms.locfileid: "85569347"
---
# <a name="quickstart-provision-a-python-device-with-symmetric-keys"></a>快速入门：使用对称密钥预配 Python 设备

本快速入门将介绍如何使用 Python 将 Windows 开发计算机作为设备预配到 IoT 中心。 此设备将使用对称密钥对设备预配服务 (DPS) 实例进行身份验证，以便被分配到 IoT 中心。 DPS 将根据单个注册来识别经过身份验证的设备，并将其分配到 IoT 中心。 [Azure IoT Python SDK ](https://github.com/Azure/azure-iot-sdk-python) 中的示例代码将用于预配设备。 

虽然本文演示了使用单个注册进行预配，但你也可以使用注册组。 使用注册组时有一些不同之处。 例如，必须将派生的设备密钥与设备的唯一注册 ID 一起使用。 虽然对称密钥注册组不限于旧设备，但[如何使用对称密钥证明预配旧设备](how-to-legacy-device-symm-key.md)提供了注册组示例。 有关详细信息，请参阅[对称密钥证明的组注册](concepts-symmetric-key-attestation.md#group-enrollments)。

如果你不熟悉自动预配过程，请查看[自动预配的概念](concepts-auto-provisioning.md)。 

另外，在继续学习本快速入门之前，请确保已完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。 本快速入门需要你已创建设备预配服务实例。

本文面向基于 Windows 的工作站。 但是，你也可以在 Linux 上执行过程。 对于 Linux 示例，请参阅[如何进行多租户预配](how-to-provision-multitenant.md)。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>先决条件

* 确保在基于 Windows 的计算机上安装了 [Python 3.7](https://www.python.org/downloads/) 或更高版本。 可以通过运行 `python --version` 来检查 Python 版本。

* 已安装最新版本的 [Git](https://git-scm.com/download/)。

<a id="setupdevbox"></a>

## <a name="prepare-the-python-sdk-environment"></a>准备 Python SDK 环境 

1. 确保在计算机上安装 Git 并将其添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。 

2. 打开命令提示符。 克隆 Azure IoT Python SDK 的 GitHub 存储库：
    
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```
3. 导航到 provision_symmetric_key.py 示例文件所在的 `azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios` 目录。
   
   ```console
   cd azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios
   ```
4. 通过运行以下命令来安装 azure-iot-device 库。

    ```console
    pip install azure-iot-device
    ```


## <a name="create-a-device-enrollment"></a>创建设备注册

1. 登录到 [Azure 门户](https://portal.azure.com)，选择左侧菜单上的“所有资源”按钮，打开设备预配服务 (DPS) 实例。

2. 选择“管理注册”选项卡，然后选择顶部的“添加个人注册”按钮 。 

3. 在“添加注册”面板中输入以下信息，然后按“保存”按钮 。

   - **机制**：选择“对称密钥”作为标识证明“机制”。

   - **自动生成密钥**：选中此框。

   - **注册 ID**：输入注册 ID 以标识注册。 仅使用小写字母数字和短划线（“-”）字符。 例如，symm-key-python-device-008。

   - **IoT 中心设备 ID：** 输入设备标识符。 例如，python-device-008。

     ![在门户中为对称密钥证明添加单个注册](./media/quick-create-device-symm-key-python/create-individual-enrollment-python.png)

4. 保存注册后，将生成“主要密钥”和“辅助密钥”，并将其添加到注册条目 。 对称密钥设备注册会在“单个注册”选项卡的“注册 ID”列下显示为“symm-key-python-device-008” 。 

5. 打开注册并复制生成的“主要密钥”的值。 稍后你将在添加用于设备预配示例代码的环境变量时使用此密钥值和“注册 ID”。



<a id="firstbootsequence"></a>

## <a name="prepare-the-device-provisioning-code"></a>准备设备预配代码

在本部分中，你将添加以下四个环境变量，这些变量将作为参数用于对称密钥设备的设备预配示例代码。 

* `PROVISIONING_HOST`
* `PROVISIONING_IDSCOPE`
* `PROVISIONING_REGISTRATION_ID`
* `PROVISIONING_SYMMETRIC_KEY`

预配代码将根据这些变量联系 DPS 实例，以便对设备进行身份验证。 然后，会根据单个注册配置将设备分配到已链接到 DPS 实例的 IoT 中心。 预配后，示例代码会将一些测试遥测发送到 IoT 中心。

1. 在 [Azure 门户](https://portal.azure.com)中的“设备预配服务”菜单上，选择“概述”，并复制“服务终结点”和“ID 范围” 。 你会将这些值用于 `PROVISIONING_HOST` 和 `PROVISIONING_IDSCOPE` 环境变量。

    ![服务信息](./media/quick-create-device-symm-key-python/extract-dps-endpoints.png)

2. 在 Python 命令提示符中，使用已复制的值添加环境变量。 

    以下命令是展示命令语法的示例。 确保使用正确的值。

    ```console
    set PROVISIONING_HOST=test-dps-docs.azure-devices-provisioning.net
    ```

    ```console
    set PROVISIONING_IDSCOPE=0ne00000A0A
    ```

3. 在 Python 命令提示符中，为上一节从单个注册中复制的注册 ID 和对称密钥添加环境变量。 

    以下命令是展示命令语法的示例。 确保使用正确的值。

    ```console
    set PROVISIONING_REGISTRATION_ID=symm-key-python-device-008
    ```

    ```console
    set PROVISIONING_SYMMETRIC_KEY=sbDDeEzRuEuGKag+kQKV+T1QGakRtHpsERLP0yPjwR93TrpEgEh/Y07CXstfha6dhIPWvdD1nRxK5T0KGKA+nQ==
    ```

4. 在 provision_symmetric_key.py 中运行 python 示例代码。

    ```console
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    ```

5. 预期输出应与以下内容类似，其中显示根据单个注册设置设备被分配到的已链接的 IoT 中心。 一些示例风速遥测消息也会作为测试发送到中心：

    ```output
    D:\azure-iot-sdk-python\azure-iot-device\samples\async-hub-scenarios>python provision_symmetric_key.py
    RegistrationStage(RequestAndResponseOperation): Op will transition into polling after interval 2.  Setting timer.
    The complete registration result is
    python-device-008
    docs-test-iot-hub.azure-devices.net
    initialAssignment
    null
    Will send telemetry from the provisioned device
    sending message #8
    sending message #9
    sending message #3
    sending message #10
    sending message #4
    sending message #2
    sending message #6
    sending message #7
    sending message #1
    sending message #5
    done sending message #8
    done sending message #9
    done sending message #3
    done sending message #10
    done sending message #4
    done sending message #2
    done sending message #6
    done sending message #7
    done sending message #1
    done sending message #5
    ```
    
6. 在 Azure 门户中，导航到已链接到预配服务的 IoT 中心，并打开“IoT 设备”边栏选项卡。 成功将对称密钥设备预配到中心后，设备 ID 的“状态”会显示为“已启用”。 如果在运行设备示例代码前已打开边栏选项卡，则可能需要按顶部的“刷新”按钮。 

    ![设备注册到 IoT 中心](./media/quick-create-device-symm-key-python/hub-registration-python.png) 

> [!NOTE]
> 如果从设备的注册项中的默认值更改了“初始设备孪生状态”，则它会从中心拉取所需的孪生状态，并执行相应的操作。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。
>


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请按以下步骤删除本快速入门中创建的所有资源。

1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择设备预配服务。 打开服务的“管理注册”，然后选择“个人注册”选项卡 。选中在本快速入门中注册的设备的“注册 ID”旁边的复选框，然后按窗格顶部的“删除”按钮。 
1. 在 Azure 门户的左侧菜单中选择“所有资源”，然后选择 IoT 中心。 打开中心的“IoT 设备”，选中在本快速入门中注册的设备的“设备 ID”旁边的复选框，然后按窗格顶部的“删除”按钮。

## <a name="next-steps"></a>后续步骤

在此快速入门中，你使用 IoT 中心设备预配服务将基于 Windows 的对称密钥设备预配到了 IoT 中心。 若要了解如何使用 Python 预配 X.509 证书设备，请继续阅读下面的 X.509 设备快速入门。 

> [!div class="nextstepaction"]
> [Azure 快速入门 - 使用 DPS 和 Python 预配 X.509 设备](quick-create-simulated-device-x509-python.md)
