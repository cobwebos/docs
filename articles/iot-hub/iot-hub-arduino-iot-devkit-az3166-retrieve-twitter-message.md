---
title: 使用 Azure Functions 检索 Twitter 消息 | Microsoft Docs
description: 使用动作传感器检测抖动，然后使用 Azure Functions 查找包含指定的井号标签的随机推文。
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/07/2018
ms.author: liydu
ms.openlocfilehash: 50ce34cb3289e346e7b637d917730dac8e8a097b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>“Shake, Shake”推文项目 -- 使用 Azure Functions 检索 Twitter 消息

此项目演示如何 Azure Functions 通过动作传感器触发事件。 该应用会检索包含 Arduino 草图中配置的 # 井号标签的随机推文。 该推文显示在 DevKit 屏幕上。

## <a name="what-you-need"></a>所需条件

完成[入门指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)来实现以下目的：

* 将 DevKit 连接到 Wi-Fi。
* 准备开发环境。

一个有效的 Azure 订阅。 如果没有订阅，可通过以下方法之一进行注册：

* 激活 [30 天免费试用版 Microsoft Azure 帐户](https://azure.microsoft.com/free/)
* 声明你的 [Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)（如果你是 MSDN 或 Visual Studio 订阅者）

## <a name="open-the-project-folder"></a>打开项目文件夹

### <a name="start-vs-code"></a>启动 VS Code

- 确保 DevKit **未**连接到计算机。
- 启动 VS Code。
- 将 DevKit 连接到计算机。

> [!NOTE]
> 启动 VS Code 时，可能会收到有关找不到 Arduino IDE 或相关开发板包的错误消息。 如果出现此错误，请关闭 VS Code，然后重新启动 Arduino IDE。 现在，VS Code 应会正确找到 Arduino IDE 的路径。

### <a name="open-arduino-examples-folder"></a>打开 Arduino 示例文件夹

展开左侧的“ARDUINO 示例”部分，浏览到“MXCHIP AZ3166 的示例 > AzureIoT”，然后选择“ShakeShake”。 此时会打开一个新的 VS Code 窗口，其中包含一个项目文件夹。

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

> [!NOTE]
> 也可以从命令面板打开示例。 使用 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 打开命令面板，键入“Arduino”，然后找到并选择“Arduino: Examples”。

## <a name="provision-azure-services"></a>预配 Azure 服务

在解决方案窗口中，通过输入 `task cloud-provision` 并按 `Ctrl+P` (macOS: `Cmd+P`) 来运行任务。

在 VS Code 终端中，交互式命令行指导你预配所需的 Azure 服务：

![cloud-provision](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> 如果在尝试登录 Azure 时，页面停滞在“正在加载”状态，请参阅此[常见问题解答步骤](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure)。
 
## <a name="modify-the-hashtag"></a>修改 # 井号标签

打开 `ShakeShake.ino` 并找到以下代码行：

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

将大括号中的字符串 `iot` 替换为所需的井号标签。 然后，DevKit 会检索包含此步骤中所指定的井号标签的随机推文。

## <a name="deploy-azure-functions"></a>部署 Azure Functions

使用 `Ctrl+P`（macOS：`Cmd+P`）运行 `task cloud-deploy`，开始部署 Azure Functions 代码：

![cloud-deploy](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Azure 函数偶尔无法正常工作。 若要解决此问题，请查看此[常见问题解答步骤](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function)。

## <a name="build-and-upload-the-device-code"></a>生成并上传设备代码

### <a name="windows"></a>Windows

1. 使用 `Ctrl+P` 运行 `task device-upload`。
2. 终端会提示进入配置模式。 为此，请执行以下操作：

   * 按住按钮 A
   * 按下然后松开重置按钮。

3. 屏幕将显示 DevKit ID 和“配置”。
4. 这会设置从 `task cloud-provision` 步骤检索的连接字符串。
5. 然后，VS Code 开始验证 Arduino 草图并将其上传到 DevKit：![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
6. DevKit 将重新启动并开始运行代码。

> [!NOTE]
> 可能会出现“错误: AZ3166: 未知的包”错误消息。 如果未正确刷新开发板包索引，则会出现此错误。 若要解决此问题，请查看此[常见问题解答步骤](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)。

### <a name="macos"></a>macOS

1. 将 DevKit 置于配置模式：按下按钮 A，然后按下重置按钮并松开。 屏幕将显示“配置”。
2. 使用 `Cmd+P` 运行 `task device-upload`，设置从 `task cloud-provision` 步骤检索的连接字符串。
3. 然后，VS Code 开始验证 Arduino 草图并将其上传到 DevKit：![device-upload](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)
4. DevKit 将重新启动并开始运行代码。

> [!NOTE]
> 可能会出现“错误: AZ3166: 未知的包”错误消息。 如果未正确刷新开发板包索引，则会出现此错误。 若要解决此问题，请查看此[常见问题解答步骤](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)。

## <a name="test-the-project"></a>测试项目

应用初始化后，单击并松开按钮 A，然后轻轻摇动 DevKit 开发板。 此操作会检索包含前面指定的哈希标记的随机推文。 在几秒钟内，DevKit 屏幕上会显示一篇推文：

### <a name="arduino-application-initializing"></a>Arduino 应用程序正在初始化...
![Arduino-application-initializing](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>按 A 摇动...
![Press-A-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>准备摇动...
![Ready-to-shake](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>正在处理...
![正在处理](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>按 B 读取...
![Press-B-to-read](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>显示随机推文...
![Display-a-random-tweet](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- 再次按下按钮 A，然后摇动以返回新的推文。
- 按下按钮 B，滚动浏览剩余的推文。

## <a name="how-it-works"></a>工作原理

![示意图](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Arduino 草图将事件发送到 Azure IoT 中心。 此事件触发 Azure Functions 应用。 Azure Functions 应用包含用于连接 Twitter API 和检索推文的逻辑。 然后，它将推文包装成 C2D（云到设备）消息，并将其发回到设备。

## <a name="optional-use-your-own-twitter-bearer-token"></a>可选：使用自己的 Twitter 持有者令牌

出于测试目的，此示例项目使用了预配置的 Twitter 持有者令牌。 但是，每个 Twitter 帐户存在[频率限制](https://dev.twitter.com/rest/reference/get/search/tweets)。 若要使用自己的令牌，请执行以下步骤：

1. 转到 [Twitter 开发人员门户](https://dev.twitter.com/)，注册新的 Twitter 应用。

2. [获取应用的使用者密钥和使用者机密](https://support.yapsody.com/hc/en-us/articles/203068116-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-)。

3. 使用[某种实用工具](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/)，通过这两个密钥生成 Twitter 持有者令牌。

4. 在 [Azure 门户](https://portal.azure.com/){:target="_blank"} 中，转到“资源组”并找到“Shake, Shake”项目的 Azure 函数（类型：应用服务）。 名称始终包含“shake...”字符串。
  ![azure-function](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. 使用自己的令牌更新 **Functions > shakeshake-cs** 中 `run.csx` 的代码：
  ```csharp
  ...
  string authHeader = "Bearer " + "[your own token]";
  ...
  ```
  ![twitter-token](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. 保存文件并单击“运行”。


## <a name="problems-and-feedback"></a>问题和反馈

### <a name="the-screen-displays-no-tweets-while-every-step-has-run-successfully"></a>屏幕显示“无推文”，而每个步骤已成功运行

这种情况通常发生在首次部署和运行示例时，因为函数应用需要几秒到一分钟的时间才能冷启动应用。 或者，在运行代码时，某些暂时性的问题会导致应用重启。 如果发生这种情况，设备应用可能会在提取推文时超时。 此时，可以尝试下列一种或两种方法解决此问题：

1. 单击 DevKit 上的重置按钮再次运行设备应用。

2. 在 [Azure 门户](https://portal.azure.com/)中，找到创建的 Azure Functions 应用并将其重启：![azure-function-restart](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

### <a name="feedback"></a>反馈

如果遇到其他问题，请参阅[常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或通过以下渠道联系我们：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [堆栈溢出](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>后续步骤

了解如何将 DevKit 设备连接到 Azure IoT 远程监视解决方案加速器和检索推文后，我们建议接下来学习以下教程：

* [Azure IoT 远程监视解决方案加速器概述](https://docs.microsoft.com/azure/iot-suite/)
