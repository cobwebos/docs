---
title: 发行说明-语音服务
titleSuffix: Azure Cognitive Services
description: 正在运行的语音服务功能版本、改进、bug 修复和已知问题的日志。
services: cognitive-services
author: brianem
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: brianem
ms.custom: seodec18
ms.openlocfilehash: cbe9c7cbd0f402e38d1163050d77b055f89948ba
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219639"
---
# <a name="release-notes"></a>发行说明
## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0： 2020-2 月发行版

**新功能**
 - 添加了 Python 包以支持新3.8 版本的 Python。
 - Red Hat Enterprise Linux （RHEL）/CentOS 8 x64 支持（C++、 C#、Java、Python）。
   > [!NOTE] 
   > 客户必须根据[这些说明](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)配置 OpenSSL。
 - Linux ARM32 支持 Debian 和 Ubuntu。
 - DialogServiceConnector 现在支持 BotFrameworkConfig 上的可选 "机器人 ID" 参数。 此参数允许将多个直接连线语音机器人用于单个 Azure 语音资源。 如果未指定参数，则将使用默认机器人（由 "直接连线语音通道配置" 页确定）。
 - DialogServiceConnector 现在具有一个 SpeechActivityTemplate 属性。 直接 Line Speech 将使用此 JSON 字符串的内容来预填充到达直接语音机器人的所有活动中各种受支持的字段，包括自动生成的活动，以响应语音识别之类的事件。
 - TTS 现在使用订阅密钥进行身份验证，从而减少了创建合成器后第一个合成结果的第一个字节延迟。
 - 更新了适用于19个区域设置的语音识别模型，以获得18.6% （es，es-MX、fr-CA、fr、zh-chs、pt、ja、ko-KR、pt-BR、、zh-chs、nb、nb、fi、ru、pl、pl、CA-ES、zh-chs、、pt、tr、tr、）。 新模型在多个域中提供了重大改进，包括听写、呼叫中心脚本和视频索引方案。

**Bug 修复**
 - 修复了在 JAVA Api 中会话 Transcriber 未正确等待的 bug 
 - 适用于 Xamarin [GitHub 问题](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)的 Android x86 模拟器修补程序
 - 添加缺失（Get |Set） AudioConfig 属性方法
 - 修复无法在连接失败时停止 audioDataStream 的 TTS bug
 - 使用不带区域的终结点将导致会话转换器出现 USP 故障
 - 通用 Windows 应用程序中的 ID 生成现在使用适当的唯一 GUID 算法;它以前和无意中默认为用作存根实现，这种实现通常会在大型交互集上生成冲突。
 
 **示例**
 - 结合使用 Speech SDK 与[unity 麦克风和推送模式流式处理](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)的 Unity 示例

**其他更改**
 - [适用于 Linux 的 OpenSSL 配置文档已更新](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0： 2020-一月发行版

**新功能**

- 多设备对话：将多个设备连接到相同的语音或基于文本的对话，并选择性地转换在它们之间发送的消息。 请参阅[本文](multi-device-conversation.md)中的详细信息。 
- 已为 aar 包添加关键字识别支持，并为 x86 和 x64 风格添加了支持。 
- 目标-C： `SendMessage` 和 `SetMessageProperty` 方法添加到 `Connection` 对象。 请参阅[此处](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)的文档。
- TTS C++ api 现在支持 `std::wstring` 作为合成文本输入，无需将 wstring 转换为字符串，然后再将其传递给 SDK。 详见[此处](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)。 
- C#：[语言 ID](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp)和[源语言配置](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp)现在可用。
- JavaScript：向 `Connection` 对象添加了一项功能，该功能可通过自定义消息作为回调 `receivedServiceMessage`从语音服务传递。
- JavaScript：添加了对 `FromHost API` 的支持，便于与本地容器和主权云一起使用。 请参阅[此处](speech-container-howto.md)的文档。
- JavaScript：我们现在通过[orgads](https://github.com/orgads)的内容来服从 `NODE_TLS_REJECT_UNAUTHORIZED`。 详见[此处](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)。


**重大更改**

- `OpenSSL` 已更新到版本 1.1.1 b 并静态链接到适用于 Linux 的语音 SDK 核心库。 如果收件箱 `OpenSSL` 未安装到系统中的 `/usr/lib/ssl` 目录，这可能会造成中断。 请查看语音 SDK 文档下的[文档](how-to-configure-openssl-linux.md)以解决此问题。
- 我们已将从 `int` `WordLevelTimingResult.Offset` 返回的C#数据类型更改为 `long`，以便在语音数据超过2分钟时允许访问 `WordLevelTimingResults`。
- `PushAudioInputStream` 和 `PullAudioInputStream` 现在会根据 `AudioStreamFormat`，将 wav 标头信息发送到语音服务，并根据需要在创建时指定。 客户现在必须使用[支持的音频输入格式](how-to-use-audio-input-streams.md)。 任何其他格式将获得欠最佳的识别结果，也可能会导致其他问题。 


**Bug 修复**

- 请参阅上述重大更改下的 `OpenSSL` 更新。 修复了 Linux 和 Java 中的间歇性崩溃和性能问题（高负载下的锁争用）。 
- Java：对高并发方案中的对象关闭进行了改进。
- 重构了 NuGet 包。 我们删除了 `Microsoft.CognitiveServices.Speech.core.dll` 和 `Microsoft.CognitiveServices.Speech.extension.kws.dll` lib 文件夹下的三个副本，使 NuGet 包更小、更快速地下载，并添加了编译某些C++本机应用所需的标头。
- 修复了[本文](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)中的快速入门示例。 这些都是在 Linux、MacOS、Windows 上不显示 "未找到麦克风" 异常的情况下退出。
- 修复了长语音识别的 SDK 崩溃，其中包含了某些代码路径，如[本示例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)中所示。
- 修复了 Azure Web 应用环境中的 SDK 部署错误，以解决[此客户问题](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)。
- 修复了在使用多个 `<voice>` 标记或 `<audio>` 标记来解决[此客户问题](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)时出现 TTS 错误。 
- 修复了从挂起中恢复 SDK 时出现的 TTS 401 错误。
- JavaScript：修复了音频数据的循环导入，这归功于[euirim](https://github.com/euirim)的贡献。 
- JavaScript：添加了对设置服务属性的支持，如1.7 中添加的。
- JavaScript：修复了一个问题，即连接错误可能导致连续失败的 websocket 重新连接尝试。


**示例**

- [此处](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)添加了适用于 Android 的关键字识别示例。
- [此处](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)为服务器方案添加了 TTS 示例。
- 为和C# C++ [此处](quickstarts/multi-device-conversation.md)增加了多设备对话快速入门。


**其他更改**

- Android 上优化的 SDK 核心库大小。
- 1\.9.0 和后中的 SDK 支持会话 Transcriber 的语音签名版本字段中的 `int` 和 `string` 类型。

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0： 2019-11 月发行版

**新功能**

- 添加了 `FromHost()` API，以便于本地容器和主权云轻松使用。
- 为语音识别添加了自动源语言检测（在 Java C++和中）
- 添加了 `SourceLanguageConfig` 对象用于语音识别，用于指定预期的源语言（在 Java C++和中）
- 通过 NuGet 和 Unity 包增加了对 Windows （UWP）、Android 和 iOS 的 `KeywordRecognizer` 支持
- 添加了用于执行会话的远程会话 Java API 异步批处理。

**重大更改**

- 在命名空间 `Microsoft.CognitiveServices.Speech.Transcription`下移动的会话 Transcriber 功能。
- 会话 Transcriber 方法的一部分被移动到新 `Conversation` 类中。
- 丢弃对32位（ARMv7 和 x86） iOS 的支持

**Bug 修复**

- 如果在不使用有效语音服务订阅密钥的情况下使用本地 `KeywordRecognizer`，则修复故障

**示例**

- 适用于 `KeywordRecognizer` 的 Xamarin 示例
- `KeywordRecognizer` 的 Unity 示例
- C++用于自动源语言检测的和 Java 示例。

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0： 2019-九月发行版

**新功能**

- 为通用 Windows 平台（UWP）、Android 和 iOS 上的 Xamarin 添加了 beta 支持
- 添加了对 Unity 的 iOS 支持
- 添加了 `Compressed` 输入支持 Android、iOS 和 Linux 上的 ALaw、Mulaw、FLAC
- 在 `Connection` 类中添加了 `SendMessageAsync`，以便将消息发送到服务
- 在用于设置消息属性 `Connection` 类中添加了 `SetMessageProperty`
- TTS 为 Java 添加了绑定（Jre 和 Android）、Python、Swift 和目标-C
- TTS 添加了对 macOS、iOS 和 Android 的播放支持。
- 为 TTS 添加了 "单词边界" 信息。

**Bug 修复**

- 修复了适用于 Android 的 Unity 2019 上的 IL2CPP 生成问题
- 修复了未正确处理的 wav 文件输入中格式不正确的标头的问题
- 修复了某些连接属性中的 Uuid 不唯一的问题
- 修复了有关 Swift 绑定中的可为 null 说明符的一些警告（可能需要较小的代码更改）
- 修复了一个 bug，该 bug 导致在网络负载下意外关闭 websocket 连接
- 修复了 Android 上的一个问题，有时会导致 `DialogServiceConnector` 使用重复的印记 Id
- 改进了跨多个交互的连接稳定性，并在发生故障时报告故障（通过 `Canceled` 事件） `DialogServiceConnector`
- `DialogServiceConnector` 会话启动现在会正确地提供事件，包括在活动 `StartKeywordRecognitionAsync()` 调用 `ListenOnceAsync()` 时
- 解决了与接收的 `DialogServiceConnector` 活动相关联的故障

**示例**

- Xamarin 快速入门
- 通过 Linux ARM64 信息更新了 CPP 快速入门
- 通过 iOS 信息更新了 Unity 快速入门

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0： 2019-6 月发行版

**示例**

- UWP 和 Unity 上的文本到语音的快速入门示例
- IOS 上的 Swift 快速入门示例
- 用于语音 & 意向识别和翻译的 Unity 示例
- `DialogServiceConnector` 更新的快速入门示例

**改进 / 更改**

- 对话框命名空间：
  - `SpeechBotConnector` 已重名为 `DialogServiceConnector`
  - `BotConfig` 已重名为 `DialogServiceConfig`
  - `BotConfig::FromChannelSecret()` 已重新映射到 `DialogServiceConfig::FromBotSecret()`
  - 重命名后继续支持所有现有的直接连线语音客户端
- 更新 TTS REST 适配器以支持代理，永久连接
- 如果传递了无效的区域，则改进错误消息
- Swift/目标-C：
  - 改进了错误报告：可能导致错误的方法现在存在于两个版本中：一个用于公开用于错误处理的 `NSError` 对象，另一个则引发异常。 前者向 Swift 公开。 此更改需要适应现有的 Swift 代码。
  - 改进事件处理

**Bug 修复**

- 针对 TTS 的修复：在何处 `SpeakTextAsync` 以后返回，无需等到音频完成呈现
- 修复中C#的封送字符串以启用完整语言支持
- 修复 .NET core 应用问题，以在示例中加载包含 net461 目标框架的核心库
- 解决在示例中将本机库部署到输出文件夹的偶尔问题
- 修补 web socket 可靠地关闭
- 在 Linux 上的负载很重的情况下打开连接时修复可能的故障
- 修复框架捆绑包中缺少的元数据 macOS
- 解决 Windows `pip install --user` 的问题

## <a name="speech-sdk-151"></a>Speech SDK 1.5。1

这是一个 bug 修复版本，仅影响本机/托管的 SDK。 它不会影响 SDK 的 JavaScript 版本。

**Bug 修复**

- 解决与会话脚本一起使用时的 FromSubscription。
- 修复关键字发现中的 bug 以获取语音助手。

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0 _： 2019-可能发布

**新功能**

- 关键字发现（KWS）现在适用于 Windows 和 Linux。 KWS 功能可能适用于任何麦克风类型，但官方 KWS 支持目前仅限于在 Azure Kinect 深色硬件或语音设备 SDK 中找到的麦克风阵列。
- 短语提示功能可通过 SDK 获得。 有关详细信息，请参阅[此文](how-to-phrase-lists.md)。
- 会话脚本功能可通过 SDK 获得。 参阅[此处](conversation-transcription-service.md)。
- 添加对使用语音助手的语音助手的支持。

**示例**

- 添加了 SDK 支持的新功能或新服务的示例。

**改进 / 更改**

- 添加了各种识别器属性以调整服务行为或服务结果（如屏蔽猥亵和其他）。
- 你现在可以通过标准配置属性配置识别器，即使 `FromEndpoint`创建了识别器。
- 目标-C： `OutputFormat` 属性已添加到 `SPXSpeechConfiguration`中。
- SDK 现在支持 Debian 9 作为 Linux 分发版。

**Bug 修复**

- 修复了在文本到语音转换中，发言人资源销毁过早的问题。

## <a name="speech-sdk-142"></a>Speech SDK 1.4。2

这是一个 bug 修复版本，仅影响本机/托管的 SDK。 它不会影响 SDK 的 JavaScript 版本。

## <a name="speech-sdk-141"></a>Speech SDK 1.4。1

这是一个仅限 JavaScript 的版本。 未增加任何功能。 进行了以下修复：

- 阻止 web 包加载 https 代理。

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0： 2019-4 月发行版

**新功能**

- SDK 现在支持使用文本到语音服务作为 beta 版本。 它在C++和C#的 Windows 和 Linux 桌面上都受支持。 有关详细信息，请查看[文本到语音转换概述](text-to-speech.md#get-started)。
- SDK 现在支持 MP3 和 Opus/OGG 音频文件作为流输入文件。 此功能仅适用于C++和C#中的 Linux，当前为 beta 版（更多[详细信息）](how-to-use-codec-compressed-audio-input-streams.md)。
- Speech SDK for Java、.NET core C++和目标-C 已获得 macOS 支持。 针对 macOS 的目标-C 支持当前为 beta 版本。
- iOS：适用于 iOS 的语音 SDK （目标-C）现在也作为 CocoaPod 发布。
- JavaScript：支持非默认麦克风作为输入设备。
- JavaScript：针对 node.js 的代理支持。

**示例**

- 添加了在 macOS 上将语音C++ SDK 与和结合使用的示例。
- 演示如何添加文本到语音服务的示例。

**改进 / 更改**

- Python：现在，通过 `properties` 属性公开了识别结果的附加属性。
- 若要获得更多的开发和调试支持，可将 SDK 日志记录和诊断信息重定向到日志文件中（更多详细[信息）。](how-to-use-logging.md)
- JavaScript：提高音频处理性能。

**Bug 修复**

- Mac/iOS：在无法建立到语音服务的连接时，导致长时间等待的 bug 已修复。
- Python：改进 Python 回调中的参数的错误处理。
- JavaScript：修复了 RequestSession 上语音的错误状态报告。

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1： 2019-2 月刷新

这是一个 bug 修复版本，仅影响本机/托管的 SDK。 它不会影响 SDK 的 JavaScript 版本。

**Bug 修复**

- 修复了使用麦克风输入时的内存泄漏。 基于流或文件输入不受影响。

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0： 2019-2 月发行版

**新功能**

- 语音 SDK 支持通过 `AudioConfig` 类选择输入麦克风。 这允许你从非默认麦克风将音频数据流式传输到语音服务。 有关详细信息，请参阅说明[音频输入设备选择](how-to-select-audio-input-devices.md)的文档。 此功能尚不能通过 JavaScript 提供。
- 语音 SDK 目前在 beta 版本中支持 Unity。 通过[GitHub 示例存储库](https://aka.ms/csspeech/samples)中的问题部分提供反馈。 此版本支持在 Windows x86 和 x64（桌面或通用 Windows 平台应用程序）以及 Android（ARM32/64，x86）上使用 Unity。 [Unity 快速入门](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)中提供了更多信息。
- 不再需要文件 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` （在以前的版本中随附）。 此功能现已集成到 core SDK。

**示例**

[示例存储库](https://aka.ms/csspeech/samples)中提供了以下新内容：

- `AudioConfig.FromMicrophoneInput`的其他示例。
- 有关意向识别和翻译的更多 Python 示例。
- 在 iOS 中使用 `Connection` 对象的其他示例。
- 有关具有音频输出的翻译的更多 Java 示例。
- 有关使用[批量听录 REST API](batch-transcription.md) 的新示例。

**改进 / 更改**

- Python
  - 提高了 `SpeechConfig`中的参数验证和错误消息。
  - 添加对 `Connection` 对象的支持。
  - 支持 Windows 上的 32 位 Python (x86)。
  - 适用于 Python 的语音 SDK 已完成 beta 版本。
- iOS
  - SDK 现在是基于 iOS SDK 版本 12.1 构建的。
  - SDK 现在支持 iOS 版本 9.2 及更高版本。
  - 改进了参考文档并修复了多个属性名称。
- JavaScript
  - 添加对 `Connection` 对象的支持。
  - 添加了捆绑的 JavaScript 的类型定义文件
  - 首次支持并实现了短语提示。
  - 随服务 JSON 返回属性集合以用于识别
- Windows DLL 现在包含一个版本资源。
- 如果创建了一个识别器 `FromEndpoint` 可以直接将参数添加到终结点 URL。 使用 `FromEndpoint` 无法通过标准配置属性配置识别器。

**Bug 修复**

- 过去无法正确处理空的代理用户名和代理密码。 在此版本中，如果将代理用户名和代理密码设置为空字符串，则在连接到代理时不会提交它们。
- 对于某些语言&nbsp;/ 环境，由 SDK 创建的 SessionId 并非总是真正随机的。 添加了随机生成器初始化，以解决此问题。
- 改进了对授权令牌的处理。 如果要使用授权令牌，请在 `SpeechConfig` 中指定，并将订阅密钥留空。 然后，像往常一样创建识别器。
- 在某些情况下，`Connection` 对象未正确发布。 现在已修复此问题。
- JavaScript 示例已修复，在 Safari 上也支持用于翻译合成的音频输出。

## <a name="speech-sdk-121"></a>语音 SDK 1.2.1

这是一个仅限 JavaScript 的版本。 未增加任何功能。 进行了以下修复：

- 在 turn.end 处触发流结束，在 speech.end 处不触发。
- 修复了音频泵中在当前发送失败时不安排下一次发送的 bug。
- 修复了使用身份验证令牌进行的连续识别。
- 对不同识别器 / 终结点的 bug 修复。
- 文档改进。

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0： 2018-12 月发行版

**新功能**

- Python
  - 此版本支持 Python 的 Beta 版本（3.5 及更高版本）。 有关详细信息，请参阅此处] （快速入门-python.md）。
- JavaScript
  - 适用于 JavaScript 的语音 SDK 已开放了源代码。 [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js) 上提供了源代码。
  - 我们现在支持 Node.js，可以在[此处](quickstart-js-node.md)找到详细信息。
  - 已删除了对音频会话的长度限制，将自动在后台进行重新连接。
- （属于`Connection` 对象）的父级。
  - 在 `Recognizer`中，可以访问 `Connection` 对象。 此对象允许你显式启动服务连接并订阅连接事件和断开连接事件。
    （此功能尚不适用于 JavaScript 和 Python。）
- 支持 Ubuntu 18.04。
- Android
  - 在生成 APK 期间启用了 ProGuard 支持。

**改进**

- 改进了内部线程的使用，减少了线程、锁和互斥的数量。
- 改进了错误报告 / 信息。 在某些情况下，错误消息尚未全部传播出去。
- 更新了 JavaScript 中的开发依赖项来使用最新模块。

**Bug 修复**

- 修复了内存泄漏，因为 `RecognizeAsync`中存在类型不匹配的问题。
- 在某些情况下，异常会被泄露。
- 修复了翻译事件参数中的内存泄漏。
- 修复了长时间运行的会话中与重新连接相关的锁定问题。
- 修复了可能导致转换失败的最终结果的问题。
- C#：如果在主线程中未等待 `async` 操作，则可能会在异步任务完成之前释放识别器。
- Java：修复了导致 Java VM 崩溃的问题。
- 目标-C：固定枚举映射;返回 RecognizedIntent，而不是 `RecognizingIntent`。
- JavaScript：在 `SpeechConfig`中将默认输出格式设置为 "simple"。
- JavaScript：删除 JavaScript 和其他语言中的配置对象上的属性之间的不一致。

**示例**

- 更新并修复了多个示例（例如，用于转换的输出声音，等等）。
- 在[示例存储库](https://aka.ms/csspeech/samples)中添加了 Node.js 示例。

## <a name="speech-sdk-110"></a>语音 SDK 1.1.0

**新功能**

- 对 Android x86/x64 的支持。
- 代理支持：在 `SpeechConfig` 对象中，你现在可以调用函数来设置代理信息（主机名、端口、用户名和密码）。 此功能在 iOS 上尚不可用。
- 改进了错误代码和消息。 如果识别返回了错误，这在过去会将 `Reason`（在已取消事件中）或 `CancellationDetails`（在识别结果中）设置为 `Error`。 取消的事件现在包含两个附加的成员：`ErrorCode` 和 `ErrorDetails`。 如果服务器随所报告的错误返回了附加的错误信息，则现在将在新成员中提供该信息。

**改进**

- 在识别器配置中添加了附加的验证并添加了附加的错误消息。
- 改进了对音频文件中间的长时间静默的处理。
- NuGet 包：对于 .NET Framework 项目，它阻止使用 AnyCPU 配置进行构建。

**Bug 修复**

- 修复了在识别器中发现的几处异常。 此外，还会捕获异常并将其转换为 `Canceled` 事件。
- 修复了属性管理中的内存泄漏。
- 修复了音频输入文件可能会导致识别器发生故障的 bug。
- 修复了在会话停止事件后无法检索事件的 bug。
- 修复了线程中的一些争用条件。
- 修复了可能会导致故障的 iOS 兼容性问题。
- 改进了对 Android 麦克风的支持的稳定性。
- 修复了 JavaScript 中的识别器将忽略识别语言的 bug。
- 修复了在 JavaScript 中阻止设置 `EndpointId` （某些情况下）的错误。
- JavaScript 中的 AddIntent 更改了参数顺序，并添加了缺少的 `AddIntent` JavaScript 签名。

**示例**

- 在C++ C# [示例存储库](https://aka.ms/csspeech/samples)中添加了请求和推送流使用的示例。

## <a name="speech-sdk-101"></a>语音 SDK 1.0.1

可靠性改进和 bug 修复：

- 修复了处理识别器时由于争用条件而导致的潜在严重错误
- 修复了未设置属性情况下的潜在严重错误。
- 添加了其他错误检查和参数检查。
- Objective-C：修复了 NSString 中名称替代而引起的潜在严重错误。
- Objective-C：调整了 API 的可见性
- JavaScript：针对事件及其有效负载进行了修复。
- 文档改进。

在[示例存储库](https://aka.ms/csspeech/samples)中已添加了适用于 JavaScript 的新示例。

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>认知服务语音 SDK 1.0.0：2018 年 9 月版本

**新功能**

- 支持 iOS 中的 Objective-C。 请查看[适用于 iOS 的 Objective-C 快速入门](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)。
- 支持浏览器中的 JavaScript。 请查看 [JavaScript 快速入门](quickstart-js-browser.md)。

**重大更改**

- 在此版本中，引入了大量的重大更改。
  有关详细信息，请查看[此页](https://aka.ms/csspeech/breakingchanges_1_0_0)。

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>认知服务语音 SDK 0.6.0：2018 年 8 月版本

**新功能**

- 使用语音 SDK 生成的 UWP 应用现在可以通过 Windows 应用认证工具包 (WACK)。
  请查看 [UWP 快速入门](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)。
- 在 Linux (Ubuntu 16.04 x64) 上支持 .NET Standard 2.0。
- 实验性：在 Windows (64-bit) 和 Linux (Ubuntu 16.04 x64) 上支持 Java 8。
  请查看 [Java 运行时环境快速入门](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)。

**功能性更改**

- 公开了关于连接错误的更多错误详细信息。

**重大更改**

- 在 Java (Android) 中，`SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 函数不再需要 path 参数。 现在，在所有受支持的平台上都会自动检测路径。
- 在 Java 和 C# 中，属性 `EndpointUrl` 的 get 访问器已被删除。

**Bug 修复**

- 在 Java 中，目前在翻译识别器上实现了音频合成结果。
- 修复了一个 bug，该 bug 可能会导致非活动线程和更多的已打开且未使用的套接字。
- 修复了一个问题，该问题导致在传输过程中无法终止长时间运行的识别。
- 修复了识别器关闭过程中的一个争用条件。

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>认知服务语音 SDK 0.5.0：2018 年 7 月版本

**新功能**

- 支持 Android 平台（API 23：Android 6.0 Marshmallow 或更高版本）。 查看 [Android 快速入门](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)。
- 在 Windows 上支持 .NET Standard 2.0。 查看 [.NET Core 快速入门](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)。
- 实验：在 Windows 上支持 UWP（版本 1709 或更高版本）。
  - 请查看 [UWP 快速入门](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)。
  - 注意：使用语音 SDK 生成的 UWP 应用尚未通过 Windows 应用认证工具包 (WACK)。
- 通过自动重新连接支持识别功能长时间运行。

**功能性更改**

- `StartContinuousRecognitionAsync()` 支持识别功能长时间运行。
- 识别结果包含更多字段。 这些字段是识别文本的音频开始和持续时间（时钟周期数）的偏移量和表示识别状态的其他值（例如 `InitialSilenceTimeout`、`InitialBabbleTimeout`）。
- 支持 AuthorizationToken 用于创建工厂实例。

**重大更改**

- 识别事件： `NoMatch` 事件类型已合并到 `Error` 事件。
- 中C#的 SpeechOutputFormat 已重命名为 `OutputFormat` 以保持C++一致。
- `AudioInputStream` 接口的某些方法的返回类型略有更改：
  - 在 Java 中，`read` 方法现返回 `long` 而不是 `int`。
  - 在 C# 中，`Read` 方法现返回 `uint` 而不是 `int`。
  - 在 C++ 中，`Read` 和 `GetFormat` 方法现返回 `size_t` 而不是 `int`。
- C++：音频输入流的实例现在只能作为 `shared_ptr` 传递。

**Bug 修复**

- 修复了 `RecognizeAsync()` 超时时结果中的错误返回值。
- 删除了 Windows 上媒体基础库的依赖项。 SDK 现在使用 Core Audio API。
- 文档修复：添加了一个[区域](regions.md)页来描述支持的区域。

**已知问题**

- 适用于 Android 的语音 SDK 不报告用于翻译的语音合成结果。 此问题将在下一版本中修复。

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>认知服务语音 SDK 0.4.0：2018 年 6 月版本

**功能性更改**

- AudioInputStream

  一种现可将流用作音频源的识别器。 有关详细信息，请参阅相关[操作说明指南](how-to-use-audio-input-streams.md)。

- 详细输出格式

  创建 `SpeechRecognizer` 时，可请求 `Detailed` 或 `Simple` 输出格式。 `DetailedSpeechRecognitionResult` 包含置信度分数、识别的文本、原始词法形式、标准化形式和已屏蔽不当字词的标准化形式。

**重大更改**

- 将 C# 中的 `SpeechRecognitionResult.Text` 更改为 `SpeechRecognitionResult.RecognizedText`。

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
