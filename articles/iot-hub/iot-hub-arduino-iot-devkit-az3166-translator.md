---
title: "使用 Azure 函数和认知服务的 IoT DevKit 翻译 | Microsoft Docs"
description: "使用 IoT DevKit 上的麦克风接收语音消息，并使用 Azure 认知服务将语音消息处理为英文翻译文本。"
services: iot-hub
documentationcenter: 
author: liydu
manager: timlt
tags: 
keywords: 
ms.service: iot-hube
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: liydu
ms.openlocfilehash: 5f74e43d20b5954ae4808f82a34372d1e2190481
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
---
# <a name="use-iot-devkit-az3166-with-azure-function-and-cognitive-services-to-make-a-language-translator"></a>将 IoT DevKit AZ3166 与 Azure 函数和认知服务配合使用生成语言翻译

本文介绍如何使用 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/)将 IoT DevKit 设为语言翻译。 它会录制你的声音并将其翻译为 DevKit 屏幕上显示的英文文本。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是具有多种外设和传感器的集成 Arduino 兼容板。 可以使用[适用于 Arduino 的 Visual Studio Code 扩展](https://aka.ms/arduino)针对其进行开发。 它附带了一个不断增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，指导你构建物联网 (IoT) 解决方案的原型，以利用 Microsoft Azure 服务。

## <a name="what-you-need"></a>所需条件

完成[入门指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)来实现以下目的：

* 将 DevKit 连接到 Wi-Fi
* 准备开发环境

一个有效的 Azure 订阅。 如果没有订阅，可以通过以下两种方法之一进行注册：

* 激活 [30 天免费试用版 Microsoft Azure 帐户](https://azureinfo.microsoft.com/us-freetrial.html)
* 声明你的 [Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)（如果你是 MSDN 或 Visual Studio 订阅者）

## <a name="step-1-open-the-project-folder"></a>步骤 1. 打开项目文件夹

### <a name="a-start-vs-code"></a>A. 启动 VS Code

- 确保 DevKit 未连接到电脑。
- 启动 VS Code
- 将 DevKit 连接到计算机。

VS Code 将自动查找 DevKit，并打开简介页：

![“简介”页](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_start.png)

### <a name="b-open-the-arduino-examples-folder"></a>B. 打开 Arduino 示例文件夹

展开左侧的“ARDUINO 示例”>“MXCHIP AZ3166 的示例”>“AzureIoT”，然后选择“DevKitTranslator”。 它将打开一个新的 VS Code 窗口，其中包含 DEVKITTRANSLATOR 项目文件夹。

![IoT DevKit 示例](media/iot-hub-arduino-iot-devkit-az3166-translator/vscode_examples.png)

如果无意中关闭了窗格，可以重新打开它。 使用 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 打开命令面板，键入“Arduino”，然后找到并选择“Arduino: Examples”。

## <a name="step-2-provision-azure-services"></a>步骤 2. 预配 Azure 服务

在解决方案窗口中，键入 `Ctrl+P`（macOS：`Cmd+P`）并输入 `task cloud-provision`。

在 VS Code 终端中，交互式命令行将引导你预配所有必需的 Azure 服务：

![云预配任务](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-provision.png)

## <a name="step-3-deploy-azure-functions"></a>步骤 3. 部署 Azure Functions

使用 `Ctrl+P`（macOS：`Cmd+P`）运行 `task cloud-deploy` 以部署 Azure Functions 代码。 此过程通常需要 2 到 5 分钟才能完成：

![云部署任务](media/iot-hub-arduino-iot-devkit-az3166-translator/cloud-deploy.png)

Azure 函数成功部署后，使用函数应用名称填写 azure_config.h 文件。 可以导航到 [Azure 门户](https://portal.azure.com/)来查找该文件：

![查找 Azure 函数应用名称](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-function.png)

> [!NOTE]
> 如果 Azure 函数无法正常工作，请查看此[常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function)部分以解决该问题。

## <a name="step-4-build-and-upload-the-device-code"></a>步骤 4. 生成并上传设备代码

1. 使用 `Ctrl+P`（macOS：`Cmd+P`）运行 `task config-device-connection`。

2. 终端会询问你是否要使用通过 `task cloud-provision` 步骤检索到的连接字符串。 也可以通过选择“新建...”输入自己的设备连接字符串

3. 终端会提示进入配置模式。 为此，请长按按钮 A，然后按下重置按钮并松开。 屏幕将显示 DevKit ID 和“配置”。
  ![验证并上传 Arduino 草图](media/iot-hub-arduino-iot-devkit-az3166-translator/config-device-connection.png)

4. 在 `task config-device-connection` 完成后，单击 `F1` 来加载 VS Code 命令并选择 `Arduino: Upload`，然后，VS Code 将开始验证并上传 Arduino 草图：![Arduino 草图的验证和上传](media/iot-hub-arduino-iot-devkit-az3166-translator/arduino-upload.png)

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

- 按下按钮 A 和按钮 B，以滚动并选择源语言。
- 按下按钮 B 讲话，释放该按钮以发送语音并获取翻译文本

## <a name="how-it-works"></a>工作原理

![mini-solution-voice-to-tweet-diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

Arduino 草图会记录你的声音，然后发送 HTTP 请求以触发 Azure Functions。 Azure Functions 会调用认知服务语音翻译 API 进行翻译。 Azure Functions 获取翻译文本后，它将 C2D 消息发送到设备。 然后翻译将显示在屏幕上。

## <a name="change-device-id"></a>更改设备 ID

在 Azure IoT 中心注册的默认设备 ID 为 **AZ3166**。 若要修改此 ID，请遵照[此处的说明](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)。

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅[常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)或通过以下渠道联系我们：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [堆栈溢出](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>后续步骤

现在已使用 Azure 函数和认知服务将 IoT DevKit 设为翻译。 本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Visual Studio Code 任务自动执行云预配
> * 配置 Azure IoT 设备连接字符串
> * 部署 Azure 函数
> * 测试语音消息翻译

继续学习其他教程：

> [!div class="nextstepaction"]
> [将 IoT DevKit AZ3166 连接到 Azure IoT 套件以进行远程监视](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring)