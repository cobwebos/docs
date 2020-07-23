---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399989"
---
:::row:::
    :::column span="3":::
        针对 iOS 进行开发时，有两个可用的语音 Sdk。 目标为 C 的语音 SDK 以 iOS CocoaPod 包的形式提供。 或者，.NET Speech SDK 可与 Xamarin 一起使用，因为它实现的 .NET Standard 2.0。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 若要详细了解如何使用带有 Swift 的目标 C 语音 SDK，请参阅将<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">目标语言<span class="docon docon-navigate-external x-hidden-focus"></span>导入 swift </a>。

### <a name="system-requirements"></a>系统要求

- MacOS 版本10.3 或更高版本
- 目标 iOS 9.3 或更高版本

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        IOS CocoaPod 包可用于下载并用于<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1 （或更高版本） <span class="docon docon-navigate-external x-hidden-focus"></span> </a>集成开发环境（IDE）。 首先，<a href="https://aka.ms/csspeech/iosbinary" target="_blank">下载二进制 CocoaPod <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>。 在同一目录中提取 pod 以供其使用，创建*Podfile* ，并将`pod`作为列出。 `target`
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
        Xamarin.iOS 为 .NET 开发人员提供了完整的 iOS SDK。 在 Visual Studio 中使用 C# 或 F# 生成完全本机的 iOS 应用。 有关详细信息，请参阅<a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Xamarin" src="https://docs.microsoft.com/media/logos/logo_xamarin.svg" width="60px">
            &nbsp;❤️ &nbsp;        <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

---

#### <a name="additional-resources"></a>其他资源

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS 语音 SDK 快速入门目标-C 源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS 语音 SDK 快速入门快速入门源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>