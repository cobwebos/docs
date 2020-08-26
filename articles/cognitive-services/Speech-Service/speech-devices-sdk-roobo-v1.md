---
title: 语音设备 SDK Roobo 智能音频开发工具包 v1 - 语音服务
titleSuffix: Azure Cognitive Services
description: 语音设备 SDK、Roobo 智能音频开发工具包 v1 入门的先决条件和说明。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1a32e485dfc9cd465f6dd6d50abdb166737dc3aa
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387976"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>设备：Roobo 智能音频开发工具包

本文提供 Roobo 智能音频开发工具包的设备特定信息。

## <a name="set-up-the-development-kit"></a>设置开发工具包

1. 开发工具包拥有两个 micro USB 连接器。 左侧的连接器用于为开发工具包接通电源，在下图中突出显示为 Power。 右侧的连接器用于控制开发工具包，在图中标记为 Debug。

    ![连接开发工具包](media/speech-devices-sdk/qsg-1.png)

1. 使用 micro USB 数据线为开发工具包接通电源，将电源端口连接到 PC 或电源适配器。 顶部板下的绿色电源指示灯将亮起。

1. 若要控制开发工具包，请使用另一条 micro USB 电缆将调试端口连接到计算机。 务必使用高品质的数据线，以确保可靠的通信。

1. 摆放好开发工具包以进行环形或线性配置。

    |开发工具包配置|方向|
    |-----------------------------|------------|
    |环形|竖直，麦克风面向天花板|
    |线性|在其一端，麦克风面向你（如下图所示）|

    ![线性开发工具包方向](media/speech-devices-sdk/qsg-2.png)

1. 安装证书并设置声音设备的权限。 在命令提示符窗口中键入以下命令：

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > 这些命令使用 Android Debug Bridge `adb.exe`，它是 Android Studio 安装的一部分。 此工具位于 C:\Users\[用户名]\AppData\Local\Android\Sdk\platform-tools 中。 可将该目录添加到你的路径，以便更轻松地调用 `adb`。 否则，必须在调用 `adb` 必须在每个调用 adb 的命令中指定到 adb.exe 的完整安装路径。
    >
    > 如果看到 `no devices/emulators found` 错误，请检查 USB 电缆是否已连接以及是否为高品质电缆。 可使用 `adb devices` 检查并确保计算机可与开发工具包通信，因为它将返回设备列表。
    >
    > [!TIP]
    > 将电脑的麦克风和扬声器调为静音，以确保使用开发工具包的麦克风。 这可以避免电脑中的音频意外触发设备。

1. 如果希望将扬声器连接到开发工具包，可将其连接到音频线路输出。应选择带有 3.5 mm 模拟插头的高品质扬声器。

    ![Vysor 音频](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>开发信息

有关更多开发信息，请参阅 [Roobo 开发指南](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf)。

## <a name="audio"></a>音频

Roobo 提供一个工具用于将所有音频捕获到闪存。 它可能有助于排查音频问题。 已针对每个开发工具包配置提供该工具的一个版本。 在 [Roobo 站点](http://ddk.roobo.com/)上选择设备，然后选择页面底部的“Roobo 工具”链接。 

## <a name="next-steps"></a>后续步骤

* [运行 Android 示例应用](speech-devices-sdk-android-quickstart.md)
