---
title: 语音设备 SDK 入门
description: 语音设备 SDK 入门的先决条件和说明。
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/18/2018
ms.author: v-jerkin
ms.openlocfilehash: 266315a731eec8a2c0ab0a880ce9e1db58331184
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283130"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>语音设备 SDK 入门

本文介绍如何使用语音设备 SDK 配置开发电脑和语音设备开发工具包，以开发支持语音的设备。 然后，将生成示例应用程序并将其部署到设备。 

示例应用程序的源代码随附在语音设备 SDK 中，也可在 [GitHub 上](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)获取。

## <a name="prerequisites"></a>先决条件

在使用语音设备 SDK 开始开发之前，收集需要的信息和软件。

* [从 Roobo](http://ddk.roobo.com/) 获取开发工具包。 工具包可用于线性或环形麦克风数组配置；请根据你的需求选择正确的工具包。

    |开发工具包配置|扬声器位置|
    |-----------------------------|------------|
    |环形|设备的任何方向|
    |线性|设备的前面|

* 从语音设备 SDK 的[下载网站](https://shares.datatransfer.microsoft.com/)获取语音设备 SDK 的最新版本，包括 Android 示例应用。 将 ZIP 文件解压缩到本地文件夹（如 `C:\SDSDK`）。

* 在电脑上安装 [Android Studio](https://developer.android.com/studio/) 和 [Vysor](http://vysor.io/download/)。

* 获取语音服务[订阅密钥](get-started.md)。 可获取 30 天免费试用版，或从 Azure 仪表板获取密钥。

* 如果想要使用语音服务的意向识别，请订阅[语言理解服务](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) (LUIS) 并[获取订阅密钥](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/azureibizasubscription)。 

    可[创建一个简单的 LUIS 模型](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/)，或使用语音设备 SDK [下载网站](https://shares.datatransfer.microsoft.com/)提供的示例 LUIS 模型 `LUIS-example.json`。 通过单击“导入新应用”并选择 JSON 文件，将模型的 JSON 文件上传到 [LUIS 门户](https://www.luis.ai/home)。

## <a name="set-up-the-development-kit"></a>设置开发工具包

1. 插入开发工具包的电源适配器。 顶部板下的绿色电源指示器应亮起。

1. 使用迷你 USB 电缆将开发工具包连接到计算机。

    ![连接开发工具包](media/speech-devices-sdk/qsg-1.jpg)

1. 适当设置开发工具包方向。

    |开发工具包配置|方向|
    |-----------------------------|------------|
    |环形|竖直，麦克风面向天花板|
    |线性|在其一端，麦克风面向你（如下所示）|

    ![线性开发工具包方向](media/speech-devices-sdk/qsg-2.jpg)

1. 安装证书和唤醒字（关键字）表文件，并设置声音设备的权限。 在命令窗口中键入以下命令。

    > [!NOTE]
    > 这些命令使用 Android Debug Bridge `adb.exe`，它是 Android Studio 安装的一部分。 此工具可在 `C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools` 中找到。 可将该目录添加到你的路径，以便更轻松地调用 `adb`。 否则，必须在调用 `adb` 的每个命令中指定安装 `adb.exe` 的完整路径。

    ```
    adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/ 
    adb shell
    cd /data/ 
    chmod 777 roobo_setup.sh
    ./roobo_setup.sh
    exit
    ```

    > [!TIP]
    > 将电脑的麦克风和扬声器调为静音。 这样，便可以确定你正在使用开发工具包的麦克风，并且不会通过电脑上的音频意外触发设备。
    
1.  在计算机上启动 Vysor。

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  你的设备应列在“选择设备”下。 单击其旁边的“查看”按钮。 
 
1.  通过单击“设置”，然后单击“WLAN”，连接到无线网络。

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)
 
## <a name="run-a-sample-application"></a>运行示例应用程序

若要运行 Roobo 测试并验证开发工具包设置，请生成并安装示例应用程序。

1.  启动 Android Studio。

1.  选择打开现有 Android Studio 项目。

    ![Android studio - 打开现有项目](media/speech-devices-sdk/qsg-5.png)
 
1.  浏览到 `C:\SDSDK\Android-Sample-Release\example`，然后单击“确定”，打开示例项目。
 
1.  将语音订阅密钥添加到源代码。 如果想要尝试意向识别，还需添加[语言理解服务](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)订阅密钥和应用程序 ID。 

    你的密钥和应用程序信息位于源文件 `MainActivity.java` 中的以下几行中。

    ```java
    // Subscription
    private static final String SpeechSubscriptionKey = "[your speech key]";
    private static final String SpeechRegion = "westus";
    private static final String LuisSubscriptionKey = "[your LUIS key]";
    private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
    private static final String LuisAppId = "[your LUIS app id]"
    ```

1. 默认唤醒字（关键字）为“Computer”。  根据需要，还可以尝试提供的其他唤醒字，“Machine”和“Assistant”。 这些替换字的资源文件可在“keyword”文件夹中的语音设备 SDK 中找到。 例如，`C:\SDSDK\Android-Sample-Release\keyword\Computer` 包含用于“Computer”的文件。

    此外，还可以[创建自定义唤醒字](speech-devices-sdk-create-kws.md)。

    安装所需唤醒字：
 
    * 通过在命令窗口中运行以下命令，在设备的 \data\ 文件夹中创建 `keyword` 文件夹。

        ```
        adb shell
        cd /data
        mkdir keyword
        exit
        ```

    * 将文件 `kws.table`、`kws_g.fst`、`kws_k.fst` 和 `words_kw.txt`) 复制到设备的 \data\keyword\ 文件夹。 在命令窗口中运行以下命令。

        ```
        adb push C:\SDSDK\Android-Sample-Release\keyword\kws.table /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_g.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\kws_k.fst /data/keyword
        adb push C:\SDSDK\Android-Sample-Release\keyword\Computer\words_kw.txt /data/keyword
        ```
    
    * 在示例应用程序中引用这些文件。 在 `MainActivity.java` 中查找以下行。 确保指定的关键字是正在使用的关键字，并且路径指向推送到设备的 `kws.table` 文件。
        
        ```java
        private static final String Keyword = "Computer";
        private static final String KeywordModel = "/data/keyword/kws.table";
        ```

        > [!NOTE]
        > 在你自己的代码中，可以使用 `kws.table` 文件创建关键字模型实例并开始识别，如下所示。
        >
        > ```java
        > KeywordRecognitionModel km = KeywordRecognitionModel.fromFile(KeywordModel);
        > final Task<?> task = reco.startKeywordRecognitionAsync(km);
        > ```

1.  更新包含麦克风数组几何设置的以下行。

    ```java
    private static final String DeviceGeometry = "Circular6+1";
    private static final String SelectedGeometry = "Circular6+1";
    ```

    |变量|含义|可用值|
    |--------|-------|----------------|
    |`DeviceGeometry`|物理麦克风配置|`Circular6+1` 用于环形开发工具包|
    ||| `Linear4` 用于线性开发工具包|
    |`SelectedGeometry`|软件麦克风配置|`Circular6+1` 用于使用所有麦克风的环形开发工具包|
    |||`Circular3+1` 用于使用四个麦克风的环形开发工具包|
    |||`Linear4` 用于使用所有麦克风的线性开发工具包|
    |||`Linear2` 用于使用两个麦克风的线性开发工具包|


1.  通过从“运行”菜单中选择“运行‘应用’”，生成应用程序。 将显示“选择部署目标”对话框。 选择设备，然后单击“确定”，将应用程序部署到设备。

    ![选择部署目标](media/speech-devices-sdk/qsg-7.png)
 
1.  语音设备 SDK 示例应用程序启动，并显示此处显示的选项。

    ![示例语音设备应用程序](media/speech-devices-sdk/qsg-8.png)

1. 尽情体验吧！

## <a name="troubleshooting"></a>故障排除

如果在使用语音服务时发生证书故障，请确保设备具有正确的日期和时间。

有关更多开发信息，请参阅 Roobo 的[开发指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)。

Roobo 提供一种工具，用于将所有音频捕获到闪存，这可帮助解决音频问题。 已针对每个开发工具包配置提供该工具的一个版本。 在 [Roobo 网站](http://ddk.roobo.com/)选择设备，然后单击页底部的“ROOBO 工具”链接。
