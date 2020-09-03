---
title: 教程 - 使用托管标识将密钥保管库连接到 Azure Spring Cloud 应用
description: 设置托管标识将密钥保管库连接到 Azure Spring Cloud 应用
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: fc803cbe3dd1ec57b6cd286513efe8393a1471e9
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297121"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>教程：使用托管标识将密钥保管库连接到 Azure Spring Cloud 应用

本文介绍如何为 Azure Spring Cloud 应用创建托管标识，并使用该标识来访问 Azure Key Vault。

Azure Key Vault 可以用来安全地存储令牌、密码、证书、API 密钥和应用的其他机密，并对其访问进行严格控制。 可以在 Azure Active Directory (AAD) 中创建托管标识，并向支持 AAD 身份验证的任何服务（包括 Key Vault）进行身份验证，而无需在代码中显示任何凭据。

## <a name="prerequisites"></a>先决条件

* [注册 Azure 订阅](https://azure.microsoft.com/free/)
* [安装 Azure CLI 2.0.67 或更高版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [安装 Maven 3.0 或更高版本](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>创建资源组
资源组是在其中部署和管理 Azure 资源的逻辑容器。 使用命令 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) 创建一个资源组，以同时包含 Key Vault 和 Spring Cloud：

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>设置密钥保管库
要创建密钥保管库，请使用命令 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)：

> [!Important]
> 每个密钥保管库必须具有唯一的名称。 将以下示例中的 <your-keyvault-name> 替换为你的密钥保管库名称。

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

记下返回的 `vaultUri`（其格式将为“https://<your-keyvault-name>.vault.azure.net”）。 在后续步骤中将使用它。

现在可以使用命令 [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) 将机密放入密钥保管库中：

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>创建 Azure Spring Cloud 服务和应用
安装相应的扩展后，使用 Azure CLI 命令 `az spring-cloud create` 创建 Azure Spring Cloud 实例。 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
以下示例按 `--assign-identity` 参数的请求，创建名为 `springapp` 且已启用系统分配托管标识的应用。

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --is-public true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

记下返回的 `url`（其格式将为“https://<your-app-name>.azuremicroservices.io”）。 在后续步骤中将使用它。


## <a name="grant-your-app-access-to-key-vault"></a>请授予应用对密钥保管库的访问权限
使用 `az keyvault set-policy` 为应用授予密钥保管库正确的访问权限。
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>使用 Spring Boot 简易版构建示例 Spring Boot 应用
此应用将有权从 Azure Key Vault 获取机密。 使用简易版应用：[Azure Key Vault 机密 Spring Boot 简易版](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets)。  Azure Key Vault 添加为 Spring PropertySource 的实例。  可以像任何外部化配置属性（如文件中的属性）一样方便地访问和使用存储在 Azure Key Vault 中的机密。 

1. 使用 Azure Key Vault Spring 简易版从 start.spring.io 生成示例项目。 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. 在应用中指定密钥保管库。 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    要对 Azure Spring Cloud 应用使用托管标识，请将具有以下内容的属性添加到 src/main/resources/application.properties。

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > 必须如上所示将密钥保管库 URL 添加到 `application.properties`。 否则，在运行时可能不会捕获密钥保管库 URL。

3. 将代码示例添加到 src/main/java/com/example/demo/DemoApplication.java。 它从密钥保管库检索连接字符串。 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    如果打开 pom.xml，你将看见 `azure-keyvault-secrets-spring-boot-starter` 的依赖项。 将此依赖项添加到 pom.xml 中的项目。 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. 打包示例应用。 

    ```azurecli
    mvn clean package
    ```

5. 现在，你可以使用 Azure CLI 命令 `az spring-cloud app deploy` 将应用部署到 Azure。 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  要测试应用，请访问公共终结点或测试终结点。

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    你将看到消息“已成功从密钥保管库 https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE 中获取了机密 connectionString 的值；”。

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>使用 Java SDK 生成示例 Spring Boot 应用

此示例可从 Azure 密钥保管库设置和获取机密。 [适用于 Java 的 Azure Key Vault 机密客户端库](https://docs.microsoft.com/java/api/overview/azure/security-keyvault-secrets-readme?view=azure-java-stablelibrary)提供跨 Azure SDK 的 Azure Active Directory 令牌身份验证支持。 它提供一组可用于构建 Azure SDK 客户端以支持 AAD 令牌身份验证的 TokenCredential 实现。

使用 Azure Key Vault 机密客户端库，可以安全地存储令牌、密码、API 密钥和其他机密并控制对它们的访问权限。 此库提供了创建、检索、更新、删除、清除、备份、还原和列出机密及其版本的操作。

1. 克隆示例项目。 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. 在应用中指定密钥保管库。 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    要对 Azure Spring Cloud 应用使用托管标识，请将具有以下内容的属性添加到“src/main/resources/application.properties”。

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. 通过添加 [ManagedIdentityCredentialBuilder](https://docs.microsoft.com/java/api/com.azure.identity.managedidentitycredentialbuilder?view=azure-java-stable)，从 Azure Active Directory 和 [SecretClientBuilder](https://docs.microsoft.com/java/api/com.azure.security.keyvault.secrets.secretclientbuilder?view=azure-java-stable) 获取令牌，以便在代码中从密钥保管库设置或获取机密。

    从克隆示例项目的 [MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) 获取示例。

    并添加 `azure-identity` 和 `azure-security-keyvault-secrets` 作为 pom.xml 中的依赖项。 从克隆示例项目的 [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) 获取示例。 

4. 打包示例应用。 

    ```azurecli
    mvn clean package
    ```

5. 现在，使用 Azure CLI 命令 `az spring-cloud app deploy` 将应用部署到 Azure。 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. 访问公共终结点或测试终结点以测试应用。 

    首先，获取你在 Azure Key Vault 中设置的机密值。 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    你将看到消息“已成功从密钥保管库 https://<your-keyvault-name>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE 中获取了机密 connectionString 的值；”。

    现在，创建一个机密，然后使用 Java SDK 检索它。 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    你将看到消息“已成功从密钥保管库 https://<your-keyvault-name>.vault.azure.net: success 获取了机密测试的值”。 

## <a name="next-steps"></a>后续步骤

* [如何使用 Azure Spring Cloud 中的托管标识访问 Storage Blob](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [如何为 Azure Spring Cloud 应用程序启用系统分配的托管标识](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity)
* [了解有关 Azure 资源的托管标识的详细信息](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [在 GitHub Actions 中使用密钥保管库对 Azure Spring Cloud 进行身份验证](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-github-actions-key-vault)
