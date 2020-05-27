---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 57244c2022d06d8f1768dbe8bcc971aa59acd6a9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673129"
---
本快速入门介绍如何使用适用于 Android 的语音设备 SDK 来生成支持语音的产品，或将其用作[对话听录](../conversation-transcription-service.md)设备。

本指南需要一个包含语音服务资源的 [Azure 认知服务](../get-started.md)帐户。 如果没有帐户，可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/)获取订阅密钥。

示例应用程序的源代码随附在语音设备 SDK 中， 也可在 [GitHub 上获取](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)。

## <a name="prerequisites"></a>必备条件

开始使用语音设备 SDK 之前，需要：

- 按[开发工具包](../get-speech-devices-sdk.md)中提供的说明启动设备。

- 下载[语音设备 SDK](https://aka.ms/sdsdk-download) 的最新版本，并将 .zip 提取到工作目录。

  > [!NOTE]
  > 本快速入门假设应用已解压缩到 C:\SDSDK\Android-Sample-Release

- 获取[语音服务的 Azure 订阅密钥](../get-started.md)

- 如果计划使用对话听录，则必须使用[圆形麦克风设备](../get-speech-devices-sdk.md)，该功能目前仅适用于“centralus”区域的“en-US”和“eastasia”区域的“zh-CN”。 必须在这些区域中的一个区域中具有语音密钥，才能使用对话听录。

- 如果计划使用语音服务来确定用户话语中的意向（或行动），则需[语言理解服务 (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription) 订阅。 若要了解有关 LUIS 和意向识别的详细信息，请参阅[使用 LUIS、C# 识别语音意向](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp)。

  可[创建一个简单的 LUIS 模型](https://docs.microsoft.com/azure/cognitive-services/luis/)，或使用示例 LUIS 模型 LUIS-example.json。 可从[语音设备 SDK 下载站点](https://aka.ms/sdsdk-luis)获取示例 LUIS 模型。 选择“导入新应用”并选择 JSON 文件，将模型的 JSON 文件上传到 [LUIS 门户](https://www.luis.ai/home)。 

- 在电脑上安装 [Android Studio](https://developer.android.com/studio/) 和 [Vysor](https://vysor.io/download/)。

## <a name="set-up-the-device"></a>设置设备

1. 在计算机上启动 Vysor。

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. 你的设备应列在“选择设备”下。  选择设备旁边的“视图”按钮。 

1. 选择文件夹图标，然后选择“设置” > “WLAN”连接到无线网络。  

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > 如果你的公司有关于将设备连接到 Wi-Fi 系统的政策，则你需要获取 MAC 地址并联系 IT 部门来了解如何将它连接到公司的 Wi-Fi。
   >
   > 若要查找开发工具包的 MAC 地址，请在开发工具包的桌面上选择文件夹图标。
   >
   > ![Vysor 文件夹](../media/speech-devices-sdk/qsg-10.png)
   >
   > 选择“设置”。  搜索“mac 地址”，然后选择“Mac 地址” > “高级 WLAN”。   记下对话框底部附近显示的 MAC 地址。
   >
   > ![Vysor MAC 地址](../media/speech-devices-sdk/qsg-11.png)
   >
   > 某些公司可能会限制设备可以连接到其 Wi-Fi 系统的时长。 在一定的天数后，可能需要延长开发工具包在 Wi-Fi 系统中的注册。

## <a name="run-the-sample-application"></a>运行示例应用程序

若要验证开发工具包设置，请生成并安装示例应用程序：

1. 启动 Android Studio。

1. 选择“打开现有 Android Studio 项目”  。

   ![Android Studio - 打开现有项目](../media/speech-devices-sdk/qsg-5.png)

1. 转到 C:\SDSDK\Android-Sample-Release\example。 选择“确定”打开示例项目。 

1. 配置 Gradle 以引用语音 SDK。 可以在 Android Studio 的 **Gradle Scripts** 下找到以下文件。

    通过添加 maven 行更新 **build.gradle(Project:example)** ，allprojects 块应该与下面匹配。

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    通过将以下行添加到 dependencies 节来更新 **build.gradle(Module:app)** 。 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.12.0'
    ```
    
1. 将语音订阅密钥添加到源代码。 如果想要尝试意向识别，还需要添加[语言理解服务](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)订阅密钥和应用程序 ID。

   对于语音和 LUIS，你的信息会进入 MainActivity.java：

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   如果使用的是对话听录，conversation.java 中还需要语音密钥和区域信息：

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. 默认关键字为“Computer”。 还可以尝试所提供的其他关键字之一，例如“Machine”或“Assistant”。 这些备用关键字的资源文件位于语音设备 SDK 的 keyword 文件夹中。 例如，C:\SDSDK\Android-Sample-Release\keyword\Computer 包含用于关键字“Computer”的文件。

   > [!TIP]
   > 还可以[创建自定义关键字](../speech-devices-sdk-create-kws.md)。

   要使用新的关键字，请更新 `MainActivity.java` 中的下面两行，并将关键字包复制到应用。 例如，若要使用关键字包 kws-machine.zip 中的关键字“Machine”，请执行以下操作：

   - 将该关键字包复制到“C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\”文件夹中。
   - 使用关键字和包名称更新 `MainActivity.java`：

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

   | 变量 | 含义 | 可用值 |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | 物理麦克风配置 | 环形开发工具包：`Circular6+1` |
   |          |         | 线性开发工具包：`Linear4` |
   | `SelectedGeometry` | 软件麦克风配置 | 使用所有麦克风的环形开发工具包：`Circular6+1` |
   |          |         | 使用四个麦克风的环形开发工具包：`Circular3+1` |
   |          |         | 使用所有麦克风的线性开发工具包：`Linear4` |
   |          |         | 使用两个麦克风的线性开发工具包：`Linear2` |

1. 若要生成应用程序，请在“运行”菜单中选择“运行‘应用’”。   此时会显示“选择部署目标”对话框。 

1. 选择设备，然后选择“确定”，将应用程序部署到设备。 

   ![“选择部署目标”对话框](../media/speech-devices-sdk/qsg-7.png)

1. 语音设备 SDK 示例应用程序将会启动，并显示以下选项：

   ![示例语音设备 SDK 的示例应用程序和选项](../media/speech-devices-sdk/qsg-8.png)

1. 试用新的“对话听录”演示。 使用“启动会话”来启动听录。 默认情况下，每个人都是来宾。 但是，如果你有参与者的语音签名，则这些签名可以放入设备上的 `/video/participants.properties` 文件中。 若要生成语音签名，请查看[听录对话 (SDK)](../how-to-use-conversation-transcription-service.md)。

   ![演示对话听录应用程序](../media/speech-devices-sdk/qsg-15.png)

1. 尽情体验吧！

## <a name="troubleshooting"></a>故障排除

如果无法连接到语音设备。 在命令提示符窗口中键入以下命令。 这会返回设备列表：

```powershell
 adb devices
```

> [!NOTE]
> 此命令使用 Android Debug Bridge `adb.exe`，它是 Android Studio 安装的一部分。 此工具位于 C:\Users\[用户名]\AppData\Local\Android\Sdk\platform-tools 中。 可将该目录添加到你的路径，以便更轻松地调用 `adb`。 否则，必须在调用 `adb` 必须在每个调用 adb 的命令中指定到 adb.exe 的完整安装路径。
>
> 如果看到 `no devices/emulators found` 错误，请检查 USB 电缆是否已连接，并确保使用了高品质的电缆。
