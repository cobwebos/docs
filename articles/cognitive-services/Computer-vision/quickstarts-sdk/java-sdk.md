---
title: 快速入门：适用于 Java 的计算机视觉客户端库
titleSuffix: Azure Cognitive Services
description: 适用于 Java 的计算机视觉客户端库入门。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 15baf5ee2418581056d571340ba6e8009c33e4ca
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829263"
---
# <a name="quickstart-computer-vision-client-library-for-java"></a>快速入门：适用于 Java 的计算机视觉客户端库

适用于 Java 的计算机视觉客户端库入门。 请按照以下步骤安装程序包并试用基本任务的示例代码。 使用计算机视觉可以访问用于处理图像并返回信息的高级算法。

使用适用于 Java 的计算机视觉客户端库可以：

* 分析图像中的标记、文本说明、人脸、成人内容，等等。

[参考文档](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [项目 (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [示例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Java 开发工具包 (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle 生成工具](https://gradle.org/install/)，或其他依赖项管理器。

## <a name="setting-up"></a>设置

### <a name="create-a-computer-vision-azure-resource"></a>创建计算机视觉 Azure 资源

Azure 认知服务由你订阅的 Azure 资源表示。 在本地计算机上使用 [Azure 门户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)或 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) 创建计算机视觉的资源。 还可以：

* 免费获取在七天内有效的[试用密钥](https://azure.microsoft.com/try/cognitive-services/#decision)。 注册之后，[Azure 网站](https://azure.microsoft.com/try/cognitive-services/my-apis/)上会提供此密钥。  
* 在 [Azure 门户](https://portal.azure.com/)上查看资源。

获取试用订阅或资源的密钥后，请为该密钥创建名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

### <a name="create-a-new-gradle-project"></a>创建新的 Gradle 项目

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。 

```console
mkdir myapp && cd myapp
```

从工作目录运行 `gradle init` 命令。 此命令将创建 Gradle 的基本生成文件，包括 *build.gradle.kts*，在运行时将使用该文件创建并配置应用程序。

```console
gradle init --type basic
```

当提示你选择一个 **DSL** 时，选择 **Kotlin**。

找到 *build.gradle.kts*，并使用喜好的 IDE 或文本编辑器将其打开。 然后在该文件中复制以下生成配置。 此配置将项目定义一个 Java 应用程序，其入口点为 **ComputerVisionQuickstarts** 类。 它将导入计算机视觉库。

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

从工作目录运行以下命令，以创建项目源文件夹：

```console
mkdir -p src/main/java
```

导航到新文件夹，创建名为 *ComputerVisionQuickstarts.java* 的文件。 在喜好的编辑器或 IDE 中打开该文件并添加以下 `import` 语句：

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_imports)]

然后添加 **ComputerVisionQuickstarts** 的类定义。

### <a name="install-the-client-library"></a>安装客户端库

本快速入门使用 Gradle 依赖项管理器。 可以在 [Maven 中央存储库](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)中找到客户端库以及其他依赖项管理器的信息。

在项目的 *build.gradle.kts* 文件中，包含计算机视觉客户端库作为依赖项。

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>对象模型

以下类和接口将处理人脸计算机视觉 Java SDK 的某些主要功能。

|Name|说明|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | 所有计算机视觉功能都需要此类。 请使用你的订阅信息实例化此类，然后使用它来生成其他类的实例。|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| 此类来自客户端对象，它直接处理所有图像操作，例如图像分析、文本检测和缩略图生成。
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| 此枚举定义可在标准分析操作中执行的不同类型的图像分析。 请根据需求指定一组 VisualFeatureTypes 值。 |

## <a name="code-examples"></a>代码示例

这些代码片段演示如何使用适用于 Java 的计算机视觉客户端库执行以下任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [分析图像](#analyze-an-image)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE]
> 本快速入门假设你已为计算机视觉密钥创建了名为 `COMPUTER_VISION_SUBSCRIPTION_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

以下代码将 `main` 方法添加到类，并为资源的 Azure 终结点和密钥创建变量。 你需要输入自己的终结点字符串，可以在 Azure 门户的“概述”部分找到该字符串。  

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_mainvars)]

接下来添加以下代码，以创建 [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) 对象并将其传递给稍后要定义的其他方法。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> 如果在启动应用程序后创建了环境变量，则需要关闭再重新打开运行该应用程序的编辑器、IDE 或 shell 才能访问该变量。

## <a name="analyze-an-image"></a>分析图像

以下代码定义方法 `AnalyzeLocalImage`，该方法使用客户端对象分析本地图像并输出结果。 该方法返回文本说明、分类、标记列表、检测到的人脸、成人内容标志、主颜色和图像类型。

### <a name="set-up-test-image"></a>设置测试图像

首先，在项目的 **src/main/** 文件夹中创建 **resources/** 文件夹，并添加要分析的图像。 然后，将以下方法定义添加到 **ComputerVisionQuickstarts** 类。 如有必要，请更改 `pathToLocalImage` 的值，使之与图像文件相匹配。 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>指定视觉特性

接下来，指定要在分析中提取的视觉特征。 有关完整列表，请参阅 [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) 枚举。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>分析
此方法根据每个图像分析范围将详细结果输出到控制台。 建议将此方法调用包含在 Try/Catch 块中

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

### <a name="display-results"></a>显示结果

以上方法调用将返回包含所有提取的信息的 ImageAnalysis 对象。 可以使用如下所示的代码块来输出给定视觉特征的详细信息。

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/ComputerVisionQuickstart.java?name=snippet_analyzelocal_display)]

有关完整的显示选项集，请参阅 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java) 上的示例代码。

## <a name="run-the-application"></a>运行应用程序

可使用以下命令生成应用：

```console
gradle build
```

使用 `gradle run` 命令运行应用程序：

```console
gradle run
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用计算机视觉 Java 库执行基本任务。 接下来，请在参考文档中详细了解该库。

> [!div class="nextstepaction"]
>[计算机视觉参考 (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [什么是计算机视觉？](../Home.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/ComputerVisionQuickstart.java) 上找到此示例的源代码。