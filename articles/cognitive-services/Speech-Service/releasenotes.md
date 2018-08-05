---
title: 认知服务语音 SDK 文档
description: 发行说明 - 最新版本中的内容更改
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: wolfma
ms.openlocfilehash: a28df7faaf98e5cba8ad4afe7aa1a829195d7828
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281484"
---
# <a name="release-notes"></a>发行说明

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>认知服务语音 SDK 0.5.0：2018 年 7 月版本

**新功能**

* 支持 Android 平台（API 23：Android 6.0 Marshmallow 或更高版本）。
  查看 [Android 快速入门](quickstart-java-android.md)。
* 在 Windows 上支持 .NET Standard 2.0。
  查看 [.NET Core 快速入门](quickstart-csharp-dotnetcore-windows.md)。
* 实验：在 Windows 上支持 UWP（版本 1709 或更高版本）
  * 查看 [UWP 快速入门](quickstart-csharp-uwp.md)。
  * 注意：使用语音 SDK 生成的 UWP 应用尚未通过 Windows 应用认证工具包 (WACK)。
* 通过自动重新连接支持识别功能长时间运行。

**功能性更改**

* `StartContinuousRecognitionAsync()` 支持识别功能长时间运行
* 识别结果包含更多字段：到识别文本的音频开始和持续时间（时钟周期数）的偏移量、表示识别状态的附加值（例如 `InitialSilenceTimeout`、`InitialBabbleTimeout`）。
* 支持 AuthorizationToken 用于创建工厂实例。

**重大更改**

* 识别事件：NoMatch 事件类型合并到 Error 事件中。
* C# 中的 SpeechOutputFormat 重命名为 OutputFormat 以与 C++ 保持一致。
* `AudioInputStream` 接口的某些方法的返回类型略有更改：
   * 在 Java 中，`read` 方法现返回 `long` 而不是 `int`。
   * 在 C# 中，`Read` 方法现返回 `uint` 而不是 `int`。
   * 在 C++ 中，`Read` 和 `GetFormat` 方法现返回 `size_t` 而不是 `int`。
* C++：音频输入流的实例现在只能作为 `shared_ptr` 传递。

**Bug 修复**

* 修复了 `RecognizeAsync()` 超时时结果中的错误返回值。
* 删除了 Windows 上媒体基础库的依赖项。 SDK 现使用 Core Audio API。
* 文档修复：添加了一个区域页来描述支持的区域。

**已知问题**

* 适用于 Android 的语音 SDK 不报告用于翻译的语音合成结果。
  此问题将在下一版本中修复。

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>认知服务语音 SDK 0.4.0：2018 年 6 月版本

**功能性更改**

- AudioInputStream

  一种现可将流用作音频源的识别器。 有关详细信息，请参阅相关[操作说明指南](how-to-use-audio-input-streams.md)。

- 详细输出格式

  创建 `SpeechRecognizer` 时，可请求 `Detailed` 或 `Simple` 输出格式。 `DetailedSpeechRecognitionResult` 包含置信度分数、识别的文本、原始词法形式、标准化形式和已屏蔽不当字词的标准化形式。

**重大更改**

- 将 C# 中的 `SpeechRecognitionResult.RecognizedText` 更改为 `SpeechRecognitionResult.Text`。

**Bug 修复**

- 修复关闭期间 USP 层中可能出现的回叫问题。

- 如果识别器使用了音频输入文件，则它在文件句柄上停留的时间将超过必要时间。

- 删除了消息泵和识别器之间的多个死锁。

- 在服务的响应超时后触发 `NoMatch` 结果。

- Windows 上的媒体基础库为延迟加载。 此库仅用于麦克风输入。

- 音频数据的上传速度约限制为原始音频速度的两倍。

- 在 Windows 上，C# .NET 程序集现在为强命名。

- 文档修复：`Region` 是创建识别器所必需的信息。

已添加更多示例，还将持续更新。 有关最新的示例集，请参阅[语音 SDK 示例 GitHub 存储库](https://aka.ms/csspeech/samples)。

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>认知服务语音 SDK 0.2.12733：2018 年 5 月版本

认知服务语音 SDK 的第一个公共预览版本。
