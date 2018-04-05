---
title: 使用 Java 将模拟的 X.509 设备预配到 Azure IoT 中心 | Microsoft Docs
description: Azure 快速入门 - 使用适用于 IoT 中心设备预配服务的 Java 设备 SDK 创建和预配模拟的 X.509 设备
services: iot-dps
keywords: ''
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: f9eb572897854cfb2927b0282d25d5c272abd0e8
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>使用适用于 IoT 中心设备预配服务的 Java 设备 SDK 创建和预配模拟的 X.509 设备
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

以下步骤演示了如何在运行 Windows OS 的开发计算机上模拟 X.509 设备，以及如何使用代码示例通过设备预配服务和 IoT 中心连接该模拟设备。 

在继续操作之前，请确保完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

[!INCLUDE [IoT DPS basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>准备环境 

1. 确保已在计算机上安装 [Java SE 开发工具包 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

1. 下载并安装 [Maven](https://maven.apache.org/install.html)。

1. 确保在计算机上安装 `git` 并将其添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。 

1. 打开命令提示符。 为设备模拟代码示例克隆 GitHub 存储库：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
1. 导航到 azure-iot-sdk-java 根目录，并生成项目以下载全部所需的包。
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
1. 导航到证书生成器项目，然后生成该项目。 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. 导航到目标文件夹，然后执行创建的 jar 文件。

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. 根据设置以下述方式之一创建注册信息：

    - **单个注册**：

        1. 对于“是否需要输入公用名称”，请输入 **N**。 将 `Client Cert` 的输出（从 *-----BEGIN CERTIFICATE-----* 到 *-----END CERTIFICATE-----*）复制到剪贴板。

            ![单个证书的生成器](./media/java-quick-create-simulated-device-x509/individual.png)

        1. 在 Windows 计算机上创建名为 **_X509individual.pem_** 的文件，在所选编辑器中将其打开，然后将剪贴板内容复制到该文件中。 保存文件。

        1. 对于“是否需要输入验证码”，请输入 **N**，并让程序输出保持打开状态，以便在快速入门教程的后面部分进行引用。 记下“客户端证书”和“客户端证书私钥”的值。
    
    - **注册组**：

        1. 对于“是否需要输入公用名称”，请输入 **N**。 将 `Root Cert` 的输出（从 *-----BEGIN CERTIFICATE-----* 到 *-----END CERTIFICATE-----*）复制到剪贴板。

            ![组证书生成器](./media/java-quick-create-simulated-device-x509/group.png)

        1. 在 Windows 计算机上创建名为 **_X509group.pem_** 的文件，在所选编辑器中将其打开，然后将剪贴板内容复制到该文件中。 保存文件。

        1. 对于“是否需要输入验证码”，请输入 **Y**，并让程序保持打开状态，以便在快速入门教程的后面部分使用。 记下“客户端证书”、“客户端证书私钥”、“签名者证书”和“根证书”的值。

        > [!NOTE]
        > 上面的 `Root Cert` 仅适用于在控制台输出中创建的证书，不能用于签署其他客户端证书。 如果需要更可靠的测试证书集，请参阅 [Managing CA Certificates Sample](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)（管理 CA 证书示例）。
        >

## <a name="create-a-device-enrollment-entry"></a>创建设备注册项

1. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开预配服务。

1. 根据设置以下述方式之一输入注册信息：

    - **单个注册**： 

        1. 在“设备预配服务摘要”边栏选项卡上，选择“管理注册”。 选择“单个注册”选项卡，单击顶部的“添加”按钮。 

        1. 在“添加注册列表项”下，输入以下信息：
            - 选择“X.509”作为标识证明机制。
            - 使用“文件资源管理器”小组件，在“证书 .pem 或 .cer 文件”下选择在前述步骤中创建的证书文件 **_X509individual.pem_**。
            - （可选）可以提供以下信息：
                - 选择与预配服务链接的 IoT 中心。
                - 输入唯一设备 ID。 为设备命名时，请确保避免使用敏感数据。 
                - 使用设备所需的初始配置更新“初始设备孪生状态”。
            - 完成后，单击“保存”按钮。 

        ![在门户边栏选项卡中输入 X.509 设备注册信息](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       成功注册以后，X.509 设备会在“单独注册”选项卡的“注册 ID”列下显示为 **microsoftriotcore**。 

    - **注册组**： 

        1. 在设备预配服务摘要边栏选项卡上选择“证书”，然后单击顶部的“添加”按钮。

        1. 在“添加证书”下输入以下信息：
            - 输入唯一的证书名称。
            - 选择以前创建的 **_X509group.pem_** 文件。
            - 完成后，单击“保存”按钮。

        ![添加证书](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. 选择新建的证书：
            - 单击“生成验证码”。 复制生成的代码。
            - 输入验证码，或者通过右键单击在运行的 _provisioning-x509-cert-generator_ 窗口中进行粘贴。  按 **Enter**。
            - 将 `Verification Cert` 的输出（从 *-----BEGIN CERTIFICATE-----* 到 *-----END CERTIFICATE-----*）复制到剪贴板。
            
                ![验证生成器](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - 在 Windows 计算机上创建名为 **_X509validation.pem_** 的文件，在所选编辑器中将其打开，然后将剪贴板内容复制到该文件中。 保存文件。
            - 在 Azure 门户中选择 **_X509validation.pem_** 文件。 单击“验证”。

            ![验证证书](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. 选择“管理注册”。 选择“注册组”选项卡，单击顶部的“添加”按钮。
            - 输入唯一的组名称。
            - 选择以前创建的唯一证书名称
            - （可选）可以提供以下信息：
                - 选择与预配服务链接的 IoT 中心。
                - 使用设备所需的初始配置更新“初始设备孪生状态”。

        ![在门户边栏选项卡中输入 X.509 组注册信息](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        成功注册以后，X.509 设备组会显示在“注册组”选项卡的“组名称”栏下。


## <a name="simulate-the-device"></a>模拟设备

1. 在设备预配服务摘要边栏选项卡上选择“概览”，记下“ID 范围”和“预配服务全局终结点”。

    ![服务信息](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. 打开命令提示符。 导航到示例项目文件夹。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. 根据设置以下述方式之一输入注册信息：

    - **单个注册**： 

        1. 编辑 `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`，使之包括“ID 范围”和“预配服务全局终结点”，如前所述。 另请包括“客户端证书”和“客户端证书私钥”，如前所述。

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - 使用以下格式来包括证书和密钥：
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **注册组**： 

        1. 按上述“单个注册”的说明操作。

        1. 将以下代码行添加到 `main` 函数的开头。
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(rootPem);
            ```
    
            - 使用以下格式来包括证书：
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. 生成示例。 导航到目标文件夹，然后执行创建的 jar 文件。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. 在门户中导航到已链接到预配服务的 IoT 中心，然后打开“Device Explorer”边栏选项卡。 将模拟的 X.509 设备成功预配到中心以后，设备 ID 会显示在“Device Explorer”边栏选项卡上，“状态”为“已启用”。 请注意，如果在运行示例设备应用程序之前已打开边栏选项卡，则可能需要单击顶部的“刷新”按钮。 

    ![设备注册到 IoT 中心](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> 如果从设备的注册项中的默认值更改了“初始设备孪生状态”，则它会从中心拉取所需的孪生状态，并执行相应的操作。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。
>


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请通过以下步骤删除通过本快速入门创建的所有资源。

1. 关闭计算机上的设备客户端示例输出窗口。
1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择设备预配服务。 打开服务的“管理注册”边栏选项卡，然后单击“单个注册”选项卡。选择在本快速入门中注册的设备的“注册 ID”，然后单击顶部的“删除”按钮。 
1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心。 打开中心的“IoT 设备”边栏选项卡，选择在本快速入门中注册的设备的“设备 ID”，然后单击顶部的“删除”按钮。


## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何在 Windows 计算机上创建模拟 X.509 设备，以及如何使用门户中的 Azure IoT 中心设备预配服务将其预配到 IoT 中心。 若要了解如何以编程方式注册 X.509 设备，请继续阅读快速入门中关于 X.509 设备的编程注册内容。 

> [!div class="nextstepaction"]
> [Azure 快速入门 - 将 X.509 设备注册到 Azure IoT 中心设备预配服务](quick-enroll-device-x509-java.md)
