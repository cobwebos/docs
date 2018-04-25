---
title: 使用 Java 将模拟的 X.509 设备预配到 Azure IoT 中心 | Microsoft Docs
description: Azure 快速入门 - 使用适用于 IoT 中心设备预配服务的 Java 设备 SDK 创建和预配模拟的 X.509 设备
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-masebo;bryanla
ms.date: 04/09/2018
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 72460e19d202b79369844db6fea24f2914c8bbbe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>使用适用于 IoT 中心设备预配服务的 Java 设备 SDK 创建和预配模拟的 X.509 设备
[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

以下步骤演示了如何在运行 Windows OS 的开发计算机上模拟 X.509 设备，以及如何使用代码示例通过设备预配服务和 IoT 中心连接该模拟设备。 

如果不熟悉自动预配过程，还务必查看[自动预配概念](concepts-auto-provisioning.md)。 另外，在继续操作之前，请确保已完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。 

## <a name="prepare-the-environment"></a>准备环境 

1. 确保已在计算机上安装 [Java SE 开发工具包 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。

2. 下载并安装 [Maven](https://maven.apache.org/install.html)。

3. 确保在计算机上安装 Git 并将其添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。 

4. 打开命令提示符。 为设备模拟代码示例克隆 GitHub 存储库：
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. 导航到 `azure-iot-sdk-`java` 根目录，并生成项目以下载全部所需的包。
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. 导航到证书生成器项目，然后生成该项目。 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>创建自签名的 X.509 设备证书和单个注册项

1. 使用前面步骤中的命令提示符，导航到 `target` 文件夹，然后执行在上一步骤中创建的 jar 文件。

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. 对于“是否需要输入公用名称”，请输入 **N**。 将 `Client Cert` 的输出（从 *-----BEGIN CERTIFICATE-----* 到 *-----END CERTIFICATE-----*）复制到剪贴板。

   ![单个证书的生成器](./media/java-quick-create-simulated-device-x509/individual.png)

3. 在 Windows 计算机上创建名为 **_X509individual.pem_** 的文件，在所选编辑器中将其打开，然后将剪贴板内容复制到该文件中。 保存该文件并关闭编辑器。

4. 在命令提示符中，对于“是否需要输入验证码”，请输入 **N**，并让程序输出保持打开状态，以便在快速入门教程的后面部分进行引用。 稍后，你将复制 `Client Cert` 和 `Client Cert Private Key` 值，以在下一部分中使用。

5. 登录到 [Azure 门户](https://portal.azure.com)，单击左侧菜单上的“所有资源”按钮，打开设备预配服务实例。

6. 在“设备预配服务摘要”边栏选项卡上，选择“管理注册”。 选择“单个注册”选项卡，单击顶部的“添加”按钮。 

7. 在“添加注册”面板下，输入以下信息：
    - 选择“X.509”作为标识证明机制。
    - 在“主要证书 .pem 或 .cer 文件”下，单击“选择文件”选择在前述步骤中创建的证书文件 **X509individual.pem**。  
    - （可选）可以提供以下信息：
      - 选择与预配服务链接的 IoT 中心。
      - 输入唯一设备 ID。 为设备命名时，请确保避免使用敏感数据。 
      - 使用设备所需的初始配置更新“初始设备孪生状态”。
   - 完成后，单击“保存”按钮。 

    [![在门户中为 X.509 证明添加单个注册](./media/quick-create-simulated-device-x509-csharp/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     成功注册以后，X.509 设备会在“单个注册”选项卡的“注册 ID”列下显示为 **microsoftriotcore**。 



## <a name="simulate-the-device"></a>模拟设备

1. 在设备预配服务摘要边栏选项卡上选择“概览”，记下“ID 范围”和“预配服务全局终结点”。

    ![服务信息](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. 打开命令提示符。 导航到 Java SDK 存储库的示例项目文件夹。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. 在代码中输入预配服务和 X.509 标识信息。 这在自动预配期间在注册设备之前用于证明模拟设备：

   - 编辑文件 `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`，使之包括前面提到的“ID 范围”和“预配服务全局终结点”。 另请包括上一部分中提到的“客户端证书”和“客户端证书私钥”。

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - 复制/粘贴证书和私钥时，请使用以下格式：
        
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

4. 生成示例。 导航到 `target` 文件夹，并执行所创建的 jar 文件。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. 在 Azure 门户中，导航到已链接到预配服务的 IoT 中心，然后打开“Device Explorer”边栏选项卡。 将模拟的 X.509 设备成功预配到中心后，设备 ID 会显示在“Device Explorer”边栏选项卡上，“状态”为“已启用”。  如果在运行示例设备应用程序之前已打开边栏选项卡，则可能需要单击顶部的“刷新”按钮。 

    ![设备注册到 IoT 中心](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> 如果从设备的注册项中的默认值更改了“初始设备孪生状态”，则它会从中心拉取所需的孪生状态，并执行相应的操作。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。
>


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请通过以下步骤删除通过本快速入门创建的所有资源。

1. 关闭计算机上的设备客户端示例输出窗口。
2. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择设备预配服务。 打开服务的“管理注册”边栏选项卡，然后单击“单个注册”选项卡。选择在本快速入门中注册的设备的“注册 ID”，然后单击顶部的“删除”按钮。 
3. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心。 打开中心的“IoT 设备”边栏选项卡，选择在本快速入门中注册的设备的“设备 ID”，然后单击顶部的“删除”按钮。


## <a name="next-steps"></a>后续步骤

在此快速入门中，你已在 Windows 计算机上创建了一台模拟的 X.509 设备。 你在 Azure IoT 中心设备预配服务中配置了其注册，然后将设备自动预配到了 IoT 中心。 若要了解如何以编程方式注册 X.509 设备，请继续阅读快速入门中关于 X.509 设备的编程注册内容。 

> [!div class="nextstepaction"]
> [Azure 快速入门 - 将 X.509 设备注册到 Azure IoT 中心设备预配服务](quick-enroll-device-x509-java.md)
