---
title: 快速入门：使用音频文件测试模型 - Speech Studio
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将使用 Speech Studio 在音频文件中测试语音识别。
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: v-demjoh
ms.openlocfilehash: 76fb933893b624e9b289ddf51d2a0373b51e58d1
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391530"
---
# <a name="quickstart-test-a-model-using-an-audio-file-in-speech-studio"></a>快速入门：在 Speech Studio 中使用音频文件测试模型

在本快速入门中，你将使用 Speech Studio 将音频文件中的语音转换为文本。 使用 Speech Studio，你可以使用相关文本、带有用户标记的听录内容的音频和你提供的发音指导来测试、比较、改进和部署语音识别模型。

## <a name="prerequisites"></a>先决条件

使用语音门户之前，[按照以下说明创建 Azure 帐户，并订阅语音服务](../how-to-custom-speech.md#set-up-your-azure-account)。 此统一订阅使你可以访问语音到文本、文本到语音、语音翻译和自定义语音门户。

## <a name="download-an-audio-file"></a>下载音频文件

按照以下步骤下载包含语音的音频文件，并将其打包为 zip 文件。

1. 从此链接下载[示例 wav 文件](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)，方法是：右键单击链接，然后选择“将链接另存为”。 单击“保存”以下载 `whatstheweatherlike.wav` 文件。
2. 使用文件资源管理器或带有 zip 工具的终端窗口，创建一个名为 `whatstheweatherlike.zip` 的 zip 文件，其中包含所下载的 `whatstheweatherlike.wav` 文件。 在 Windows 中，你可以打开 Windows 资源管理器，导航到 `Downloads` 文件夹，右键单击 `whatstheweatherliike.wav`，单击“发送到”，单击“压缩的文件夹”，然后按 Enter 以接受默认文件名。

## <a name="create-a-project-in-the-custom-speech-portal"></a>在自定义语音门户中创建项目

按照以下步骤创建一个项目，该项目包含一个音频文件的 zip 文件。

1. 打开 [Speech Studio](https://speech.microsoft.com/)，然后单击“新建项目”。 键入此项目的名称，然后单击“创建”。 项目将出现在“自定义语音列表”中。
2. 单击项目名称。 在“数据”选项卡中，单击“上传数据”。
3. 语音数据类型默认为“仅音频”，因此单击“下一步”。
4. 将新的语音数据集命名为 `MyZipOfAudio`，然后单击“下一步”。
5. 单击“浏览文件...”，导航到 `whatstheweatherlike.zip` 文件，然后单击“打开”。
6. 单击“上传”按钮。 浏览器将你的 zip 文件上传到 Speech Studio，Speech Studio 会处理这些内容。

## <a name="test-a-model"></a>测试模型

Speech Studio 处理完 zip 文件的内容后，可以在播放源音频的同时检查听录内容，以查找错误或遗漏。 请按照以下步骤在浏览器中检查听录内容的质量。

1. 单击“测试”选项卡，然后单击“添加测试”。
2. 在此测试中，我们将检查仅音频数据的质量，因此单击“下一步”以接受此测试类型。
3. 将此测试命名为 `MyModelTest`，然后单击“下一步”。
4. 单击 `MyZipOfAudio` 左侧的单选按钮，然后单击“下一步”。
5. “模型 1”下拉列表默认为最新的识别模型，因此，请单击“创建”。 处理音频数据集的内容后，测试状态将更改为“成功”。
6. 单击“MyModelTest”。 随即出现语音识别的结果。 单击圆圈内的右指三角形即可听到音频，并将你听到的内容与圆圈旁的文本进行比较。

## <a name="download-detailed-results"></a>下载详细报告

你可以下载更详细地介绍听录内容的文件。 该文件包括音频文件中语音的词汇形式，以及包含每个字词的偏移量、持续时间和听录内容可信度详细信息的 JSON 文件。 请遵循以下步骤以查看这些文件。

1. 单击“下载”。
2. 在“下载”对话框中，取消选择“音频”，然后单击“下载”。
3. 解压缩已下载的 zip 文件，并检查解压缩后的文件。

## <a name="next-steps"></a>后续步骤

了解如何通过[训练自定义模型](../how-to-custom-speech-test-and-train.md)来提高语音识别的准确性。