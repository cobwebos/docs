---
title: 使用 Python 将 TPM 设备注册到 Azure 设备预配服务
description: 快速入门 - 使用 Python 预配服务 SDK 将 TPM 设备注册到 Azure IoT 中心设备预配服务 (DPS)。 本快速入门使用单独注册。
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 60ce27ddc533b6c4066cea771f7a24570ff3c04c
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604894"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>快速入门：使用 Python 预配服务 SDK 将 TPM 设备注册到 IoT 中心设备预配服务

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

在本快速入门中，你将借助示例 Python 应用程序，使用 Python 预配服务 SDK 以编程方式在 Azure IoT 中心设备预配服务中为 TPM 设备创建单个注册。

## <a name="prerequisites"></a>必备条件

- 完成[使用 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)。
- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Python 2.x 或 3.x](https://www.python.org/downloads/)。 本快速入门将在下面安装 [Python 预配服务 SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client)。
- [Pip](https://pip.pypa.io/en/stable/installing/)（如果 Python 分发版中未附带）。
- 认可密钥。 使用[创建和预配模拟设备](quick-create-simulated-device.md)中的步骤，或使用 SDK 随附的认可密钥，如下所述。

> [!IMPORTANT]
> 本文仅适用于已弃用的 V1 Python SDK。 V2 中尚不提供用于 IoT 中心设备预配服务的设备和服务客户端。 该团队目前正在努力使 V2 具有功能奇偶一致性。

<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>准备环境 

1. 下载并安装 [Python 2.x 或 3.x](https://www.python.org/downloads/)。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 

1. 对于 [Python 预配服务 SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client)，请选择以下选项之一：

    - 生成并编译 **Azure IoT Python SDK**。 按照[这些说明](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md)生成 Python 包。 如果使用 Windows OS，则另请安装 [Visual C++ 可再发行组件包](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)，以便使用 Python 中的本机 DLL。

    - [安装或升级 *pip*（Python 包管理系统）](https://pip.pypa.io/en/stable/installing/)，然后通过以下命令安装该包：

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. 需要适用于设备的认可密钥。 如果已按照[创建和预配模拟的设备](quick-create-simulated-device.md)快速入门教程创建模拟的 TPM 设备，请使用为该设备创建的密钥。 否则，可以使用 SDK 随附的以下认可密钥：

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>修改 Python 示例代码

此部分演示如何向示例代码添加 TPM 设备的预配详细信息。 

1. 使用文本编辑器，新建一个 **TpmEnrollment.py** 文件。

1. 在 **TpmEnrollment.py** 文件的开头添加以下 `import` 语句和变量： 然后，将 `dpsConnectionString` 替换为你的连接字符串，该字符串位于 **Azure 门户**的**设备预配服务**的“共享访问策略”  下。 将 `endorsementKey` 替换为此前在[准备环境](quick-enroll-device-tpm-python.md#prepareenvironment)中记下的值。 最后，创建唯一的 `registrationid`，确保其只包含小写字母数字和连字符。  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. 添加以下函数和函数调用，以便执行组注册创建操作：
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. 保存并关闭 **TpmEnrollment.py** 文件。
 

## <a name="run-the-sample-tpm-enrollment"></a>运行示例 TPM 注册

1. 打开命令提示符并运行此脚本。

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. 观察成功注册后的输出。

1. 在 Azure 门户中导航到预配服务。 选择“管理注册”。  请注意，TPM 设备显示在“单个注册”选项卡下，  使用的名称 `registrationid` 是以前创建的。 

    ![验证是否已在门户中成功完成 TPM 注册](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>清理资源
如果打算学习 Java 服务示例，请勿清理本快速入门中创建的资源。 如果不打算继续学习，请按以下步骤删除本快速入门中创建的所有资源。

1. 关闭计算机上的 Python 示例输出窗口。
1. 如果已创建模拟的 TPM 设备，请关闭 TPM 模拟器窗口。
1. 在 Azure 门户中导航到你的设备预配服务，选择“管理注册”，然后选择“单个注册”选项卡。   选中通过本快速入门创建的注册项的“注册 ID”旁边的复选框，然后按窗格顶部的“删除”按钮   。


## <a name="next-steps"></a>后续步骤
本快速入门介绍了如何以编程方式为 TPM 设备创建单个注册项，以及如何选择性地在计算机上创建 TPM 模拟设备，并使用 Azure IoT 中心设备预配服务将其预配到 IoT 中心。 若要深入了解设备预配，请继续学习本教程有关如何在 Azure 门户中进行设备预配服务设置的内容。

> [!div class="nextstepaction"]
> [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)
