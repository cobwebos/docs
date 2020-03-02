---
title: 使用 Python 将 X.509 设备注册到 Azure 设备预配服务
description: 本快速入门使用组注册。 本快速入门将使用 Python 将 X.509 设备注册到 Azure IoT 中心设备预配服务 (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: 6f4457397f358d893a5735cc7401394adda8d62b
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605501"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-python"></a>快速入门：使用 Python 将 X.509 设备注册到设备预配服务

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

在本快速入门中，你将使用 Python 以编程方式创建使用中间或根 CA X.509 证书的注册组。 注册组可以控制对设备的预配服务的访问，此类设备在其证书链中共享常用签名证书。 该注册组是使用 Python 预配服务 SDK 和一个示例 Python 应用程序创建的。

## <a name="prerequisites"></a>必备条件

- 完成[使用 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)。
- 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Python 2.x 或 3.x](https://www.python.org/downloads/)。 将 Python 添加到特定于平台的环境变量。 本快速入门将在下面安装 [Python 预配服务 SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client)。
- [Pip](https://pip.pypa.io/en/stable/installing/)（如果 Python 分发版中未附带）。
- [Git](https://git-scm.com/download/)。

> [!IMPORTANT]
> 本文仅适用于已弃用的 V1 Python SDK。 V2 中尚不提供用于 IoT 中心设备预配服务的设备和服务客户端。 该团队目前正在努力使 V2 具有功能奇偶一致性。

## <a name="prepare-test-certificates"></a>准备测试证书

对于本快速入门，必须具有一个包含中间或根 CA X.509 证书的公共部分的 .pem 或.cer 文件。 此证书必须上传到预配服务，并由该服务进行验证。

若要详细了解如何将基于 X.509 证书的公钥基础结构 (PKI) 与 Azure IoT 中心和设备预配服务配合使用，请参阅 [X.509 CA 证书安全概述](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview)。

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 包含的测试工具可以帮助你创建 X.509 证书链、从该链上传根证书或中间证书，以及通过服务执行所有权证明操作，对证书进行验证。 根据设计，使用 SDK 工具创建的证书只能用于**开发测试**。 这些证书**不得在生产环境中使用**。 它们包含硬编码的密码（“1234”），在 30 天后过期。 若要了解如何获取适用于生产用途的证书，请参阅 Azure IoT 中心文档中的[如何获取 X.509 CA 证书](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate)。

若要使用此测试工具来生成证书，请执行以下步骤：

1. 找到[最新版](https://github.com/Azure/azure-iot-sdk-c/releases/latest) Azure IoT C SDK 的标记名称。

2. 打开命令提示符或 Git Bash shell，并切换到计算机上的某个工作文件夹。 运行以下命令，克隆最新版 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 使用在上一步找到的标记作为 `-b` 参数的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    应该预料到此操作需要几分钟才能完成。

   测试工具位于你克隆的存储库的 *azure-iot-sdk-c/tools/CACertificates* 中。

3. 根据[管理示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步骤进行操作。 

## <a name="modify-the-python-sample-code"></a>修改 Python 示例代码

此部分演示如何向示例代码添加 X.509 设备的预配详细信息。 

1. 使用文本编辑器，新建一个 **EnrollmentGroup.py** 文件。

1. 在 **EnrollmentGroup.py** 文件的开头添加以下 `import` 语句和变量： 然后，将 `dpsConnectionString` 替换为你的连接字符串，该字符串位于 **Azure 门户**的**设备预配服务**的“共享访问策略”  下。 将证书占位符替换为此前在[准备测试证书](quick-enroll-device-x509-python.md#prepare-test-certificates)中创建的证书。 最后，创建唯一的 `registrationid`，确保其只包含小写字母数字和连字符。  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""

    GROUP_ID = "{registrationid}"
    ```

1. 添加以下函数和函数调用，以便执行组注册创建操作：
   
    ```python
    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

1. 保存并关闭 **EnrollmentGroup.py** 文件。
 

## <a name="run-the-sample-group-enrollment"></a>运行示例组注册

Azure IoT 设备预配服务支持两类注册：

- [注册组](concepts-service.md#enrollment-group)：用于注册多个相关设备。
- [单个注册](concepts-service.md#individual-enrollment)：用于注册单个设备。

使用 [Python 预配服务 SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client) 创建个人注册是正在进行的一项工作。 若要了解详细信息，请参阅[使用 X.509 证书控制设备对预配服务的访问](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)。

1. 打开命令提示符，并运行以下命令来安装 [azure-iot-provisioning-device-client](https://pypi.org/project/azure-iot-provisioning-device-client)。

    ```cmd/sh
    pip install azure-iothub-provisioningserviceclient    
    ```

2. 在命令提示符下运行此脚本。

    ```cmd/sh
    python EnrollmentGroup.py
    ```

3. 观察成功注册后的输出。

4. 在 Azure 门户中导航到预配服务。 单击“管理注册”。  请注意，X.509 设备组显示在“注册组”选项卡下，带有此前创建的名称 `registrationid`。  

    ![验证是否已在门户中成功注册 X.509](./media/quick-enroll-device-x509-python/1.png)  


## <a name="clean-up-resources"></a>清理资源
如果打算学习 Java 服务示例，请勿清理本快速入门中创建的资源。 如果不打算继续学习，请按以下步骤删除本快速入门中创建的所有资源。

1. 关闭计算机上的 Java 示例输出窗口。
1. 关闭计算机上的“X509 证书生成器”  窗口。
1. 在 Azure 门户中导航到设备预配服务，选择“管理注册”，然后选择“注册组”选项卡   。选中通过本快速入门注册的 X.509 设备的“组名称”旁边的复选框，然后按窗格顶部的“删除”按钮   。    


## <a name="next-steps"></a>后续步骤
在本快速入门中，你将模拟的 X.509 设备组注册到了设备预配服务。 若要深入了解设备预配，请继续学习本教程有关如何在 Azure 门户中进行设备预配服务设置的内容。 

> [!div class="nextstepaction"]
> [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)
