---
title: "可选：更改 LED 的亮起和熄灭行为 | Microsoft 文档"
description: "对消息进行自定义以更改 LED 的亮起和熄灭行为。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "使用 raspberry pi 控制 led, raspberry pi led 控制, raspberry pi 控制 led"
ms.assetid: 3b42a4ad-0197-42f6-8ca9-04c883e879e8
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: ffcb9214b8fa645a8a2378c5e7054b9f984addbb
ms.openlocfilehash: dd74474d315c94ae3f2d3e69f8696f9be8508499


---
# <a name="change-the-on-and-off-behavior-of-the-led"></a>更改 LED 的亮起和熄灭行为
## <a name="what-you-will-do"></a>执行的操作
对消息进行自定义以更改 LED 的亮起和熄灭行为。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
使用额外的 Node.js 函数更改 LED 的亮起和熄灭行为。

## <a name="what-you-need"></a>所需条件
必须已成功完成了[在 Raspberry Pi 上运行示例应用程序来检索“云到设备”消息](iot-hub-raspberry-pi-kit-node-lesson4-send-cloud-to-device-messages.md)。

## <a name="add-nodejs-functions"></a>添加 Node.js 函数
1. 通过运行以下命令在 Visual Studio Code 中打开示例应用程序：
   
   ```bash
   cd Lesson4
   code .
   ```
2. 打开 `app.js` 文件，然后在末尾添加以下函数：
   
   ```javascript
   function turnOnLED() {
     wpi.digitalWrite(CONFIG_PIN, 1);
   }
   
   function turnOffLED() {
     wpi.digitalWrite(CONFIG_PIN, 0);
   }
   ```
   
   ![包含已添加函数的 App.js 文件](media/iot-hub-raspberry-pi-lessons/lesson4/updated_app_js.png)
3. 在 `receiveMessageCallback` 函数的开关 case 块中在默认条件之前添加以下条件：
   
   ```javascript
   case 'on':
     turnOnLED();
     break;
   case 'off':
     turnOffLED();
     break;
   ```
   
   现在，你已将示例应用程序配置为通过消息来响应更多指令。 “on”指令使 LED 亮起，“off”指令使 LED 熄灭。
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
   
   ![包含已添加函数的 Gulpfile.js 文件](media/iot-hub-raspberry-pi-lessons/lesson4/updated_gulpfile.png)
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

![包含 on 和 off 消息的示例应用程序](media/iot-hub-raspberry-pi-lessons/lesson4/gulp_on_and_off.png)

祝贺你！ 你已成功自定义了从 IoT 中心发送到 Pi 的消息。

### <a name="summary"></a>摘要
此可选部分展示了如何自定义消息，以便使示例应用程序能够以不同的方式控制 LED 的亮起和熄灭行为。




<!--HONumber=Nov16_HO5-->


