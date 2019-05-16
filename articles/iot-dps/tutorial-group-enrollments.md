---
title: 使用 Java 和登记组将模拟的 X.509 设备预配到 Azure IoT 中心 | Microsoft Docs
description: Azure 教程 - 使用适用于 IoT 中心设备预配服务的 Java 设备和服务 SDK 与登记组来创建和预配模拟的 X.509 设备
author: wesmc7777
ms.author: wesmc
ms.date: 01/04/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 8e926c3ff7c3d7abc9467291e9b1de77781f664e
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805047"
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-and-service-sdk-and-group-enrollments-for-iot-hub-device-provisioning-service"></a>使用适用于 IoT 中心设备预配服务的 Java 设备和服务 SDK 与组登记来创建和预配模拟的 X.509 设备

以下步骤说明如何在运行 Windows OS 的开发计算机上模拟 X.509 设备，以及如何使用代码示例通过登记组将模拟设备连接到设备预配服务和 IoT 中心。 

在继续操作之前，请确保完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。


## <a name="prepare-the-environment"></a>准备环境 

1. 确保已在计算机上安装 [Java SE 开发工具包 8](https://aka.ms/azure-jdks)。

1. 下载并安装 [Maven](https://maven.apache.org/install.html)。

1. 确保在计算机上安装 `git` 并将其添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。 

1. 使用以下[证书概述](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)创建测试证书。

    > [!NOTE]
    > 此步骤需要 [OpenSSL](https://www.openssl.org/)，可以通过源代码生成并安装此工具，也可以通过[第三方](https://wiki.openssl.org/index.php/Binaries)（例如[此处](https://sourceforge.net/projects/openssl/)）下载并安装此工具。 如果已创建根证书、中间证书和设备证书，则可以跳过此步骤。
    >

    1. 运行头两个步骤即可创建根证书和中间证书。

    1. 登录到 Azure 门户，单击左侧菜单上的“所有资源”按钮，打开预配服务。

        1. 在设备预配服务摘要边栏选项卡上选择“证书”，然后单击顶部的“添加”按钮。

        1. 在“添加证书”下输入以下信息：
            - 输入唯一的证书名称。
            - 选择创建的 RootCA.pem 文件。
            - 完成后，单击“保存”按钮。

           ![添加证书](./media/tutorial-group-enrollments/add-certificate.png)

        1. 选择新建的证书：
            - 单击“生成验证码”。 复制生成的代码。
            - 运行验证步骤。 输入验证码，或者在运行的 PowerShell 窗口中右键单击进行粘贴。  按 **Enter**。
            - 在 Azure 门户中选择新建的 **_verifyCert4.pem_** 文件。 单击“验证”。

              ![验证证书](./media/tutorial-group-enrollments/validate-certificate.png)

    1. 最后，请运行创建设备证书并清理资源的步骤。

       > [!NOTE]
       > 创建设备证书时，请确保只在设备名称中使用小写字母数字和连字符。
       >


## <a name="create-a-device-enrollment-entry"></a>创建设备注册项

1. 打开命令提示符。 克隆 Java SDK 代码示例的 GitHub 存储库：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. 在下载的源代码中，导航到示例文件夹 **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**。 在所选编辑器中打开文件 **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_**，添加以下详细信息：

    1. 在门户中为预配服务添加 `[Provisioning Connection String]`，如下所示：

        1. 在 [Azure 门户](https://portal.azure.com)中导航到预配服务。

        1. 打开“共享访问策略”，选择具有 *EnrollmentWrite* 权限的策略。

        1. 复制“主密钥连接字符串”。

            ![从门户获取预配连接字符串](./media/tutorial-group-enrollments/provisioning-string.png)  

        1. 在示例代码文件 **_ServiceEnrollmentGroupSample.java_** 中，将 `[Provisioning Connection String]` 替换为“主密钥连接字符串”。

            ```java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    1. 在文本编辑器中打开中间签名证书文件。 使用中间签名证书的值更新 `PUBLIC_KEY_CERTIFICATE_STRING` 值。

        如果是使用 Bash shell 生成的设备证书，则 ./certs/azure-iot-test-only.intermediate.cert.pem 包含中间证书密钥。 如果是使用 PowerShell 生成的证书，则 ./Intermediate1.pem 会是中间证书文件。

        ```java
        private static final String PUBLIC_KEY_CERTIFICATE_STRING =
                "-----BEGIN CERTIFICATE-----\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                "-----END CERTIFICATE-----\n";
        ```

    1. 在 [Azure 门户](https://portal.azure.com)中导航到已链接到预配服务的 IoT 中心。 打开中心的“概览”选项卡，复制“主机名”。 将该“主机名”指定给 *IOTHUB_HOST_NAME* 参数。

        ```java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

    1. 研究示例代码。 此代码用于创建、更新、查询和删除 X.509 设备的组注册。 若要验证是否已在门户中成功注册，请暂时性地注释掉 _ServiceEnrollmentGroupSample.java_ 文件末尾的以下代码行：

        ```java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    1. 保存 _ServiceEnrollmentGroupSample.java_ 文件。

1. 打开命令窗口，导航到文件夹 **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**。

1. 使用以下命令生成示例代码：

    ```cmd\sh
    mvn install -DskipTests
    ```

1. 运行示例，方法是在命令窗口使用以下命令：

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

1. 在成功注册后观察输出窗口。

    ![成功登记](./media/tutorial-group-enrollments/enrollment.png) 

1. 在 Azure 门户中导航到预配服务。 单击“管理注册”。 请注意，X.509 设备组显示在“注册组”选项卡下，带有自动生成的“组名称”。

## <a name="simulate-the-device"></a>模拟设备

1. 在设备预配服务摘要边栏选项卡上选择“概览”，记下“ID 范围”和“预配服务全局终结点”。

    ![服务信息](./media/tutorial-group-enrollments/extract-dps-endpoints.png)

1. 打开命令提示符。 导航到示例项目文件夹。

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. 编辑 `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java`，使之包括前面记下的“ID 范围”和“预配服务全局终结点”。

    ```java
    private static final String idScope = "[Your ID scope here]";
    private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
    private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
    private static final int MAX_TIME_TO_WAIT_FOR_REGISTRATION = 10000; // in milli seconds
    private static final String leafPublicPem = "<Your Public PEM Certificate here>";
    private static final String leafPrivateKey = "<Your Private PEM Key here>";
    ```

1. 使用公共和专用设备证书更新 `leafPublicPem` 和 `leafPrivateKey` 变量。

    如果是使用 PowerShell 生成的设备证书，则文件 mydevice* 包含设备的公钥、私钥和 PFX。

    如果是使用 Bash shell 生成的设备证书，则 ./certs/new-device.cert.pem 包含公钥。 设备的私钥位于 ./private/new-device.key.pem 文件中。

    打开公钥文件并使用该值更新 `leafPublicPem` 变量。 复制从 -----BEGIN PRIVATE KEY----- 到 -----END PRIVATE KEY----- 的文本。

    ```java
    private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

    打开私钥文件并使用该值更新 `leafPrivatePem` 变量。 复制从 -----BEGIN RSA PRIVATE KEY----- 到 -----END RSA PRIVATE KEY----- 的文本。

    ```java
    private static final String leafPrivateKey = "-----BEGIN RSA PRIVATE KEY-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END RSA PRIVATE KEY-----\n";
    ```

1. 就在 `leafPrivateKey` 下方为中间证书添加新变量。 将此新变量命名为 `intermediateKey`。 向它提供中间签名证书的值。

    如果是使用 Bash shell 生成的设备证书，则 ./certs/azure-iot-test-only.intermediate.cert.pem 包含中间证书密钥。 如果是使用 PowerShell 生成的证书，则 ./Intermediate1.pem 会是中间证书文件。

    ```java
    private static final String intermediateKey = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
    ```

1. 在 `main` 函数中，在 `securityProviderX509` 的初始化之前将 `intermediateKey` 添加到 `signerCertificates` 集合。

    ```java
    public static void main(String[] args) throws Exception
    {
        ...

        try
        {
            ProvisioningStatus provisioningStatus = new ProvisioningStatus();

            // Add intermediate certificate as part of the certificate key chain.
            signerCertificates.add(intermediateKey);

            SecurityProvider securityProviderX509 = new SecurityProviderX509Cert(leafPublicPem, leafPrivateKey, signerCertificates);
    ```

1. 保存更改，然后生成示例。 导航到目标文件夹，然后执行创建的 jar 文件。

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

    ![注册成功](./media/tutorial-group-enrollments/registration.png)

1. 在门户中导航到已链接到预配服务的 IoT 中心，然后打开“Device Explorer”边栏选项卡。 将模拟的 X.509 设备成功预配到中心以后，设备 ID 会显示在“Device Explorer”边栏选项卡上，“状态”为“已启用”。 请注意，如果在运行示例设备应用程序之前已打开边栏选项卡，则可能需要单击顶部的“刷新”按钮。 

    ![设备注册到 IoT 中心](./media/tutorial-group-enrollments/hub-registration.png) 


## <a name="clean-up-resources"></a>清理资源

如果打算继续使用和探索设备客户端示例，请勿清理在本快速入门中创建的资源。 如果不打算继续学习，请通过以下步骤删除通过本快速入门创建的所有资源。

1. 关闭计算机上的设备客户端示例输出窗口。
1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择设备预配服务。 打开服务的“管理注册”边栏选项卡，然后单击“单个注册”选项卡。选择在本快速入门中注册的设备的“注册 ID”，然后单击顶部的“删除”按钮。 
1. 在 Azure 门户的左侧菜单中单击“所有资源”，然后选择 IoT 中心。 打开中心的“IoT 设备”边栏选项卡，选择在本快速入门中注册的设备的“设备 ID”，然后单击顶部的“删除”按钮。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何在 Windows 计算机上创建模拟 X.509 设备，以及如何使用 Azure IoT 中心设备预配服务和登记组将其预配到 IoT 中心。 若要了解有关 X.509 设备的详细信息，请继续阅读设备概念。 

> [!div class="nextstepaction"]
> [IoT 中心设备预配服务设备概念](concepts-device.md)
