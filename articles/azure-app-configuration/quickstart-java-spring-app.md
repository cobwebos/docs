---
title: 了解如何使用 Azure 应用配置的快速入门 | Microsoft Docs
description: 将 Azure 应用配置与 Java Spring 应用结合使用的快速入门。
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: d607d6cd813b23051e1676153cbb134261bcf5bc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960594"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>快速入门：使用应用配置创建 Java Spring 应用

Azure 应用配置是 Azure 中的托管配置服务。 通过它可以在与代码分离的一个位置轻松存储和管理所有应用程序设置。 本快速入门介绍如何将服务合并到 Java Spring 应用中。

可使用任何代码编辑器来完成本快速入门中的步骤。 但是，[Visual Studio Code](https://code.visualstudio.com/) 是一个很好的选项，可用于 Windows、macOS 和 Linux 平台。

## <a name="prerequisites"></a>先决条件

要完成此快速入门，安装受支持的 [Java 开发套件 (JDK)](https://aka.ms/azure-jdks) 版本 8 以及 [Apache Maven](http://maven.apache.org/) 版本 3.0 或更高版本。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>创建应用配置存储区

1. 要创建新应用配置存储区，请先登录到 [Azure 门户](https://aka.ms/azconfig/portal)。 在页面的左上方，单击“+ 创建资源”。 在“搜索市场”文本框中，键入“应用配置”并按 Enter。

    ![搜索应用配置](./media/quickstarts/azure-app-configuration-new.png)

2. 在搜索结果中单击“应用配置”，然后单击“创建”。

3. 在“应用配置” > “创建”页中，输入以下设置：

    | 设置 | 建议的值 | 说明 |
    |---|---|---|
    | **资源名称** | 全局唯一名称 | 输入用于应用配置存储区资源的唯一资源名称。 该名称必须是包含 1 到 63 个字符的字符串，只能包含数字、字母和 `-` 字符。 该名称的开头或末尾不能是 `-` 字符，并且连续的 `-` 字符无效。  |
    | **订阅** | 订阅 | 选择要用来测试应用配置的 Azure 订阅。 如果帐户只有一个订阅，则会自动选择该订阅并且不显示“订阅”下拉菜单。 |
    | **资源组** | AppConfigTestResources | 为应用配置存储区资源选择或创建资源组。 此组可用于组织多个资源，删除该资源组可以同时删除这些资源。 有关详细信息，请参阅[使用资源组管理 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 |
    | **位置** | 美国中部 | 使用“位置”指定在其中托管 SignalR 资源的地理位置。 为获得最佳性能，我们建议在应用程序的其他组件所在的同一区域创建资源。 |

    ![创建应用配置存储区](./media/quickstarts/azure-app-configuration-create.png)

4. 单击“创建”。 部署可能需要几分钟才能完成。

5. 部署完成后，单击“设置” > “访问密钥”。 记下只读主键或读写主键密钥连接字符串。 稍后将使用它来配置应用程序以与刚刚创建的应用配置存储区进行通信。 连接字符串具有以下格式：

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    需要在应用程序中使用整个字符串。

6. 单击“键/值资源管理器”和“+ 创建”来添加以下键值对：

    | 密钥 | 值 |
    |---|---|
    | /application/config.message | 你好 |

    现在，将“标签”和“内容类型”保留为空。

## <a name="create-a-spring-boot-app"></a>创建 Spring Boot 应用

将使用 [Spring Initializr](https://start.spring.io/) 新建 Spring Boot 项目。

1. 浏览到 <https://start.spring.io/>。

2. 指定以下选项：

   * 使用 **Java** 生成一个 **Maven** 项目。
   * 指定一个其值大于或等于 2.0 的 **Spring Boot** 版本。
   * 指定应用程序的“组”和“项目”名称。
   * 添加 **Web** 依赖项。

3. 指定上面列出的选项后，请单击“生成项目”。 出现提示时，将项目下载到本地计算机中的路径。

## <a name="connect-to-app-configuration-store"></a>连接到应用配置存储区

1. 在本地系统中提供文件后，就可以对简单的 Spring Boot 应用程序进行编辑。 在应用的根目录中找到 pom.xml 文件。

2. 在文本编辑器中打开 *pom.xml* 文件，将 Spring Cloud Azure Config Starter 添加到 `<dependencies>` 列表：

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.RC2</version>
    </dependency>
    ```

3. 在应用的包目录中创建名为 MessageProperties.java 的新 Java 文件。 添加以下行。

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. 在应用的包目录中创建新的名为 HelloController.java 的 Java 文件。 添加以下行。

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. 打开主应用程序 Java 文件，并添加 `@EnableConfigurationProperties`以启用此功能。

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. 在应用的资源目录下新建名为 `bootstrap.yaml` 的文件，将以下行添加到文件中，将示例值替换为应用配置存储区的相应属性。

    ```yaml
    spring:
        cloud:
            azure:
                config:
                    stores:
                        - connection-string: [your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>在本地生成并运行应用

1. 使用 Maven 生成 Spring Boot 应用程序，然后运行该程序，例如：

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. 应用程序运行以后，即可使用 *curl* 对其进行测试，例如：

      ```shell
      curl -X GET http://localhost:8080/
      ```
    应看到输入到应用配置存储区的消息。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已经创建了一个新的应用配置存储区，并将其用于Java Spring 应用。 请访问 [Azure 主页上的 Spring](https://docs.microsoft.com/java/azure/spring-framework/)，了解详细信息。

要了解有关使用应用配置的详细信息，请继续阅读下一个演示身份验证的教程。

> [!div class="nextstepaction"]
> [Azure 资源集成的托管标识](./integrate-azure-managed-service-identity.md)