---
title: 快速入门：识别语音，Java - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何识别 Java 中的语音（Windows 或 Linux）
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: fmegen
ms.openlocfilehash: 7d1f26a43866025c3b542fc10a3f316ad0d1dc37
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103109"
---
# <a name="quickstart-recognize-speech-in-java-on-windows-or-linux-by-using-the-speech-service-sdk"></a>快速入门：在 Windows 或 Linux 上使用语音服务 SDK 通过 Java 识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

在本文中，请使用[语音服务 SDK](speech-sdk.md) 创建一个 Java 控制台应用程序。 可以通过电脑的麦克风实时将语音转录为文本。 此应用程序是使用语音 SDK Maven 程序包和 Eclipse Java IDE (v4.8) 在 64 位 Windows 或 Ubuntu Linux 16.04 上构建的。 它在 64 位 Java 8 运行时环境 (JRE) 中运行。

> [!NOTE]
> 对于语音设备 SDK 和 Roobo 设备，请参阅[语音设备 SDK](speech-devices-sdk.md)。

## <a name="prerequisites"></a>先决条件

需要具有语音服务订阅密钥才能完成此快速入门。 你可以免费获得一个。 有关详细信息，请参阅[免费试用语音服务](get-started.md)。


## <a name="create-and-configure-project"></a>创建并配置项目

如果使用的是 Ubuntu 16.04，在启动 Eclipse 之前，请运行以下命令来确保已安装了必需的程序包。

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

1. 启动 Eclipse。

1. 在 Eclipse Launcher 中，在“工作区”字段中输入某个新工作区目录的名称。 然后选择“启动”。

   ![Eclipse Launcher 的屏幕截图](media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. 片刻之后，Eclipse IDE 的主窗口将会显示。 如果出现了欢迎屏幕，请将其关闭。

1. 从 Eclipse 菜单栏上，通过选择“文件” > “新建” > “项目”创建一个新项目。

1. 将显示“新建项目”对话框。 选择“Java 项目”，然后选择“下一步”。

   ![“新建项目”对话框的屏幕截图，其中突出显示了 Java 项目](media/sdk/qs-java-jre-02-select-wizard.png)

1. “新建 Java 项目”向导随即启动。 在“项目名称”字段中，输入 **quickstart**，然后选择 **JavaSE 1.8** 作为执行环境。 选择“完成”。

   ![“新建 Java 项目”向导的屏幕截图](media/sdk/qs-java-jre-03-create-java-project.png)

1. 如果出现了“打开关联的透视图?”窗口，请选择“打开透视图”。

1. 在**包资源管理器**中，右键单击 **quickstart** 项目。 从上下文菜单中选择“配置” > “转换为 Maven 项目”。

   ![包资源管理器的屏幕截图](media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. 此时将显示“新建 POM”窗口。 在“组 ID”字段中输入 **com.microsoft.cognitiveservices.speech.samples**，然后在“项目 ID”字段中输入 **quickstart**。 然后选择“完成”。

   ![“新建 POM”窗口的屏幕截图](media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. 打开 **pom.xml** 文件并对其进行编辑。

   * 在文件末尾，在右标记 `</project>` 前面，创建一个 `repositories` 元素，使其中包含对语音 SDK 的 Maven 存储库的引用，如下所示：

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#repositories)]

  * 还添加一个 `dependencies` 元素，使用语音 SDK 1.1.0 版本作为依赖项：

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/pom.xml#dependencies)]

   * 保存更改。

## <a name="add-sample-code"></a>添加示例代码

1. 若要向 Java 项目添加新的空类，请选择“文件” > “新建” > “类”。

1. 在“新建 Java 类”窗口中，在“包”字段内输入 **speechsdk.quickstart**，在“名称”字段内输入 **Main**。

   ![“新建 Java 类”窗口的屏幕截图](media/sdk/qs-java-jre-06-create-main-java.png)

1. 将 `Main.java` 中的所有代码替换为以下代码片段：

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. 将字符串 `YourSubscriptionKey` 替换为你的订阅密钥。

1. 将字符串 `YourServiceRegion` 替换为与订阅关联的[区域](regions.md)（例如，免费试用版订阅的 `westus`）。

1. 保存对项目的更改。

## <a name="build-and-run-the-app"></a>生成并运行应用

按 F11，或选择“运行” > “调试”。
接下来的 15 秒，通过麦克风提供的语音输入将被识别并记录到控制台窗口中。

![成功识别后的控制台输出的屏幕截图](media/sdk/qs-java-jre-07-console-output.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
在 `quickstart/java-jre` 文件夹中查找此示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用适用于 Java 的语音 SDK 从语音中识别意向](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>另请参阅

- [翻译语音](how-to-translate-speech-csharp.md)
- [自定义声学模型](how-to-customize-acoustic-models.md)
- [自定义语言模型](how-to-customize-language-model.md)
