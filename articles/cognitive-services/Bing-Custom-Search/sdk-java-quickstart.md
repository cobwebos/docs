---
title: 快速入门：适用于 Java 的必应自定义搜索客户端库
description: 在本快速入门中，通过从必应自定义搜索实例请求搜索结果，开始使用适用于 Java 的必应自定义搜索客户端库。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 1060cceb9241d9c1c490f7e7f12490c734b0a78d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384547"
---
# <a name="quickstart-bing-custom-search-client-library-for-java"></a>快速入门：适用于 Java 的必应自定义搜索客户端库

开始使用适用于 Java 的必应自定义搜索客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。 借助必应自定义搜索 API，可为关注的主题创建定制的无广告搜索体验。

使用适用于 Java 的必应自定义搜索客户端库，可以执行以下操作：

* 使用必应自定义搜索实例在网上查找搜索结果。 

[参考文档](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-java-stable) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [项目 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [示例](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)。
* 最新版 [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。
* 必应自定义搜索实例。 请参阅[快速入门：创建第一个必应自定义搜索实例](quick-start.md)，了解详细信息。

## <a name="setting-up"></a>设置

### <a name="create-a-bing-custom-search-azure-resource"></a>创建必应自定义搜索 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建用于必应自定义搜索的资源。 你还可以：

* 免费获取在 7 天内有效的[试用版密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，它将在 [Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上提供。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源。

获取试用订阅或资源的密钥后，请为该密钥创建名为 `AZURE_BING_CUSTOM_SEARCH_API_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

> [!TIP]
> 如果使用 Gradle，则可在 [Maven 中央存储库](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)中找到客户端库以及其他依赖项管理器的详细信息。

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令创建 Gradle 的基本生成文件，包括 *build.gradle.kts* 文件，在运行时使用该文件配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

## <a name="install-the-client-library"></a>安装客户端库 

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后将以下生成配置复制到其中。 确保 `dependencies` 下包含客户端库：

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

为示例应用创建一个文件夹。 在工作目录中运行以下命令：

```console
mkdir src/main/java
```

导航到新文件夹，创建名为 *BingCustomSearchSample.java* 的文件。 打开它，并添加以下 `import` 语句：


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

创建名为 `BingCustomSearchSample` 的类

```java
public class BingCustomSearchSample {
}
```

在该类中创建一个 `main` 方法，并为资源的密钥创建变量。 如果在启动应用程序后创建了环境变量，请关闭再重新打开运行该应用程序的编辑器、IDE 或 shell，然后才能访问该变量。 稍后将定义方法。

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>对象模型

必应自定义搜索客户端是一个 [BingCustomSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) 对象，该对象通过 [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager?view=azure-java-stable) 对象的 [authenticate()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomSearchManager_authenticate_String_) 方法创建。 可以使用客户端的 [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) 方法发送搜索请求。

API 响应是一个 [SearchResponse](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse?view=azure-java-stable) 对象，该对象包含有关搜索查询的信息以及搜索结果。

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的必应自定义搜索客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [从自定义搜索实例获取搜索结果](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>验证客户端

main 方法应该包含一个使用密钥的 [BingCustomSearchManager](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi?view=azure-java-stable) 对象，并调用其 `authenticate()`。

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>从自定义搜索实例获取搜索结果

使用客户端的 [BingCustomInstances.search()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) 函数，向自定义实例发送搜索查询。 将 `withCustomConfig` 设置为自定义配置 ID，或默认设置为 `1`。 从 API 获得响应以后，检查是否发现了任何搜索结果。 如果发现了搜索结果，请通过调用响应的 `webPages().value().get()` 函数来获取第一个搜索结果，并输出结果的名称和 URL。 

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>运行应用程序

在项目的主目录中使用以下命令来生成应用：

```console
gradle build
```

使用 `run` 目标运行应用程序：

```console
gradle run
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [生成自定义搜索 Web 应用](./tutorials/custom-search-web-page.md)
