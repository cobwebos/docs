---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 7ce193c2c2f5e10a27550da68a4c2d2fdcd1db7f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399989"
---
:::row:::
    :::column span="3":::
        为 iOS 开发时，有两个语音 SDK 可用。 Objective-C 语音 SDK 作为 iOS CocoaPod 包在本机上可用。 或者，.NET 语音 SDK 可以在 Xamarin.iOS 实现 .NET 标准 2.0 时与 Xamarin.iOS 一起使用。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="iOS" src="https://docs.microsoft.com/media/logos/logo_ios.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 有关使用带有 Swift 的目标-C 语音 SDK 的详细信息，请参阅<a href="https://developer.apple.com/documentation/swift/imported_c_and_objective-c_apis/importing_objective-c_into_swift" target="_blank">将目标<span class="docon docon-navigate-external x-hidden-focus"></span>C 导入 Swift </a>。

### <a name="system-requirements"></a>系统要求

- macOS 版本 10.3 或更高版本
- 目标 iOS 9.3 或更高版本

# <a name="xcode"></a>[Xcode](#tab/ios-xcode)

:::row:::
    :::column span="3":::
        iOS CocoaPod 软件包可供下载，可与<a href="https://apps.apple.com/us/app/xcode/id497799835" target="_blank">Xcode 9.4.1（或更高版本<span class="docon docon-navigate-external x-hidden-focus"></span>）</a>集成开发环境 （IDE） 一起下载和使用。 首先<a href="https://aka.ms/csspeech/iosbinary" target="_blank">，下载二进制可可盒<span class="docon docon-navigate-external x-hidden-focus"></span></a>。 在同一目录中提取 Pod 以使其预期用途，创建*Podfile*并将`pod`列为`target`。
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
        Xamarin.iOS 为 .NET 开发人员提供了完整的 iOS SDK。 在 Visual Studio 中使用 C# 或 F# 生成完全本机的 iOS 应用。 有关详细信息，请参阅<a href="https://docs.microsoft.com/xamarin/ios/" target="_blank">Xamarin.iOS <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>。
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

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/objectivec/ios" target="_blank">iOS 语音 SDK 快速入门目标 C 源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/swift/ios" target="_blank">iOS 语音 SDK 快速入门 Swift 源代码<span class="docon docon-navigate-external x-hidden-focus"></span></a>