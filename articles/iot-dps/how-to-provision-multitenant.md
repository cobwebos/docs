---
title: 如何在 Azure IoT 中心设备预配服务中为多租户预配设备
description: 如何通过设备预配服务（DPS）实例为多租户设置设备
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5703db90307f679ff4728386dc24647437f9f9ba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974949"
---
# <a name="how-to-provision-for-multitenancy"></a>如何预配多租户 

由预配服务定义的分配策略支持各种不同的分配方案。 两个常见的方案是：

* 地理位置/GeoLatency：当设备在两位置之间移动时，通过将设备预配到距离每个位置最近的 IoT 中心来改善网络延迟。 在此方案中，为注册选择跨越区域的一组 IoT 中心。 为这些注册选择“最低延迟”分配策略。 此策略会使设备预配服务评估设备延迟，并从一组 IoT 中心确定最接近的 IoT 中心。 

* 多租户：IoT 解决方案中使用的设备可能需要被分配到一个特定 IoT 中心或一组 IoT 中心。 解决方案可能要求特定租户的所有设备与一组特定的 IoT 中心进行通信。 在某些情况下，租户可能拥有 IoT 中心并要求设备被分配到其 IoT 中心。

通常会将这两种方案结合使用。 例如，多租户 IoT 解决方案通常使用跨区域分散的一组 IoT 中心来分配租户设备。 这些租户设备可被分配到基于地理位置具有最低延迟的组中的 IoT 中心。

本文使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 中的模拟设备示例来演示如何在跨区域的多租户方案中预配设备。 你将在本文中执行以下步骤：

* 使用 Azure CLI 创建两个区域 IoT 中心（美国西部和美国东部）
* 创建多租户注册
* 使用 Azure CLI 创建两个区域 Linux VM，以充当同一区域中的设备（美国西部和美国东部）
* 在这两个 Linux VM 上为 Azure IoT C SDK 设置开发环境
* 模拟设备，以确保为最近区域中同一租户预配设备。


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>必备组件

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)快速入门。


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


## <a name="create-two-regional-iot-hubs"></a>创建两个区域 IoT 中心

在这一部分，你将使用 Azure Cloud Shell 在美国西部和美国东部区域为租户创建两个新的区域 IoT 中心。


1. 在 Azure Cloud Shell 中，使用 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

    以下示例在“eastus”区域创建名为“contoso-us-resource-group”的资源组。 建议对本文中创建的所有资源使用该组。 这将便于在完成学习后更为轻松地清理创建的资源。

    ```azurecli-interactive 
    az group create --name contoso-us-resource-group --location eastus
    ```

2. 在 Azure Cloud Shell 中，使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令在“eastus”区域中创建 IoT 中心。 IoT 中心将被添加到 contoso-us-resource-group。

    以下示例在“eastus”位置创建名为“contoso-east-hub”的 IoT 中心。 你必须使用自己的唯一中心名称来替代 contoso-east-hub。

    ```azurecli-interactive 
    az iot hub create --name contoso-east-hub --resource-group contoso-us-resource-group --location eastus --sku S1
    ```
    
    此命令可能需要花费几分钟时间完成。

3. 在 Azure Cloud Shell 中，使用 [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) 命令在“westus”区域中创建 IoT 中心。 此 IoT 中心也将被添加到 contoso-us-resource-group。

    以下示例在“westus”位置创建名为“contoso-west-hub”的 IoT 中心。 你必须使用自己的唯一中心名称来替代 contoso-west-hub。

    ```azurecli-interactive 
    az iot hub create --name contoso-west-hub --resource-group contoso-us-resource-group --location westus --sku S1
    ```

    此命令可能需要花费几分钟时间完成。



## <a name="create-the-multitenant-enrollment"></a>创建多租户注册

在这一部分，你将为租户设备创建新的注册组。  

为简单起见，本文将在注册中使用[对称密钥证明](concepts-symmetric-key-attestation.md)。 对于更安全的解决方案，请考虑使用具有信任链的 [X.509 证书证明](concepts-security.md#x509-certificates)。

1. 登录到 [Azure 门户](https://portal.azure.com)，并打开你的设备预配服务实例。

2. 选择“管理注册”选项卡，然后单击页面顶部的“添加注册组”按钮。 

3. 在“添加注册组”中输入以下信息，然后单击“保存”按钮。

    组名称：输入 contoso-us-devices。

    证明类型：选择“对称密钥”。

    自动生成密钥：此复选框应已处于选中状态。

    选择要如何将设备分配到中心：选择“最低延迟”。

    ![为对称密钥证明添加多租户注册组](./media/how-to-provision-multitenant/create-multitenant-enrollment.png)


4. 在“添加注册组”上，单击“链接新的 IoT 中心”，以链接这两个区域中心。

    订阅：如果你有多个订阅，请选择创建区域 IoT 中心的订阅。

    IoT 中心：选择你创建的区域中心之一。

    访问策略：选择“iothubowner”。

    ![使用预配服务链接区域 IoT 中心](./media/how-to-provision-multitenant/link-regional-hubs.png)


5. 在链接这两个区域 IoT 中心后，必须为注册组选择它们，并单击“保存”，以为注册创建区域 IoT 中心组。

    ![为注册创建区域中心组](./media/how-to-provision-multitenant/enrollment-regional-hub-group.png)


6. 保存注册后，重新打开它，并记录“主键”。 必须先保存注册，才能生成密钥。 此密钥稍后将会在为这两个模拟设备生成唯一设备密钥时使用。


## <a name="create-regional-linux-vms"></a>创建区域 Linux VM

在这一部分，你将创建两个区域 Linux 虚拟机 (VM)。 这些 VM 将从每个区域运行设备模拟示例，以演示这两个区域中针对租户设备的设备预配。

为了更易清理资源，这些 VM 将被添加到包含创建的 IoT 中心的同一资源组 contoso-us-resource-group。 但是，VM 将在不同区域中运行（美国西部和美国东部）。

1. 在 Azure Cloud Shell 中，在命令中更改以下参数后，执行该命令以创建美国东部区域 VM：

    --name：为美国东部区域设备 VM 输入一个唯一名称。 

    --admin-username：使用你自己的管理员用户名称。

    --admin-password：使用你自己的管理员密码。

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    此命令需要花费几分钟时间完成。 完成该命令后，请记下美国东部区域 VM 的 publicIpAddress 值。

1. 在 Azure Cloud Shell 中，在命令中更改以下参数后，执行该命令以创建美国西部区域 VM：

    --name：为美国西部区域设备 VM 输入一个唯一名称。 

    --admin-username：使用你自己的管理员用户名称。

    --admin-password：使用你自己的管理员密码。

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest \
    --location westus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    ```

    此命令需要花费几分钟时间完成。 完成该命令后，请记下美国西部区域 VM 的 publicIpAddress 值。

1. 打开两个命令行 shell。 使用 SSH 连接到每个 shell 中的其中一个区域 VM。 

    将管理员用户名和为 VM 记录的公共 IP 地址作为参数传递到 SSH。 在出现提示时输入管理员密码。

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```



## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>准备 Azure IoT C SDK 开发环境

在这一部分，你将克隆每个 VM 上的 Azure IoT C SDK。 SDK 包含将从每个区域模拟租户的设备预配的示例。


1. 对于每个 VM，使用以下命令安装 Cmake 、g++ 、gcc 和 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)：

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```


1. 在这两个 VM 上克隆 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)。

    ```bash
    cd ~/
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    应该预料到此操作需要几分钟才能完成。

1. 对于这两个 VM，在存储库内创建一个新的 cmake 文件夹，并更改为该文件夹。

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

1. 对于这两个 VM，运行以下命令，生成特定于你的开发客户端平台的 SDK 版本。 

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

    生成成功后，最后的几个输出行如下所示：

    ```bash
    -- IoT Client SDK Version = 1.2.9
    -- Provisioning client ON
    -- Provisioning SDK Version = 1.2.9
    -- target architecture: x86_64
    -- Checking for module 'libcurl'
    --   Found libcurl, version 7.58.0
    -- Found CURL: curl
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- target architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/cmake
    ```    


## <a name="derive-unique-device-keys"></a>派生唯一设备密钥

在组注册中使用对称密钥证明时，不会直接使用注册组密钥。 相反，你为每个设备创建唯一派生的密钥，并在[使用对称密钥进行组注册](concepts-symmetric-key-attestation.md#group-enrollments)中提及。

若要生成设备密钥，请使用组主键计算设备的唯一注册 ID 的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)，并将结果转换为 Base64 格式。

不要在设备代码中包含你的组主键。

使用 Bash shell 示例为使用 openssl 的每个设备创建派生的设备密钥。

- 将密钥值替换为之前注册时所记录的主键。

- 针对每个设备，将 REG_ID 的值替换为你自己的唯一注册 ID。 使用小写字母数字和短划线（“-”）字符定义这两个 ID。

contoso-simdevice-east 的示例设备密钥生成：

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-east

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
```

contoso-simdevice-west 的示例设备密钥生成：

```bash
KEY=rLuyBPpIJ+hOre2SFIP9Ajvdty3j0EwSP/WvTVH9eZAw5HpDuEmf13nziHy5RRXmuTy84FCLpOnhhBPASSbHYg==
REG_ID=contoso-simdevice-west

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=
```


租户设备将分别使用其派生的设备密钥和唯一注册 ID，在预配到租户 IoT 中心期间在注册组中执行对称密钥证明。




## <a name="simulate-the-devices-from-each-region"></a>模拟来自每个区域的设备


在这一部分，你将为这两个区域 VM 更新 Azure IoT C SDK 中的预配示例。 

示例代码模拟将预配请求发送到你的设备预配服务实例的设备启动序列。 启动序列将会使设备被识别，并基于延迟被分配到最邻近的 IoT 中心。

1. 在 Azure 门户中，选择设备预配服务的“概述”选项卡，记下“ID 范围”的值。

    ![从门户边栏选项卡中提取设备预配服务终结点信息](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

1. 打开 ~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c，在这两个 VM 上进行编辑。

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

1. 找到 `id_scope` 常量，将值替换为前面复制的“ID 范围”值。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

1. 在同一文件中找到 `main()` 函数的定义。 请确保将 `hsm_type` 变量设置为 `SECURE_DEVICE_TYPE_SYMMETRIC_KEY`（如下所示），以匹配注册组证明方法。 

    将你对文件的更改保存在这两个 VM 上。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

1. 在两个 VM 上，在 **prov\_dev\_client\_sample.c** 中找到已注释掉的对 `prov_dev_set_symmetric_key_info()` 的调用。

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    取消注释这些函数调用，并将占位符值（包括尖括号）替换为每个设备的唯一注册 ID 以及派生设备密钥。 下面显示的密钥仅用作示例。 请使用你之前生成的密钥。

    美国东部：
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    美国西部：
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

    保存文件。

1. 在这两个 VM 上，导航到以下所示的示例文件夹，并生成示例。

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

1. 成功生成后，在这两个 VM 上运行 prov\_dev\_client\_sample.exe，以模拟来自每个区域的租户设备。 请注意，每个设备将被分配到最邻近模拟设备区域的租户 IoT 中心。

    运行模拟：
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    来自美国东部 VM 的示例输出：

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    来自美国西部 VM 的示例输出：
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```



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

- 若要了解更多重新设置，请参阅[IoT 中心设备重新设置概念](concepts-device-reprovision.md) 
- 若要了解更多取消设置，请参阅[如何取消预配以前自动预配的设备](how-to-unprovision-devices.md) 











