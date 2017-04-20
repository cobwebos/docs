---
title: "将 Raspberry Pi (C) 连接到 Azure IoT - 第 4 课：修改应用 |Microsoft Docs"
description: "自定义这些消息，以更改 LED 的开关行为。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "使用 raspberry pi 控制 led, raspberry pi led 控制, raspberry pi 控制 led"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: 0201b8ed-d5e6-4445-9a4d-1305003d1eff
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: c1c999e40ecc92a1018067822be66f14b61a8c36
ms.lasthandoff: 01/24/2017


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>更改 LED 的亮起和熄灭行为
## <a name="what-you-will-do"></a>执行的操作
对消息进行自定义以更改 LED 的亮起和熄灭行为。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
使用额外的 Node.js 函数更改 LED 的亮起和熄灭行为。

## <a name="what-you-need"></a>所需条件
必须已成功完成了[在 Raspberry Pi 上运行示例应用程序来检索“云到设备”消息](iot-hub-raspberry-pi-kit-c-lesson4-send-cloud-to-device-messages.md)。

## <a name="add-functions-to-mainc-and-gulpfilejs"></a>将函数添加到 main.c 和 gulpfile.js
1. 通过运行以下命令在 Visual Studio Code 中打开示例应用程序：

   ```bash
   cd Lesson4
   code .
   ```
2. 打开 `main.c` 文件，然后在 blinkLED() 函数之后添加以下函数：

   ```c
   static void turnOnLED()
   {
     digitalWrite(LED_PIN, HIGH);
   }

   static void turnOffLED()
   {
     digitalWrite(LED_PIN, LOW);
   }
   ```

   ![添加了函数的 main.c 文件](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_c.png)
3. 在 `receiveMessageCallback` 函数的 `if` 块的默认条件之前添加以下条件：

   ```c
   else if (0 == strcmp((const char*)value, "\"on\""))
   {
       turnOnLED();
   }
   else if (0 == strcmp((const char*)value, "\"off\""))
   {
       turnOffLED();
   }
   ```

   现在已将示例应用程序配置为通过消息响应更多指令。 “on”指令使 LED 亮起，“off”指令使 LED 熄灭。
4. 打开 gulpfile.js 文件，然后在函数 `sendMessage` 之前添加一个新函数：

   ```javascript
   var buildCustomMessage = function (messageId) {
     if ((messageId & 1) && (messageId < MAX_MESSAGE_COUNT)) {
       return new Message(JSON.stringify({ command: 'on', messageId: messageId }));
     } else if (messageId < MAX_MESSAGE_COUNT) {
       return new Message(JSON.stringify({ command: 'off', messageId: messageId }));
     } else {
       return new Message(JSON.stringify({ command: 'stop', messageId: messageId }));
     }
   }
   ```

   ![包含已添加函数的 Gulpfile.js 文件](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile_c.png)
5. 在 `sendMessage` 函数中，将 `var message = buildMessage(sentMessageCount);` 行替换为以下代码片段中显示的新行：

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 保存所有更改。

### <a name="deploy-and-run-the-sample-application"></a>部署并运行示例应用程序
通过运行以下命令在 Pi 上部署并运行示例应用程序：

```bash
gulp deploy && gulp run
```

你应当会看到 LED 亮起两秒，然后熄灭两秒。 最后的“stop”消息将停止运行示例应用程序。

![包含 on 和 off 消息的示例应用程序](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off_c.png)

祝贺你！ 你已成功自定义了从 IoT 中心发送到 Pi 的消息。

### <a name="summary"></a>摘要
此可选部分展示了如何自定义消息，以便使示例应用程序能够以不同的方式控制 LED 的亮起和熄灭行为。

