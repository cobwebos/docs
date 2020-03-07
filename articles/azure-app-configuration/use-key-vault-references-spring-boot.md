---
title: 教程：在 Java Spring Boot 应用中使用 Azure 应用程序配置 Key Vault 引用 | Microsoft Docs
description: 本教程介绍如何在 Java Spring Boot 应用中使用 Azure 应用程序配置的 Key Vault 引用
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: d1fb963753577e9518d93262f9c9c7a1cf984005
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656001"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>教程：在 Java Spring 应用中使用 Key Vault 引用

本教程介绍如何将 Azure 应用程序配置服务与 Azure Key Vault 配合使用。 应用程序配置和 Key Vault 是互补性的服务，大多数应用程序部署中会同时使用两者。

应用程序配置可以创建密钥来引用存储在 Key Vault 中的值，以帮助你结合使用这两个服务。 当应用程序配置创建此类密钥时，它会存储 Key Vault 值的 URI，而不是值本身。

应用程序使用应用程序配置客户端提供程序检索 Key Vault 引用，就如同检索应用程序配置中存储的任何其他密钥一样。 在这种情况下，存储在应用程序配置中的值是引用 Key Vault 中的值的 URI。 这些值不是 Key Vault 值或凭据。 由于客户端提供程序将密钥识别为 Key Vault 引用，因此它使用 Key Vault 来检索其值。

应用程序负责向应用程序配置和 Key Vault 进行适当的身份验证。 这两项服务不直接通信。

本教程介绍如何在代码中实现 Key Vault 引用。 它建立在快速入门中介绍的 Web 应用之上。 请先完成[使用应用程序配置创建 Java Spring 应用](./quickstart-java-spring-app.md)，然后再继续。

你可以使用任何代码编辑器执行本教程中的步骤。 例如，[Visual Studio Code](https://code.visualstudio.com/) 是适用于 Windows、macOS 和 Linux 操作系统的跨平台代码编辑器。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建一个应用程序配置密钥，用于引用 Key Vault 中存储的值
> * 从 Java Spring 应用程序访问此密钥的值。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [创建免费帐户](https://azure.microsoft.com/free/)
* 受支持的 [Java 开发工具包 (JDK)](https://docs.microsoft.com/java/azure/jdk)，版本为 8。
* [Apache Maven](https://maven.apache.org/download.cgi) 版本 3.0 或更高版本。

## <a name="create-a-vault"></a>创建保管库

1. 选择 Azure 门户左上角的“创建资源”选项： 

    ![Key Vault 创建完成后的输出](./media/quickstarts/search-services.png)
1. 在搜索框中输入 **Key Vault**。
1. 在结果列表中，选择左侧的“Key Vault”  。
1. 在“Key Vault”中选择“添加”。  
1. 在“创建 Key Vault”中的右侧提供以下信息： 
    * 选择“订阅”以选择订阅。 
    * 在“资源组”中选择“新建”，然后输入资源组的名称   。
    * 在“Key Vault 名称”中，必须输入唯一的名称。  对于本教程，请输入 **Contoso-vault2**。
    * 在“区域”下拉列表中，选择一个位置。 
1. 将“创建 Key Vault”的其他选项保留默认值。 
1. 选择“创建”  。

目前，只有你的 Azure 帐户有权访问这个新保管库。

![Key Vault 创建完成后的输出](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密

只需执行几个额外的步骤即可将机密添加到保管库。 在本例中，我们将添加一条消息，用于测试 Key Vault 检索功能。 此消息名为 **Message**，我们将在其中存储“Hello from Key Vault”值。

1. 在 Key Vault 属性页中选择“机密”  。
1. 选择“生成/导入”。 
1. 在“创建机密”窗格中输入以下值： 
    * **上传选项**：输入 **Manual**。
    * **Name**：输入 **Message**。
    * **值**：输入 **Hello from Key Vault**。
1. 将“创建机密”的其他属性保留默认值。 
1. 选择“创建”  。

## <a name="add-a-key-vault-reference-to-app-configuration"></a>将 Key Vault 引用添加到应用程序配置

1. 登录 [Azure 门户](https://portal.azure.com)。 选择“所有资源”，然后选择在快速入门中创建的应用程序配置存储实例  。

1. 选择“配置资源管理器”。 

1. 选择“+ 创建” > “Key Vault 引用”，然后指定以下值：  
    * **密钥**：选择 **/application/config.keyvaultmessage**
    * **标签**：将此值保留空白。
    * “订阅”、“资源组”和“Key Vault”：    输入上一部分在密钥保管库中创建的值相对应的值。
    * **机密**：选择在上一部分创建的名为 **Message** 的机密。

## <a name="connect-to-key-vault"></a>连接到 Key Vault

1. 在本教程中，我们将使用一个服务主体向 Key Vault 进行身份验证。 若要创建该服务主体，请使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令：

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    此操作返回一系列键/值对：

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 运行以下命令，使服务主体能够访问 Key Vault：

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. 运行以下命令获取 object-id，然后将其添加到应用配置。

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. 使用上一步中显示的服务主体的值创建以下环境变量：

    * **AZURE_CLIENT_ID**：clientId 
    * **AZURE_CLIENT_SECRET**：clientSecret 
    * **AZURE_TENANT_ID**：tenantId 

> [!NOTE]
> 这些 Key Vault 凭据仅在应用程序中使用。  应用程序使用这些凭据直接通过 Key Vault 进行身份验证，而不涉及应用配置服务。  Key Vault 同时为应用程序和应用配置服务提供身份验证，而无需共享或公开密钥。

## <a name="update-your-code-to-use-a-key-vault-reference"></a>更新代码以使用 Key Vault 引用

1. 打开 MessageProperties.java  。 添加一个名为“keyVaultMessage”  的新变量：

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. 打开 HelloController.java  。 更新 getMessage  方法，以包括从 Key Vault 检索到的消息。

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. 创建一个名为 AzureCredentials.java  的新文件并添加下面的代码。

    ```java
    package com.example;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. 创建一个名为 AppConfiguration.java  的新文件。 并添加以下代码。

    ```java
    package com.example;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. 在资源 META-INF 目录中创建一个名为 spring.factories  的新文件，然后添加以下内容。

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.AppConfiguration
    ```

1. 使用 Maven 生成 Spring Boot 应用程序，然后运行该程序，例如：

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. 应用程序运行以后，请使用 curl 测试该应用程序，例如  ：

      ```shell
      curl -X GET http://localhost:8080/
      ```

    可看到在应用程序配置存储区中输入的消息。 还会看到你在 Key Vault 中输入的消息。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本教程中，我们已创建一个引用 Key Vault 中存储的值的应用程序配置密钥。 若要了解如何在 Java Spring 应用程序中使用功能标记，请继续阅读下一个教程。

> [!div class="nextstepaction"]
> [托管标识集成](./quickstart-feature-flag-spring-boot.md)
