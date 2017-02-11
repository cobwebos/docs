---
title: "通过 Azure IoT 中心的消息更改 LED 闪烁行为 | Microsoft Docs"
description: "自定义这些消息，以更改 LED 的开关行为。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "使用 arduino 控制 led"
ms.assetid: 387cd97e-b05e-43c4-b252-f68ad45d524a
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 1e892cee7b977c03b2539a6495b868cd8ab83748
ms.openlocfilehash: 69cfb48789ef72d6594bd3db68170f83c9d171b0


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>更改 LED 的亮起和熄灭行为
## <a name="what-you-will-do"></a>执行的操作
对消息进行自定义以更改 LED 的亮起和熄灭行为。 如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
使用其他函数更改 LED 的开关行为。

## <a name="what-you-need"></a>所需条件
必须已成功完成[在 Intel Edison 上运行示例应用程序，接收云到设备消息][receive-cloud-to-device-messages]这一操作。

## <a name="add-functions-to-appjs-and-gulpfilejs"></a>将函数添加到 app.js 和 gulpfile.js
1. 通过运行以下命令在 Visual Studio Code 中打开示例应用程序：

   ```bash
   cd Lesson4
   code .
   ```
2. 打开 `app.js` 文件，然后在 blinkLED() 函数之后添加以下函数：

   ```javascript
   function turnOnLED() {
     myLed.write(1);
   }

   function turnOffLED() {
     myLed.write(0);
   }
   ```

   ![增加了函数的 app.js 文件](media/iot-hub-intel-edison-lessons/lesson4/updated_app_node.png)
3. 在 `receiveMessageCallback` 函数的 switch-case 块中的“blink”案例之前添加以下条件：

   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
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

   ![包含已添加函数的 Gulpfile.js 文件][gulpfile]
5. 在 `sendMessage` 函数中，将 `var message = buildMessage(sentMessageCount);` 行替换为以下代码片段中显示的新行：

   ```javascript
   var message = buildCustomMessage(sentMessageCount);
   ```
6. 保存所有更改。

### <a name="deploy-and-run-the-sample-application"></a>部署并运行示例应用程序
运行以下命令，在 Edison 上部署并运行示例应用程序：

```bash
gulp deploy && gulp run
```

此时会看到 LED 开启两秒，然后关闭两秒。 最后的“stop”消息将停止运行示例应用程序。

![打开和关闭][on-and-off]

祝贺你！ 已成功自定义从 IoT 中心发送到 Edison 的消息。

### <a name="summary"></a>摘要
此可选部分展示了如何自定义消息，以便使示例应用程序能够以不同的方式控制 LED 的亮起和熄灭行为。

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[receive-cloud-to-device-messages]: iot-hub-intel-edison-kit-node-lesson4-send-cloud-to-device-messages.md
[gulpfile]: media/iot-hub-intel-edison-lessons/lesson4/updated_gulpfile_node.png
[on-and-off]: media/iot-hub-intel-edison-lessons/lesson4/gulp_on_and_off_node.png



<!--HONumber=Dec16_HO2-->


