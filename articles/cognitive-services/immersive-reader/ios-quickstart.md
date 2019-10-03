---
title: 快速入门：创建用于启动沉浸式阅读器的 iOS 应用 (Swift)
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，你将从头开始构建一个 iOS 应用，并添加沉浸式阅读器功能。
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 64fd7508244f2123cc10ee96ec4f805050aedfaa
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899536"
---
# <a name="quickstart-create-an-ios-app-that-launches-the-immersive-reader-swift"></a>快速入门：创建用于启动沉浸式阅读器的 iOS 应用 (Swift)

[沉浸式读取器](https://www.onenote.com/learningtools)是一款经过广泛设计的工具，它实现了可靠的技术以提高阅读理解能力。

在本快速入门中，你将从头开始构建一个 iOS 应用，并使用沉浸式阅读器 SDK 集成沉浸式阅读器。 本快速入门的完整工作示例可在[此处](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/samples/quickstart-swift)找到。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* 为 Azure Active Directory (Azure AD) 身份验证配置的沉浸式阅读器资源。 按照[这些说明](./azure-active-directory-authentication.md)进行设置。 在配置示例项目属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。

## <a name="create-an-xcode-project"></a>创建 Xcode 项目

在 Xcode 中创建新项目。

![新建项目](./media/ios/xcode-create-project.png)

选择“单一视图应用”。 

![新建单一视图应用](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>获取 SDK CocoaPod
若要使用沉浸式阅读器 SDK，最简单的方法是借助 CocoaPod。 若要通过 CocoaPod 进行安装，请执行以下操作：
1. [安装 CocoaPod](http://guides.cocoapods.org/using/getting-started.html) - 按入门指南的要求安装 Cocoapod。
2. 通过在 Xcode 项目的根目录中运行 `pod init`，创建 Podfile。
3.  通过添加 `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` 将 CocoaPod 添加到 Podfile。 你的 Pofile 应如下所示（请将 quickstart-swift 替换为你的目标名称）：
 ```ruby
  platform :ios, '9.0'

  target 'quickstart-swift' do
  use_frameworks!
  # Pods for quickstart-swift
  pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'
  end
```
4. 在终端的 Xcode 项目的目录中运行 `pod install` 命令，以便安装沉浸式阅读器 SDK Pod。
5. 将 `import immersive_reader_sdk` 添加到所有需要引用此 SDK 的文件中。
6. 若要打开项目，请确保打开 `.xcworkspace` 文件而不是 `.xcodeproj` 文件。

## <a name="acquire-an-azure-ad-authentication-token"></a>获取 Azure AD 身份验证令牌

此部分需要来自上面的 Azure AD 身份验证配置先决条件步骤的一些值。 请回头参考保存的该会话的文本文件。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

在包含 ViewController.swift 文件的主项目文件夹中，创建名为 Constants.swift 的 Swift 类文件。 将该类替换为以下代码，根据情况添加你的值。 将此文件保留为仅存在于你的计算机上的本地文件，并确保不要将此文件提交到源代码管理中，因为它包含不应公开的机密。 建议不要将机密保留在应用中， 而应使用后端服务来获取令牌，这样就可以将机密保留在应用外部，使之脱离设备。 后端 API 终结点应该以某种形式的身份验证（例如，[OAuth](https://oauth.net/2/)）进行保护，防止未经授权的用户获取令牌并将其用于你的沉浸式阅读器服务和计费；该内容超出了本快速入门的范围。

[!code-swift[Constants](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/Constants.swift)]

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>将应用设置为在没有情节提要的情况下运行

打开 AppDelegate.swift，将此文件替换为以下代码。

[!code-swift[AppDelegate](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/AppDelegate.swift)]

## <a name="create-the-launch-view-controller-and-add-sample-content"></a>创建启动视图控制器并添加示例内容

将 ViewController.swift 重命名为 LaunchViewController.swift，并将此文件替换为以下代码。

[!code-swift[LaunchViewController](~/ImmersiveReaderSdk/iOS/samples/quickstart-swift/quickstart-swift/LaunchViewController.swift)]

## <a name="build-and-run-the-app"></a>生成并运行应用

通过选择模拟器或设备目标，在 Xcode 中设置存档方案。
![存档方案](./media/ios/xcode-archive-scheme.png)<br/>
![选择目标](./media/ios/xcode-select-target.png)

在 Xcode 中按 Ctrl + R 或单击播放按钮以运行项目，然后应用就会在指定模拟器或设备上启动。

在应用中会看到：

![示例应用](./media/ios/sample-app-ipad.png)

单击“沉浸式阅读器”按钮时，将会看到沉浸式阅读器随应用上的内容一起启动。

![沉浸式阅读器](./media/ios/immersive-reader-ipad.png)

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 iOS SDK](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) 和[沉浸式阅读器 iOS SDK 参考](./ios-reference.md)
