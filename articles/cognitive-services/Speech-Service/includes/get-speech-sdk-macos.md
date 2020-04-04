---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 18a2e5118ab8ab30de5cc4dbf75342cc5275256c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656635"
---
在为 macOS 开发时，有三个语音 SDK 可用。

- 目标 C 语音 SDK 作为 CocoaPod 包在本机上提供
- .NET 语音 SDK 可与**Xamarin.Mac**一起使用，因为它实现了 .NET 标准 2.0
- Python 语音 SDK 可作为 PyPI 模块提供

> [!TIP]
> 有关使用带有 Swift 的目标-C 语音 SDK 的详细信息，请参阅<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">将目标<span class="docon docon-navigate-external x-hidden-focus"></span>C 导入 Swift </a>。

### <a name="system-requirements"></a>系统要求

- macOS 版本 10.13 或更高版本

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        macOS CocoaPod 软件包可供下载，可与<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1（或更高版本<span class="docon docon-navigate-external x-hidden-focus"></span>）</a>集成开发环境 （IDE） 一起下载和使用。 首先<a href="https://aka.ms/csspeech/macosbinary" target="_blank">，下载二进制可可盒<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 在同一目录中提取 Pod 以使其预期用途，创建*Podfile*并将`pod`列为`target`。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xcode" src="https://docs.microsoft.com/media/logos/logo_xcode.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

```
platform :ios, '9.3'
use_frameworks!

target 'MyApp' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.11.0'
end
```

# <a name="xamarinmac"></a>Xamarin.Mac[](#tab/mac-xamarin)

:::row:::
    :::column span="3":::
        Xamarin 公开了完整的 macOS SDK，让 .NET 开发人员能够使用 C# 构建本机 Mac 应用程序。 有关详细信息，请参阅<a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin.Mac <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

# <a name="python"></a>[Python](#tab/mac-python)

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

---

#### <a name="additional-resources"></a>其他资源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS 语音 SDK 快速入门目标 C 源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS 语音 SDK 快速入门 Swift 源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>