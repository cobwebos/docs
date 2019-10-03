---
title: 快速入门：适用于 Java 的内容审查器客户端库 | Microsoft Docs
description: 适用于 Java 的内容审查器客户端库入门。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: pafarley
ms.openlocfilehash: 5d1575818ac35c45af8a7df59c0853389d01031c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698546"
---
# <a name="quickstart-content-moderator-client-library-for-java"></a>快速入门：适用于 Java 的内容审查器客户端库

适用于 Java 的内容审查器客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 内容审查器是一项认知服务，用于检查文本、图像和视频中是否存在可能的冒犯性内容、有风险内容或其他令人不适的内容。 找到此类内容时，此服务会将相应的标签（标记）应用到该内容。 然后，应用会处理标记的内容，使之符合法规的要求，或者为用户维持一个理想的环境。

使用适用于 Java 的内容审查器客户端库可以：

* 审查图像中的成人或猥亵内容、文本或人脸。

[参考文档](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [项目 (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [示例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=content+moderator&sort=0)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版的 [Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。

## <a name="setting-up"></a>设置

### <a name="create-a-content-moderator-azure-resource"></a>创建内容审查器 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建内容审查器的资源。 还可以：

* 免费获取在七天内有效的[试用密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，[Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上会提供此密钥。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源。

获取试用订阅或资源的密钥后，请为该密钥创建名为 `AZURE_CONTENTMODERATOR_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```
运行 `gradle init`。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。 从工作目录运行以下命令：

```console
gradle init --type basic
```

当系统提示你选择一个生成脚本 DSL 时，请选择“Kotlin”。 

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后在该文件中复制以下生成配置。 此配置将项目定义一个 Java 应用程序，其入口点为 **ContentModeratorQuickstart** 类。 它会导入内容审查器 SDK 以及用于 JSON 序列化的 Gson SDK。

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

从工作目录运行以下命令，以创建项目源文件夹。

```console
mkdir -p src/main/java
```

然后，在新文件夹中创建名为 *ContentModeratorQuickstart.java* 的文件。 在喜好的编辑器或 IDE 中打开该文件，并在顶部导入以下库：

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>对象模型

以下类将处理内容审查器 Java SDK 的某些主要功能。

|Name|说明|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|所有内容审查器功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|此类提供用于分析成人内容、个人信息或人脸的功能。|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|此类提供用于在文本中分析语言、猥亵内容、错误和个人信息的功能。|
|[评审](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|此类提供评审 API 的功能，包括用于创建作业、自定义工作流和人工评审的方法。|


## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的内容审查器客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [审查图像](#moderate-images)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 此步骤假设你已为内容审查器密钥创建了名为 `AZURE_CONTENTMODERATOR_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

在应用程序的 `main` 方法中，使用订阅终结点值和订阅密钥环境变量创建一个 [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) 对象。 

> [!NOTE]
> 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>审查图像

### <a name="get-images"></a>获取图像

在项目的 **src/main/** 文件夹中，创建一个 **resources** 文件夹并导航到该文件夹。 然后创建新的文本文件 *ImageFiles.txt*。 在此文件中添加要分析的图像的 URL &mdash; 在每行添加一个 URL。 可使用以下示例内容：

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="use-helper-class"></a>使用帮助器类

然后，在 *ContentModeratorQuickstart* 文件中，将以下类定义添加到 **ContentModeratorQuickstart** 类中。 稍后在图像审查过程中将使用此内部类。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>循环访问图像

接下来，在 `main` 方法的底部添加以下代码： 或者，可将此代码添加到从 `main` 调用的独立方法。 此代码逐步运行 _ImageFiles.txt_ 文件的每一行。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>检查成人/猥亵内容
以下代码行在给定 URL 处的图像中检查成人或猥亵内容。 有关这些术语的信息，请参阅《图像审查概念指南》。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>检查文本
以代码行在图像中检查可见文本。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>检查人脸
以代码行在图像中检查人脸。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

最后，将返回的信息存储在 `EvaluationData` 列表中。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>输出结果

在 `while` 循环的后面添加以下代码，用于将结果输出到控制台和输出文件 *src/main/resources/ModerationOutput.json*。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

结束 `try` 语句并添加 `catch` 语句以完成该方法。

[!code-java[](~/cognitive-services-java-sdk-samples/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>运行应用程序

可使用以下命令生成应用：

```console
gradle build
```

使用 `gradle run` 命令运行应用程序：

```console
gradle run
```

然后导航到 *src/main/resources/ModerationOutput.json* 文件并查看内容审查的结果。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 Java 库来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

> [!div class="nextstepaction"]
>[图像审查的概念](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [什么是 Azure 内容审查器？](./overview.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/blob/master/ContentModerator/ContentModeratorQuickstart/src/main/java/ContentModeratorQuickstart.java) 上找到此示例的源代码。