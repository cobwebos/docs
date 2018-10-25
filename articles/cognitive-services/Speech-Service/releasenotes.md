---
title: 认知服务语音 SDK 文档 | Microsoft Docs
description: 发行说明 - 最新版本中的内容更改
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 3a9da3b0f2165dadd99691c6dc9e41cbf3d48620
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339995"
---
# <a name="release-notes"></a>发行说明

## <a name="cognitive-services-speech-sdk-101"></a>认知服务语音 SDK 1.0.1

可靠性改进和 bug 修复：

* 修复处理识别器时由于争用条件而导致的潜在灾难性错误
* 修复未设置属性情况下的潜在灾难性错误。
* 其他错误和参数检查。
* Objective-C：修复 NSString 中名称替代而引起的潜在灾难性错误。
* Objectice-C：调整 API 的可见性
* JavaScript：关于事件及其有效负载的修复。
* 文档改进。

在[示例存储库](https://aka.ms/csspeech/samples)中已添加了适用于 JavaScript 的新示例。

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>认知服务语音 SDK 1.0.0：2018 年 9 月版本

**新功能**

* 支持 iOS 中的 Objective-C。 请查看[适用于 iOS 的 Objective-C 快速入门](quickstart-objectivec-ios.md)。
* 支持浏览器中的 JavaScript。 请查看 [JavaScript 快速入门](quickstart-js-browser.md)。

**重大更改**

* 该版本中推出了大量重大更改。
  有关详细信息，请查看[此页](https://aka.ms/csspeech/breakingchanges_1_0_0)。

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>认知服务语音 SDK 0.6.0：2018 年 8 月版本

**新功能**

* 使用语音 SDK 生成的 UWP 应用现在可以通过 Windows 应用认证工具包 (WACK)。
  请查看 [UWP 快速入门](quickstart-csharp-uwp.md)。
* 在 Linux (Ubuntu 16.04 x64) 上支持 .NET Standard 2.0。
* 实验性：在 Windows (64-bit) 和 Linux (Ubuntu 16.04 x64) 上支持 Java 8。
  请查看 [Java 运行时环境快速入门](quickstart-java-jre.md)。

**功能性更改**

* 公开了关于连接错误的更多错误详细信息。

**重大更改**

* 在 Java (Android) 中，`SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 函数不再需要 path 参数。 现在，在所有受支持的平台上都会自动检测路径。
* 在 Java 和 C# 中，属性 `EndpointUrl` 的 get 访问器已被删除。

**Bug 修复**

* 在 Java 中，目前在翻译识别器上实现了音频合成结果。
* 修复了一个 bug，该 bug 可能会导致非活动线程和更多的已打开且未使用的套接字。
* 修复了一个问题，该问题导致在传输过程中无法终止长时间运行的识别。
* 修复了识别器关闭过程中的一个争用条件。

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>认知服务语音 SDK 0.5.0：2018 年 7 月版本

**新功能**

* 支持 Android 平台（API 23：Android 6.0 Marshmallow 或更高版本）。 查看 [Android 快速入门](quickstart-java-android.md)。
* 在 Windows 上支持 .NET Standard 2.0。 查看 [.NET Core 快速入门](quickstart-csharp-dotnetcore-windows.md)。
* 实验：在 Windows 上支持 UWP（版本 1709 或更高版本）。
  * 请查看 [UWP 快速入门](quickstart-csharp-uwp.md)。
  * 注意：使用语音 SDK 生成的 UWP 应用尚未通过 Windows 应用认证工具包 (WACK)。
* 通过自动重新连接支持识别功能长时间运行。

**功能性更改**

* `StartContinuousRecognitionAsync()` 支持识别功能长时间运行。
* 识别结果包含更多字段。 这些字段是识别文本的音频开始和持续时间（时钟周期数）的偏移量和表示识别状态的其他值（例如 `InitialSilenceTimeout`、`InitialBabbleTimeout`）。
* 支持 AuthorizationToken 用于创建工厂实例。

**重大更改**

* 识别事件：NoMatch 事件类型已合并到 Error 事件中。
* C# 中的 SpeechOutputFormat 已重命名为 OutputFormat 以与 C++ 保持一致。
* `AudioInputStream` 接口的某些方法的返回类型略有更改：
   * 在 Java 中，`read` 方法现返回 `long` 而不是 `int`。
   * 在 C# 中，`Read` 方法现返回 `uint` 而不是 `int`。
   * 在 C++ 中，`Read` 和 `GetFormat` 方法现返回 `size_t` 而不是 `int`。
* C++：音频输入流的实例现在只能作为 `shared_ptr` 传递。

**Bug 修复**

* 修复了 `RecognizeAsync()` 超时时结果中的错误返回值。
* 删除了 Windows 上媒体基础库的依赖项。 SDK 现在使用 Core Audio API。
* 文档修复：添加了一个[区域](regions.md)页来描述支持的区域。

**已知问题**

* 适用于 Android 的语音 SDK 不报告用于翻译的语音合成结果。 此问题将在下一版本中修复。

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>认知服务语音 SDK 0.4.0：2018 年 6 月版本

**功能性更改**

- AudioInputStream

  一种现可将流用作音频源的识别器。 有关详细信息，请参阅相关[操作说明指南](how-to-use-audio-input-streams.md)。

- 详细输出格式

  创建 `SpeechRecognizer` 时，可请求 `Detailed` 或 `Simple` 输出格式。 `DetailedSpeechRecognitionResult` 包含置信度分数、识别的文本、原始词法形式、标准化形式和已屏蔽不当字词的标准化形式。

**重大更改**

- 将 C# 中的 `SpeechRecognitionResult.RecognizedText` 更改为 `SpeechRecognitionResult.Text`。

**Bug 修复**

- 修复了关闭期间 USP 层中可能出现的回叫问题。

- 如果识别器使用了音频输入文件，则它在文件句柄上停留的时间将超过必要时间。

- 删除了消息泵和识别器之间的多个死锁。

- 在服务的响应超时后触发 `NoMatch` 结果。

- Windows 上的媒体基础库为延迟加载。 此库仅用于麦克风输入。

- 音频数据的上传速度约限制为原始音频速度的两倍。

- 在 Windows 上，C# .NET 程序集现在为强命名。

- 文档修复：`Region` 是创建识别器所必需的信息。

已添加更多示例，还将持续更新。 有关最新的示例集，请参阅[语音 SDK 示例 GitHub 存储库](https://aka.ms/csspeech/samples)。

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>认知服务语音 SDK 0.2.12733：2018 年 5 月版本

此版本是认知服务语音 SDK 的第一个公共预览版本。
