---
title: 创建带 Azure Functions，认知服务，IoT DevKit 的翻译人员
description: 使用 IoT DevKit 上的麦克风接收语音消息，并使用 Azure 认知服务将其处理为英文翻译文本
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.openlocfilehash: 8e840a1ae7161ea3e7b370889a1f0fb648ca120e
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953359"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>将 IoT DevKit AZ3166 与 Azure Functions 和认知服务配合使用生成语言翻译

本文介绍如何使用 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/)将 IoT DevKit 设为语言翻译。 它会录制你的声音并将其翻译为 DevKit 屏幕上显示的英文文本。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是具有多种外设和传感器的集成 Arduino 兼容板。 可以使用 Visual Studio Code 中的 [Azure IoT Device Workbench](https://aka.ms/iot-workbench) 或 [Azure IoT 工具](https://aka.ms/azure-iot-tools)扩展包进行开发。 [项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)包含有助于确定 IoT 解决方案原型的示例应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本教程中的步骤，请先执行以下任务：

* 遵循[将 IoT DevKit AZ3166 连接到云中的 Azure IoT 中心](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)中的步骤准备好 DevKit。

## <a name="create-azure-cognitive-service"></a>创建 Azure 认知服务

1. 在 Azure 门户中，点击“创建资源”，然后搜索“语音”。 填写窗体，创建语音服务。
  ![语音服务](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. 转到刚才创建的语音服务，单击“Keys”部分进行复制，记下访问它的 DevKit 的 Key1。
  ![复制密钥](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>打开示例项目

1. 确保 IoT DevKit **未连接**到计算机。 先启动 VS Code，然后将 DevKit 连接到计算机。

1. 单击 "`F1`" 打开命令面板，键入并选择 " **Azure IoT 设备工作台：打开示例 ...** "。然后选择 " **IoT DevKit** as 板"。

1. 在 IoT Workbench 的“示例”页中，找到 DevKit Translator 并单击“打开示例”。 然后选择用于下载示例代码的默认路径。
  ![打开示例](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>配合使用语音服务和 Azure Functions

1. 在 VS Code 中，单击 "`F1`，键入并选择" **Azure IoT 设备工作台：预配 Azure 服务 ...** "。![预配 Azure 服务](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. 按照以下步骤完成 Azure IoT 中心和 Azure Functions 的预配。
   ![预配步骤](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   记录创建的 Azure IoT 中心设备的名称。

1. 打开 `Functions\DevKitTranslatorFunction.cs` 并使用记录的设备名称和语音服务密钥更新以下代码行。
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. 单击 "`F1`"，键入并选择 " **Azure IoT 设备工作台：部署到 azure ...** "。如果 VS Code 要求确认重新部署，则单击 **"是"** 。
   ![部署警告](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. 请确保部署成功。
   ![部署成功](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. 在 Azure 门户中，转到 Function App 部分，找到刚刚创建的 Azure 函数应用。 单击 `devkit_translator`，然后单击“</> 获取函数 URL”以复制 URL。
   ![复制函数 URL](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. 将 URL 粘贴到 `azure_config.h` 文件。
   ![Azure 配置](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > 如果函数应用无法正常工作，请查看此[常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function)部分以解决该问题。

## <a name="build-and-upload-device-code"></a>生成并上传设备代码

1. 按以下方式，将 DevKit 切换为配置模式：
   * 按住按钮 A。
   * 按下然后松开“重置”按钮。

   屏幕将显示 DevKit ID 和“配置”。

   ![DevKit 配置模式](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. 单击 "`F1`"，键入并选择 " **Azure IoT 设备工作台：配置设备设置 ..."> Config 设备连接字符串**。 选择“选择 IoT 中心设备连接字符串”，将其配置到 DevKit。
   ![配置连接字符串](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. 操作成功后，将看到通知。
   ![成功配置连接字符串](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. 再次单击 "`F1`"，键入并选择 " **Azure IoT 设备工作台：上传设备代码**"。 它启动编译，并将代码上传到 DevKit。
   ![设备上传](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

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

IoT DevKit 会记录你的声音，然后发送 HTTP 请求以触发 Azure Functions。 Azure Functions 会调用认知服务语音翻译 API 进行翻译。 Azure Functions 获取翻译文本后，它将 C2D 消息发送到设备。 然后翻译将显示在屏幕上。

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅 [IoT DevKit 常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或通过以下渠道联系我们：

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
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
