---
title: 发行说明 - 语音服务
titlesuffix: Azure Cognitive Services
description: 请参阅 Azure 语音服务的功能发布、改进、bug 修复和已知问题的运行日志。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 7485ca1e4b1143ed46c9b3bef9ca66af0638b4f8
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53599410"
---
# <a name="release-notes"></a>发行说明

## <a name="speech-sdk-120-2018-december-release"></a>语音 SDK 1.2.0：2018 年 12 月版本

**新功能**

* Python
  * 此版本支持 Python 的 Beta 版本（3.5 及更高版本）。 有关详细信息，请[参阅此处](quickstart-python.md)。
* JavaScript
  * 适用于 JavaScript 的语音 SDK 已开放了源代码。 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js) 上提供了源代码。
  * 我们现在支持 Node.js，可以在[此处](quickstart-js-node.md)找到详细信息。
  * 已删除了对音频会话的长度限制，将自动在后台进行重新连接。
* 连接对象
  * 可以从识别器中访问连接对象。 此对象允许你显式启动服务连接并订阅连接事件和断开连接事件。
    （这在 JavaScript 和 Python 中尚不可用。）
* 支持 Ubuntu 18.04。
* Android
  * 在生成 APK 期间启用了 ProGuard 支持。

**改进**

* 改进了内部线程的使用，减少了线程、锁和互斥的数量。
* 改进了错误报告 / 信息。 在某些情况下，错误消息没有完全传播出去。
* 更新了 JavaScript 中的开发依赖项来使用最新模块。

**Bug 修复**

* 修复了由于 RecognizeAsync 中的类型不匹配导致的内存泄漏。
* 在某些情况下，异常会被泄露。
* 修复了翻译事件参数中的内存泄漏。
* 修复了长时间运行的会话中与重新连接相关的锁定问题。
* 修复了可能会导致失败的翻译缺少最终结果的问题。
* C#：如果在主线程中没有等待异步操作，则可能会在异步任务完成之前释放识别器。
* Java:修复了导致 Java VM 故障的一个问题。
* Objective-C：修复了枚举映射；之前返回 RecognizedIntent 而非 RecognizingIntent。
* JavaScript：在 SpeechConfig 中将默认输出格式设置为“simple”。
* JavaScript：删除了 JavaScript 和其他语言中配置对象中的属性之间的不一致。

**示例**

* 更新并修复了几个示例（例如，翻译的输出语音，等等）。
* 在[示例存储库](https://aka.ms/csspeech/samples)中添加了 Node.js 示例。

## <a name="speech-sdk-110"></a>语音 SDK 1.1.0

**新功能**

* 对 Android x86/x64 的支持。
* 代理支持：在 SpeechConfig 对象中，现在可以调用某个函数来设置代理信息（主机名、端口、用户名和密码）。 此功能在 iOS 上尚不可用。
* 改进了错误代码和消息。 如果识别返回了错误，这在过去会将 `Reason`（在已取消事件中）或 `CancellationDetails`（在识别结果中）设置为 `Error`。 取消的事件现在包含两个附加的成员：`ErrorCode` 和 `ErrorDetails`。 如果服务器随所报告的错误返回了附加的错误信息，则现在将在新成员中提供该信息。

**改进**

* 在识别器配置中添加了附加的验证并添加了附加的错误消息。
* 改进了对音频文件中间的长时间静默的处理。
* NuGet 包：对于 .NET Framework 项目，它阻止使用 AnyCPU 配置进行构建。

**Bug 修复**

* 修复了在识别器中发现的几处异常。 此外，还会捕获异常并将其转换为已取消事件。
* 修复了属性管理中的内存泄漏。
* 修复了音频输入文件可能会导致识别器发生故障的 bug。
* 修复了在会话停止事件后无法检索事件的 bug。
* 修复了线程中的一些争用条件。
* 修复了可能会导致故障的 iOS 兼容性问题。
* 改进了对 Android 麦克风的支持的稳定性。
* 修复了 JavaScript 中的识别器将忽略识别语言的 bug。
* 修复了阻止在 JavaScript 中设置 EndpointId（在某些情况下）的 bug。
* 更改了 JavaScript 中的 AddIntent 中的参数顺序，并添加了缺少的 AddIntent JavaScript 签名。

**示例**

* 在[示例存储库](https://aka.ms/csspeech/samples)中添加了拉取和推送流用法的 C++ 和 C# 示例。

## <a name="speech-sdk-101"></a>语音 SDK 1.0.1

可靠性改进和 bug 修复：

* 修复了处理识别器时由于争用条件而导致的潜在严重错误
* 修复了未设置属性情况下的潜在严重错误。
* 添加了其他错误检查和参数检查。
* Objective-C：修复了 NSString 中名称替代而引起的潜在严重错误。
* Objective-C：调整了 API 的可见性
* JavaScript：针对事件及其有效负载进行了修复。
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
* 试验：在 Windows (64-bit) 和 Linux (Ubuntu 16.04 x64) 上支持 Java 8。
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
* 试验：在 Windows 上支持 UWP（版本 1709 或更高版本）。
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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>认知服务语音 0.2.12733：2018 年 5 月版本

此版本是认知服务语音 SDK 的第一个公共预览版本。
