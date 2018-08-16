---
title: 使用 Azure Functions 和认知服务创建 IoT DevKit 翻译 | Microsoft Docs
description: 使用 IoT DevKit 上的麦克风接收语音消息，并使用 Azure 认知服务将其处理为英文翻译文本
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: acfff95afacfa1e5c75a799ba84d64cfa0579f66
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592085"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>将 IoT DevKit AZ3166 与 Azure Functions 和认知服务配合使用生成语言翻译

本文介绍如何使用 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/)将 IoT DevKit 设为语言翻译。 它会录制你的声音并将其翻译为 DevKit 屏幕上显示的英文文本。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是具有多种外设和传感器的集成 Arduino 兼容板。 可以使用[适用于 Arduino 的 Visual Studio Code 扩展](https://aka.ms/arduino)针对其进行开发。 它附带了一个不断增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，可指导你构建物联网 (IoT) 解决方案的原型，以利用 Microsoft Azure 服务。

## <a name="what-you-need"></a>所需条件

完成[入门指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)来实现以下目的：

* 将 DevKit 连接到 Wi-Fi
* 准备开发环境

一个有效的 Azure 订阅。 如果没有订阅，可以通过以下两种方法之一进行注册：

* 激活 [30 天免费试用版 Microsoft Azure 帐户](https://azure.microsoft.com/free/)
* 声明你的 [Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)（如果你是 MSDN 或 Visual Studio 订阅者）

## <a name="open-the-project-folder"></a>打开项目文件夹

首先，打开项目文件夹。 

### <a name="start-vs-code"></a>启动 VS Code

- 确保 DevKit 已连接到电脑。

- 启动 VS Code。

- 将 DevKit 连接到计算机。

### <a name="open-the-arduino-examples-folder"></a>打开 Arduino 示例文件夹

展开左侧的“ARDUINO 示例”>“MXCHIP AZ3166 的示例”>“AzureIoT”，然后选择“DevKitTranslator”。 此时会打开一个新的 VS Code 窗口，其中显示项目文件夹。 如果看不到“MXCHIP AZ3166”部分的示例，请确保设备已正确连接，并重启 VS Code。  

![IoT DevKit 示例](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

还可从命令面板打开此示例。 使用 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 打开命令面板，键入“Arduino”，然后找到并选择“Arduino: Examples”。

## <a name="provision-azure-services"></a>预配 Azure 服务

在解决方案窗口中，键入 `Ctrl+P`（macOS：`Cmd+P`）并输入 `task cloud-provision`。

在 VS Code 终端中，交互式命令行将引导你预配所有必需的 Azure 服务：

![云预配任务](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="deploy-the-azure-function"></a>部署 Azure Function

使用 `Ctrl+P`（macOS：`Cmd+P`）运行 `task cloud-deploy` 以部署 Azure Functions 代码。 此过程通常需要 2 到 5 分钟才能完成。

![云部署任务](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Azure Function 成功部署后，使用函数应用名称填写 azure_config.h 文件。 可以导航到 [Azure 门户](https://portal.azure.com/)来查找该文件：

![查找 Azure 函数应用名称](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> 如果 Azure Function 无法正常运行，请检查 [IoT DevKit 常见问题解答中的“Azure Function 的复杂性错误”页面](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function)。

## <a name="build-and-upload-the-device-code"></a>生成并上传设备代码

1. 使用 `Ctrl+P`（macOS：`Cmd+P`）运行 `task config-device-connection`。

2. 终端会询问你是否要使用通过 `task cloud-provision` 步骤检索到的连接字符串。 也可以通过选择“新建...”输入自己的设备连接字符串

3. 终端会提示进入配置模式。 为此，请长按按钮 A，然后按下重置按钮并松开。 屏幕将显示 DevKit ID 和“配置”。

   ![验证并上传 Arduino 草图](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. 在 `task config-device-connection` 完成后，单击 `F1` 加载 VS Code 命令并选择 `Arduino: Upload`，然后，VS Code 将开始验证并上传 Arduino 草图。

   ![验证并上传 Arduino 草图](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

DevKit 将重新启动并开始运行代码。

## <a name="test-the-project"></a>测试项目

应用初始化后，请按照 DevKit 屏幕上的说明进行操作。 默认源语言为中文。

若要选择另一种语言进行翻译，请执行以下操作：

1. 按下按钮 A 进入设置模式。

2. 按下按钮 B 滚动所有支持的源语言。

3. 按下按钮 A 确认所选的源语言。

4. 说话时按住按钮 B，然后释放按钮 B 以启动翻译。

5. 屏幕上将显示英语的已翻译文本。

![滚动以选择语言](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![翻译结果](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

在“翻译结果”屏幕上，可以：

- 按下按钮 A 和 B，滚动并选择源语言。

- 按下按钮 B 开始说话。 释放按钮 B，发送语音并获取翻译文本。

## <a name="how-it-works"></a>工作原理

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Arduino 草图会记录你的语音，然后发送 HTTP 请求来触发 Azure Function。 Azure Function 调用认知服务语音翻译 API 进行翻译。 Azure Function 获取翻译文本后，会向设备发送 C2D （云到设备）消息。 然后翻译将显示在屏幕上。

## <a name="change-device-id"></a>更改设备 ID

在 Azure IoT 中心注册的默认设备 ID 为 **AZ3166**。 要修改设备 ID，请参阅如何[为 DevKit 自定义 IoT 设备 ID](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)。

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅 [IoT DevKit 常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或通过以下渠道联系我们：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [堆栈溢出](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>后续步骤

通过使用 Azure Functions 和认知服务，你已了解如何使用 IoT DevKit 进行翻译。 在这个操作方法中，你学会了如何：

> [!div class="checklist"]
> * 使用 Visual Studio Code 任务自动执行云预配
> * 配置 Azure IoT 设备连接字符串
> * 部署 Azure Function
> * 测试语音消息翻译

继续学习其他教程：

> [!div class="nextstepaction"]
> [将 IoT DevKit AZ3166 连接到 Azure IoT 远程监视解决方案加速器](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)
