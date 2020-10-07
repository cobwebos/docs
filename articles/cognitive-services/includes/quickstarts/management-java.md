---
title: 快速入门：适用于 Java 的 Azure 管理客户端库
description: 在本快速入门中，开始使用适用于 Java 的 Azure 管理客户端库。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 346854d5990ac6861bd4eb93914bb1745b90bfa5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321534"
---
[参考文档](https://docs.microsoft.com/java/api/com.microsoft.azure.management.cognitiveservices?view=azure-java-stable) | [库源代码](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18) | [包 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="prerequisites"></a>先决条件

* 一个有效的 Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
* 最新版本的 [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>创建新的 Java 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

在工作目录中运行以下命令：

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>安装客户端库

本快速入门使用 Gradle 依赖项管理器。 可以在 [Maven 中央存储库](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)中找到客户端库以及其他依赖项管理器的信息。

在项目的 build.gradle.kts 文件中，以 `implementation` 语句的形式包含客户端库及所需的插件和设置。

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>导入库

导航到新的 src/main/java 文件夹，并创建名为 Management.java 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>验证客户端

在 Management.java 中添加类，然后在其中添加以下字段及其值。 使用创建的服务主体和其他 Azure 帐户信息填写其值。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

然后，在 main 方法中，使用这些值构造 CognitiveServicesManager 对象 。 所有 Azure 管理操作都需要此对象。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>调用管理方法

将以下代码添加到 Main 方法中，以列出可用资源、创建示例资源、列出拥有的资源，然后删除示例资源。 你将在后续步骤中定义这些方法。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

### <a name="choose-a-service-and-pricing-tier"></a>选择服务和定价层

创建新资源时，需要知道要使用的服务的种类，以及所需的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/)（或 SKU）。 创建资源时，将此信息和其他信息用作参数。 可以通过调用以下方法来获取可用认知服务“种类”的列表：

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="create-a-cognitive-services-resource"></a>创建认知服务资源

若要创建并订阅新的认知服务资源，请使用 create 方法。 此方法向传入的资源组添加新的可计费资源。 创建新资源时，需要知道要使用的服务的种类，以及其定价层（或 SKU）和 Azure 位置。 下面的方法使用所有这些参数并创建资源。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

## <a name="view-your-resources"></a>查看资源

若要查看 Azure 帐户下的所有资源（跨所有资源组），请使用以下方法：

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>删除资源

下面的方法从给定的资源组中删除指定的资源。

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>另请参阅

* [Azure 管理 SDK 参考文档](https://docs.microsoft.com/java/api/com.microsoft.azure.management.cognitiveservices?view=azure-java-stable)
* [什么是 Azure 认知服务？](../../Welcome.md)
* [对 Azure 认知服务的请求进行身份验证](../../authentication.md)
* [使用 Azure 门户创建新资源](../../cognitive-services-apis-create-account.md)