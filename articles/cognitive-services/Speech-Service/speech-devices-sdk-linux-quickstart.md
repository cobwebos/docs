---
title: 快速入门：在 Linux 上运行语音设备 SDK - 语音服务
titleSuffix: Azure Cognitive Services
description: Linux 语音设备 SDK 入门的先决条件和说明。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: 5c881551648e8fc6078405e34fa3280723009b20
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490978"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-linux"></a>快速入门：在 Linux 上运行语音设备 SDK 示例应用

本快速入门介绍如何使用适用于 Linux 的语音设备 SDK 来生成支持语音的产品，或将其用作[对话听录](conversation-transcription-service.md)设备。 目前，只有 [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) 受支持。

该应用程序是使用语音 SDK 程序包和 Eclipse Java IDE (v4) 在 64 位 Linux（Ubuntu 16.04、Ubuntu 18.04、Debian 9）上构建的。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

本指南需要一个包含语音服务资源的 [Azure 认知服务](get-started.md)帐户。 如果没有帐户，可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/)获取订阅密钥。

[示例应用程序](https://aka.ms/sdsdk-download-JRE)的源代码随附在语音设备 SDK 中， 也可在 [GitHub 上获取](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* 操作系统：64 位 Linux（Ubuntu 16.04、Ubuntu 18.04、Debian 9）
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* 仅限 [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)。
* 语音服务的 Azure 订阅密钥。 [免费获得一个](get-started.md)。
* 下载用于 Java 的[语音设备 SDK](https://aka.ms/sdsdk-download-JRE) 的最新版本，并将 .zip 提取到工作目录。
   > [!NOTE]
   > JRE-Sample-Release.zip 文件包含 JRE 示例应用，本快速入门假设该应用提取到 /home/wcaltest/JRE-Sample-Release

确保在启动 Eclipse 之前安装这些依赖项。

* 在 Ubuntu 上：

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* 在 Debian 9 上：

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

对话听录目前仅在“centralus”和“eastasia”区域中适用于“en-US”和“zh-CN”。 必须在这些区域中的一个区域内有语音密钥，才能使用对话听录。

如果计划使用意向，则将需要[语音理解服务 (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) 订阅。 若要了解有关 LUIS 和意向识别的详细信息，请参阅[使用 LUIS、C# 识别语音意向](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)。 [示例 LUIS 模型](https://aka.ms/sdsdk-luis)适用于此应用。

## <a name="create-and-configure-the-project"></a>创建和配置项目

1. 启动 Eclipse。

1. 在 Eclipse IDE Launcher 的“工作区”字段中，输入新工作区目录的名称   。 然后选择“启动”。 

   ![Eclipse Launcher 的屏幕截图](media/speech-devices-sdk/eclipse-launcher-linux.png)

1. 片刻之后，Eclipse IDE 的主窗口将会显示。 如果出现了欢迎屏幕，请将其关闭。

1. 从 Eclipse 菜单栏上，依次选择“文件”   > “新建”   > “Java 项目”以新建一个项目  。 如果不可用，请依次选择“项目”  和“Java 项目”  。

1. 此时将启动“新建 Java 项目”向导  。 **浏览**示例项目所在的位置。 选择“完成”。 

   ![“新建 Java 项目”向导的屏幕截图](media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. 在“包资源管理器”  中，右键单击你的项目。 从上下文菜单中选择“配置” > “转换为 Maven 项目”。   选择“完成”。 

   ![包资源管理器的屏幕截图](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. 在“包资源管理器”  中，右键单击你的项目。 选择“属性”  ，然后依次选择“运行/调试设置”   > “新建...”  > **Java 应用程序**。 

1. 此时将显示“编辑配置”  窗口。 在“名称”  字段中，输入 **Main**，并对“Main 类”使用“搜索”   ，以查找和选择 **com.microsoft.cognitiveservices.speech.samples.FunctionsList**。

   ![“编辑启动配置”的屏幕截图](media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. 同样从“编辑配置”  窗口中，选择“环境”  页和“新建”  。 此时将显示“新环境变量”  窗口。 在“名称”  字段中输入 **LD_LIBRARY_PATH**，并在“值”  字段中输入包含 *.so 文件的文件夹，例如 **/home/wcaltest/JRE-Sample-Release**

1. 将 `kws.table` 和 `participants.properties` 复制到项目文件夹 **target/classes**


## <a name="configure-the-sample-application"></a>配置示例应用程序

1. 将语音订阅密钥添加到源代码。 如果想要尝试意向识别，还需要添加[语言理解服务](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)订阅密钥和应用程序 ID。

   对于语音和 LUIS，你的信息将进入 `FunctionsList.java`：

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    如果使用的是对话听录，`Cts.java` 中还需要语音密钥和区域信息：

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. 默认关键字为“Computer”。 还可以尝试所提供的其他关键字之一，例如“Machine”或“Assistant”。 这些备用关键字的资源文件位于语音设备 SDK 的 keyword 文件夹中。 例如，`/home/wcaltest/JRE-Sample-Release/keyword/Computer` 包含用于关键字“Computer”的文件。

   > [!TIP]
   > 还可以[创建自定义关键字](speech-devices-sdk-create-kws.md)。

    若要使用新的关键字，请更新 `FunctionsList.java` 中的下面两行，并将关键字包复制到应用。 例如，若要使用关键字包 `kws-machine.zip` 中的关键字“Machine”，请执行以下操作：

   * 将该关键字包复制到项目文件夹 **target/classes** 中。

   * 使用关键字和包名称更新 `FunctionsList.java`：

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>从 Eclipse 运行示例应用程序

1. 从 Eclipse 菜单栏中，依次选择“运行”   > “运行”  

1. 语音设备 SDK 示例应用程序将会启动，并显示以下选项：

   ![示例语音设备 SDK 的示例应用程序和选项](media/speech-devices-sdk/java-sample-app-linux.png)

1. 试用新的“对话听录”演示  。 通过“会话”   > “启动”  来启动听录。 默认情况下，每个人都是来宾。 但是，如果你拥有参与者的语音签名，则这些签名可以放入项目文件夹 **target/classes** 的 `participants.properties` 中。 若要生成语音签名，请查看[听录对话 (SDK)](how-to-use-conversation-transcription-service.md)。

   ![演示对话听录应用程序](media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>创建并运行独立应用程序

1. 在“包资源管理器”  中，右键单击你的项目。 选择“导出”  。 
1. 此时将显示“导出”  窗口。 展开“Java”  ，选择“可运行的 JAR 文件”  ，然后选择“下一步”  。

   ![“导出”窗口的屏幕截图](media/speech-devices-sdk/eclipse-export-linux.png) 

1. 此时将显示“可运行的 JAR 文件导出”  窗口。 为应用程序选择“导出目标”  ，然后选择“完成”  。
 
   ![“可运行的 JAR 文件导出”的屏幕截图](media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. 请将 `kws.table` 和 `participants.properties` 放入上面选择的目标文件夹中，因为该应用程序需要这些文件。

1. 将 LD_LIBRARY_LIB 设置为包含 *.so 文件的文件夹

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. 运行独立的应用程序

     ```bash
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看发行说明](devices-sdk-release-notes.md)
