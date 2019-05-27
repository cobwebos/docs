---
title: 快速入门：Android-语音服务上运行语音设备 SDK
titleSuffix: Azure Cognitive Services
description: 先决条件和开始使用 Android 的语音设备 SDK 的说明。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 073166a594088bca04d81883247a5880fcbd1cb7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234496"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-android"></a>快速入门：在 Android 上运行语音设备 SDK 示例应用

在此快速入门中，您将学习如何使用适用于 Android 的语音设备 SDK 来生成语音的产品或将其用作[会话听录](conversation-transcription-service.md)设备。

本指南要求[Azure 认知服务](get-started.md)与语音服务资源的帐户。 如果没有帐户，可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/)获取订阅密钥。

示例应用程序的源代码随附在语音设备 SDK 中， 也可在 [GitHub 上获取](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)。

## <a name="prerequisites"></a>必备组件

在开始使用语音设备 SDK 之前，你将需要：

* 按照与提供的说明你[开发工具包](get-speech-devices-sdk.md)到在设备上的电源。

* 下载最新版[语音设备 SDK](https://aka.ms/sdsdk-download)，并提取到工作目录.zip。
   > [!NOTE]
   > Android 示例 Release.zip 文件包含 Android 示例应用和应用程序已解压缩到 C:\SDSDK\Android-Sample-Release 本快速入门教程

* 若要获取[语音服务的 Azure 订阅密钥](get-started.md)

* 如果你打算使用会话脚本必须使用[循环麦克风设备](get-speech-devices-sdk.md)且该服务当前仅适用于"EN-US"和"zh CN"区域"的 centralus"和"东亚"。 若要使用会话听录这些区域之一中必须 speech 注册表项。

* 如果您计划使用的语音服务来从用户查询文本中识别出的意向 （或操作），则需要[语言理解服务 (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription)订阅。 若要了解有关 LUIS 和意图识别的详细信息，请参阅[使用 LUIS，语音意图识别C# ](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)。

    可[创建一个简单的 LUIS 模型](https://docs.microsoft.com/azure/cognitive-services/luis/)，或使用示例 LUIS 模型 LUIS-example.json。 可从[语音设备 SDK 下载站点](https://aka.ms/sdsdk-luis)获取示例 LUIS 模型。 选择“导入新应用”并选择 JSON 文件，将模型的 JSON 文件上传到 [LUIS 门户](https://www.luis.ai/home)。 

* 在电脑上安装 [Android Studio](https://developer.android.com/studio/) 和 [Vysor](https://vysor.io/download/)。

## <a name="set-up-the-device"></a>将设备设置

1. 在计算机上启动 Vysor。

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1. 你的设备应列在“选择设备”下。  选择设备旁边的“视图”按钮。 

1. 选择文件夹图标，然后选择“设置” > “WLAN”连接到无线网络。  

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > 如果你的公司有关于将设备连接到 Wi-Fi 系统的政策，则你需要获取 MAC 地址并联系 IT 部门来了解如何将它连接到公司的 Wi-Fi。
    >
    > 若要查找开发工具包的 MAC 地址，请在开发工具包的桌面上选择文件夹图标。
    >
    >  ![Vysor 文件夹](media/speech-devices-sdk/qsg-10.png)
    >
    > 选择“设置”。  搜索“mac 地址”，然后选择“Mac 地址” > “高级 WLAN”。   记下对话框底部附近显示的 MAC 地址。
    >
    > ![Vysor MAC 地址](media/speech-devices-sdk/qsg-11.png)
    >
    > 某些公司可能会限制设备可以连接到其 Wi-Fi 系统的时长。 在一定的天数后，可能需要延长开发工具包在 Wi-Fi 系统中的注册。

## <a name="run-the-sample-application"></a>运行示例应用程序

若要验证你的开发工具包安装程序，请生成并安装示例应用程序：

1. 启动 Android Studio。

1. 选择“打开现有 Android Studio 项目”  。

   ![Android Studio - 打开现有项目](media/speech-devices-sdk/qsg-5.png)

1. 转到 C:\SDSDK\Android-Sample-Release\example。 选择“确定”打开示例项目。 

1. 将语音订阅密钥添加到源代码。 如果想要尝试意向识别，还需要添加[语言理解服务](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)订阅密钥和应用程序 ID。

   语音和 LUIS 你的信息进入 MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    如果您使用的会话听录 conversation.java 也需要语音密钥和区域信息：

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. 默认唤醒字（关键字）为“Computer”。 还可以尝试提供的其他唤醒字，例如“Machine”或“Assistant”。 这些替换唤醒字的资源文件位于语音设备 SDK 的 keyword 文件夹中。 例如，C:\SDSDK\Android-Sample-Release\keyword\Computer 包含用于唤醒字“Computer”的文件。

   > [!TIP]
   > 还可以[创建自定义唤醒字](speech-devices-sdk-create-kws.md)。

    若要使用新的唤醒文字，更新中的以下两行`MainActivity.java`，并将唤醒 word 包复制到您的应用程序。 例如，若要使用唤醒 word 包 kws 唤醒单词 'Machine'-machine.zip:

   * 将唤醒 word 包复制到文件夹"C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\"。
   * 更新`MainActivity.java`加关键字和包名称：

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. 更新包含麦克风阵列几何设置的以下行：

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   此表列出了支持的值：

   |变量|含义|可用值|
   |--------|-------|----------------|
   |`DeviceGeometry`|物理麦克风配置|环形开发工具包：`Circular6+1` |
   |||线性开发工具包：`Linear4`|
   |`SelectedGeometry`|软件麦克风配置|使用所有麦克风的环形开发工具包：`Circular6+1`|
   |||使用四个麦克风的环形开发工具包：`Circular3+1`|
   |||使用所有麦克风的线性开发工具包：`Linear4`|
   |||使用两个麦克风的线性开发工具包：`Linear2`|

1. 若要生成应用程序，请在“运行”菜单中选择“运行‘应用’”。   此时会显示“选择部署目标”对话框。 

1. 选择设备，然后选择“确定”，将应用程序部署到设备。 

    ![“选择部署目标”对话框](media/speech-devices-sdk/qsg-7.png)

1. 语音设备 SDK 示例应用程序将会启动，并显示以下选项：

   ![示例语音设备 SDK 的示例应用程序和选项](media/speech-devices-sdk/qsg-8.png)

1. 新添加的是会话脚本演示。 开始使用启动会话转录。 默认情况下每个人都是来宾，但是如果您的参与者的语音签名它们可以放入文件 /video/participants.properties 在设备上。 若要生成语音签名看[转录对话 (SDK)](how-to-use-conversation-transcription-service.md)。

   ![演示会话脚本应用程序](media/speech-devices-sdk/qsg-15.png)

1. 尽情体验吧！

## <a name="troubleshooting"></a>故障排除

   如果无法连接到语音设备。 在命令提示符窗口中键入以下命令。 它将返回的设备的列表：

   ```powershell
    adb devices
   ```

   > [!NOTE]
   > 此命令使用 Android 调试桥， `adb.exe`，这是 Android Studio 中安装的一部分。 此工具位于 C:\Users\[用户名]\AppData\Local\Android\Sdk\platform-tools 中。 可将该目录添加到你的路径，以便更轻松地调用 `adb`。 否则，必须在调用 `adb` 的每个命令中指定安装 adb.exe 的完整路径。
   >
   > 如果看到错误`no devices/emulators found`然后检查您的 USB 电缆是否已连接，确保使用了高质量电缆。
   >

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [查看发行说明](devices-sdk-release-notes.md)
