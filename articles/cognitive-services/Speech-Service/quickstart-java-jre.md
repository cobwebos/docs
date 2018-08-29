---
title: 快速入门：识别 Java 中的语音（Windows 或 Linux）
titleSuffix: Microsoft Cognitive Services
description: 了解如何识别 Java 中的语音（Windows 或 Linux）
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 08/16/2018
ms.author: fmegen
ms.openlocfilehash: 11aba1ae6f49d6c00fabd928ae3aefedcbea8ed8
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234253"
---
# <a name="quickstart-recognize-speech-in-java-windows-or-linux"></a>快速入门：识别 Java 中的语音（Windows 或 Linux）

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文档介绍如何为利用语音 SDK 的 Java 运行时环境 (JRE) 创建基于 Java 的控制台应用程序。
该应用程序基于Microsoft 认知服务 SDK Maven 包。
我们将使用 Eclipse 作为集成开发环境 (IDE)。

## <a name="prerequisites"></a>先决条件

* 语音服务的订阅密钥。 请参阅[免费试用语音服务](get-started.md)。
* 一台能够运行 Eclipse 的电脑（Windows x64、Ubuntu 16.04 x64），和一个正常工作的麦克风。
* 适用于 Java 8 的 64 位 JRE/JDK。
* [Eclipse](https://www.eclipse.org) 版本 4.8（64 位版本）。
* 在 Ubuntu 16.04 上，运行以下命令安装所需的包：

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="create-and-configure-your-project"></a>创建并配置项目

1. 启动 Eclipse。

1. 在 Eclipse Launcher 中的“工作区”字段内输入某个新目录的名称。
   然后单击“启动”。

   ![创建 Eclipse 工作区](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. 片刻之后，Eclipse IDE 的主窗口将会显示。
   如果其中出现了“欢迎”屏幕，请将其关闭。

1. 选择“文件”\>“新建”\>“项目”。

1. 在显示的“新建项目”向导中，选择“Java 项目”，然后单击“下一步”。

   ![选择向导](media/sdk/qs-java-jre-02-select-wizard.png)

1. 在随后出现的窗口中，输入 **quickstart** 作为项目名称，然后选择“JavaSE-1.8”（或更高版本）作为执行环境。
   单击“完成”。

   ![选择向导](media/sdk/qs-java-jre-03-create-java-project.png)

1. 如果弹出了标题为“打开关联的透视图?”窗口，请选择“打开透视图”。

1. 在“包资源管理器”中，右键单击“quickstart”项目，并选择“配置”\>“转换为 Maven 项目”。

   ![转换为 Maven 项目](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. 在弹出的窗口中，输入 **com.microsoft.cognitiveservices.speech.samples** 作为“组 ID”，输入 **quickstart** 作为“项目 ID”。选择“完成”。

   ![配置 Maven POM](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. 编辑 **pom.xml** 文件。

  * 在文件末尾的右标记 `</project>` 前面，创建一个 repositories 节，其中包含对语音 SDK 的 Maven 存储库的引用：

    [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * 另外，在其后面接着添加一个 dependencies 节，其中包含语音 SDK 0.6.0 作为依赖项：

    [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

  * 保存更改。

## <a name="add-the-sample-code"></a>添加示例代码

1. 选择“文件”\>“新建”\>“类”，将一个新空类添加到 Java 项目。

1. 在“新建 Java 类”窗口中的“包”字段内输入 **speechsdk.quickstart**，在“名称”字段内输入 **Main**。

   ![创建 Main 类](media/sdk/qs-java-jre-06-create-main-java.png)

1. 将 `Main.java` 中的所有代码替换为以下代码片段：

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

1. 保存对项目的更改。

## <a name="build-and-run-the-sample"></a>生成并运行示例

按 F11，或选择“运行”\>“调试”。
接下来的 15 秒，通过麦克风提供的语音输入将被识别并记录到控制台窗口中。

![成功识别后的控制台输出](media/sdk/qs-java-jre-07-console-output.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/java-jre` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

* [获取我们的示例](speech-sdk.md#get-the-samples)
