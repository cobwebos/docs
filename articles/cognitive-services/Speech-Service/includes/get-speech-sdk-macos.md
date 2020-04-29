---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 57de7f1e7c37fec66cda666d3f144e52849a026f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399968"
---
针对 macOS 进行开发时，有三个可用的语音 Sdk。

- 目标为 C 的语音 SDK 以 CocoaPod 包的形式本机提供
- .NET Speech SDK 可与**Xamarin**一起使用，因为它实现 .NET Standard 2。0
- Python Speech SDK 作为 PyPI 模块提供

> [!TIP]
> 若要详细了解如何使用带有 Swift 的目标 C 语音 SDK，请参阅将<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">目标语言<span class="docon docon-navigate-external x-hidden-focus"></span>导入 swift </a>。

### <a name="system-requirements"></a>系统要求

- MacOS 版本10.13 或更高版本

# <a name="xcode"></a>[Xcode](#tab/mac-xcode)

:::row:::
    :::column span="3":::
        MacOS CocoaPod 包可用于下载并用于<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 （或更高版本） <span class="docon docon-navigate-external x-hidden-focus"></span> </a>集成开发环境（IDE）。 首先，<a href="https://aka.ms/csspeech/macosbinary" target="_blank">下载二进制 CocoaPod <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>。 在同一目录中提取 pod 以供其使用，创建*Podfile* ，并将`pod`作为列出。 `target`
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
        Xamarin 公开了完整的 macOS SDK，让 .NET 开发人员能够使用 C# 构建本机 Mac 应用程序。 有关详细信息，请参阅<a href="https://docs.microsoft.com/xamarin/mac/" target="_blank">Xamarin。 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/macos" target="_blank">macOS Speech SDK 快速入门目标-C 源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/macos" target="_blank">macOS Speech SDK 快速入门快速入门源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>