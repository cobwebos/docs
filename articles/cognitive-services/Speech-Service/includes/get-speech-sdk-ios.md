---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81399989"
---
:::row:::
    :::column span="3":::
        针对 iOS 开发时，有两个语音 SDK 可用。 Objective-C 语音 SDK 以 iOS CocoaPod 包的形式本机提供。 另外，.NET 语音 SDK 可与 Xamarin.iOS 一起使用，因为它实现了 .NET Standard 2.0。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 有关在 Swift 中使用 Objective-C 语音 SDK 的详细信息，请参阅<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">将 Objective-C 导入 Swift <span class="docon docon-navigate-external x-hidden-focus"></span></a>。

### <a name="system-requirements"></a>系统要求

- macOS 10.3 或更高版本
- 目标 iOS 9.3 或更高版本

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        iOS CocoaPod 包可供下载并在 <a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1（或更高版本）<span class="docon docon-navigate-external x-hidden-focus"></span></a>集成开发环境 (IDE) 中使用。 首先，<a href="https://aka.ms/csspeech/iosbinary" target="_blank">下载二进制 CocoaPod <span class="docon docon-navigate-external x-hidden-focus"></span></a>。 将 Pod 提取到同一目录中以供使用，创建一个 Podfile  并将 `pod` 作为 `target` 列出。
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

target 'AppName' do
  pod 'MicrosoftCognitiveServicesSpeech', '~> 1.10.0'
end
```

# <a name="xamarinios"></a>[Xamarin.iOS](#tab/ios-xamarin)

:::row:::
    :::column span="3":::
        Xamarin.iOS 向 .NET 开发人员公开了完整的 iOS SDK。 使用 C# 或 F# 在 Visual Studio 中生成完全本机的 iOS 应用。 有关详细信息，请参阅 <a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"></span></a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp; ❤️ &nbsp;         <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>其他资源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS 语音 SDK 快速入门 Objective-C 源代码 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS 语音 SDK 快速入门 Swift 源代码 <span class="docon docon-navigate-external x-hidden-focus"></span></a>