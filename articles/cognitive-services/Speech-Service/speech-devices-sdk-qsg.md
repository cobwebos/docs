---
title: 语音设备 SDK 入门 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音设备 SDK 入门的先决条件和说明。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 114e0b951b2bfe83e8b989646bd07a5fd75b3ee6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894404"
---
# <a name="get-started-with-the-speech-devices-sdk"></a>语音设备 SDK 入门

本文介绍如何使用语音设备 SDK 配置开发电脑和语音设备开发工具包，以开发支持语音的设备。 然后，将生成示例应用程序并将其部署到设备。

示例应用程序的源代码随附在语音设备 SDK 中， 也可在 [GitHub 上获取](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK)。

## <a name="prerequisites"></a>必备组件

在使用语音设备 SDK 开始开发之前，收集需要的信息和软件：

* [从 ROOBO 获取开发工具包](http://ddk.roobo.com/)。 工具包可用于线性或环形麦克风数组配置。 根据需要选择正确的配置。

    |开发工具包配置|扬声器位置|
    |-----------------------------|------------|
    |环形|设备的任何方向|
    |线性|设备的前面|

* 从[语音设备 SDK 下载站点](https://shares.datatransfer.microsoft.com/)获取语音设备 SDK 的最新版本，包括 Android 示例应用。 将 .zip 文件解压缩到本地文件夹（如 C:\SDSDK）。

* 在电脑上安装 [Android Studio](https://developer.android.com/studio/) 和 [Vysor](https://vysor.io/download/)。

* 获取[语音服务的订阅密钥](get-started.md)。 可获取 30 天免费试用版，或从 Azure 仪表板获取密钥。

* 如果你想要使用语音服务的意图识别，订阅[语言理解服务](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)(LUIS) 和[获取订阅密钥](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription)。

    可[创建一个简单的 LUIS 模型](https://docs.microsoft.com/azure/cognitive-services/luis/)，或使用示例 LUIS 模型 LUIS-example.json。 可从[语音设备 SDK 下载站点](https://shares.datatransfer.microsoft.com/)获取示例 LUIS 模型。 选择“导入新应用”并选择 JSON 文件，将模型的 JSON 文件上传到 [LUIS 门户](https://www.luis.ai/home)。

## <a name="set-up-the-development-kit"></a>设置开发工具包

1. 开发工具包拥有两个 micro USB 连接器。 左侧的连接器用于为开发工具包接通电源，在下图中突出显示为 Power。 右侧的连接器用于控制开发工具包，在图中标记为 Debug。

    ![连接开发工具包](media/speech-devices-sdk/qsg-1.png)

1. 使用 micro USB 数据线为开发工具包接通电源，将电源端口连接到 PC 或电源适配器。 顶部板下的绿色电源指示灯将亮起。

1. 若要控制开发工具包，可使用另一根 micro USB 数据线将 debug 端口连接到计算机。 务必使用高品质的数据线，以确保可靠的通信。

1. 摆放好开发工具包以进行环形或线性配置。

    |开发工具包配置|方向|
    |-----------------------------|------------|
    |环形|竖直，麦克风面向天花板|
    |线性|在其一端，麦克风面向你（如下图所示）|

    ![线性开发工具包方向](media/speech-devices-sdk/qsg-2.png)

1. 安装证书和唤醒字（关键字）表文件，并设置声音设备的权限。 在命令提示符窗口中键入以下命令：

   ```
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > 这些命令使用 Android Debug Bridge `adb.exe`，它是 Android Studio 安装的一部分。 此工具位于 C:\Users\[用户名]\AppData\Local\Android\Sdk\platform-tools 中。 可将该目录添加到你的路径，以便更轻松地调用 `adb`。 否则，必须在调用 `adb` 的每个命令中指定安装 adb.exe 的完整路径。
    >
    > 如果看到 `no devices/emulators found` 错误，请检查 USB 数据线是否已连接以及是否为高品质的数据线。 可使用 `adb devices` 检查并确保计算机可与开发工具包通信，因为它将返回设备列表。

    > [!TIP]
    > 将电脑的麦克风和扬声器调为静音，以确保使用开发工具包的麦克风。 这可以避免电脑中的音频意外触发设备。

1.  在计算机上启动 Vysor。

    ![Vysor](media/speech-devices-sdk/qsg-3.png)

1.  你的设备应列在“选择设备”下。 选择设备旁边的“视图”按钮。

1.  选择文件夹图标，然后选择“设置” > “WLAN”连接到无线网络。

    ![Vysor WLAN](media/speech-devices-sdk/qsg-4.png)

    > [!NOTE]
    > 如果你的公司有关于将设备连接到 Wi-Fi 系统的政策，则你需要获取 MAC 地址并联系 IT 部门来了解如何将它连接到公司的 Wi-Fi。
    >
    > 若要查找开发工具包的 MAC 地址，请在开发工具包的桌面上选择文件夹图标。
    >
    >  ![Vysor 文件夹](media/speech-devices-sdk/qsg-10.png)
    >
    > 选择“设置”。 搜索“mac 地址”，然后选择“Mac 地址” > “高级 WLAN”。 记下对话框底部附近显示的 MAC 地址。
    >
    > ![Vysor MAC 地址](media/speech-devices-sdk/qsg-11.png)
    >
    > 某些公司可能会限制设备可以连接到其 Wi-Fi 系统的时长。 在一定的天数后，可能需要延长开发工具包在 Wi-Fi 系统中的注册。
    >
    > 如果希望将扬声器连接到开发工具包，可将其连接到音频线路输出。应选择优质的 3.5-mm 扬声器。
    >
    > ![Vysor 音频](media/speech-devices-sdk/qsg-14.png)

## <a name="run-a-sample-application"></a>运行示例应用程序

若要运行 ROOBO 测试并验证开发工具包设置，请生成并安装示例应用程序：

1. 启动 Android Studio。

1. 选择“打开现有 Android Studio 项目”。

   ![Android Studio - 打开现有项目](media/speech-devices-sdk/qsg-5.png)

1. 转到 C:\SDSDK\Android-Sample-Release\example。 选择“确定”打开示例项目。

1. 将语音订阅密钥添加到源代码。 如果想要尝试意向识别，还需要添加[语言理解服务](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)订阅密钥和应用程序 ID。

   密钥和应用程序信息位于源文件 MainActivity.java 中的以下几行中：

   ```java
   // Subscription
   private static final String SpeechSubscriptionKey = "[your speech key]";
   private static final String SpeechRegion = "westus";
   private static final String LuisSubscriptionKey = "[your LUIS key]";
   private static final String LuisRegion = "westus2.api.cognitive.microsoft.com";
   private static final String LuisAppId = "[your LUIS app ID]"
   ```

1. 默认唤醒字（关键字）为“Computer”。 还可以尝试提供的其他唤醒字，例如“Machine”或“Assistant”。 这些替换唤醒字的资源文件位于语音设备 SDK 的 keyword 文件夹中。 例如，C:\SDSDK\Android-Sample-Release\keyword\Computer 包含用于唤醒字“Computer”的文件。

    还可以[创建自定义唤醒字](speech-devices-sdk-create-kws.md)。

    若要使用新的唤醒文字，更新以下两行的 MainActivity.java，并将唤醒 word 包复制到您的应用程序。 例如，若要使用唤醒从唤醒 word 包 kws word 'Machine'-machine.zip:

   * 将唤醒 word 包复制到文件夹"C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\"。
   * 使用关键字和包名称更新 MainActivity.java: 
    
     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. 更新包含麦克风阵列几何设置的以下行：

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```
   下表描述了可用的值：

   |变量|含义|可用值|
   |--------|-------|----------------|
   |`DeviceGeometry`|物理麦克风配置|环形开发工具包：`Circular6+1` |
   |||线性开发工具包：`Linear4`|
   |`SelectedGeometry`|软件麦克风配置|使用所有麦克风的环形开发工具包：`Circular6+1`|
   |||使用四个麦克风的环形开发工具包：`Circular3+1`|
   |||使用所有麦克风的线性开发工具包：`Linear4`|
   |||使用两个麦克风的线性开发工具包：`Linear2`|


1. 若要生成应用程序，请在“运行”菜单中选择“运行‘应用’”。 此时会显示“选择部署目标”对话框。

1. 选择设备，然后选择“确定”，将应用程序部署到设备。

    ![“选择部署目标”对话框](media/speech-devices-sdk/qsg-7.png)

1. 语音设备 SDK 示例应用程序将会启动，并显示以下选项：

   ![示例语音设备 SDK 的示例应用程序和选项](media/speech-devices-sdk/qsg-8.png)

1. 尽情体验吧！

## <a name="troubleshooting"></a>故障排除

### <a name="certificate-failures"></a>证书错误

如果使用语音服务时，则会出现证书故障，，请确保你的设备具有正确的日期和时间：

1. 转到“设置”。 在“系统”下选择“日期和时间”。

    ![在“设置”下选择“日期和时间”](media/speech-devices-sdk/qsg-12.png)

1. 将“自动日期和时间”选项保持选中状态。 在“选择时区”下，选择你的当前时区。

    ![选择日期和时区选项](media/speech-devices-sdk/qsg-13.png)

    看到开发工具包的时间与电脑上的时间匹配时，表示开发工具包已连接到 Internet。

    有关更多开发信息，请参阅 [ROOBO 开发指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)。

### <a name="audio"></a>音频

ROOBO 提供一个工具用于将所有音频捕获到闪存。 它可能有助于排查音频问题。 已针对每个开发工具包配置提供该工具的一个版本。 在 [ROOBO 站点](http://ddk.roobo.com/)上选择你的设备，然后选择页面底部的“ROOBO 工具”链接。
