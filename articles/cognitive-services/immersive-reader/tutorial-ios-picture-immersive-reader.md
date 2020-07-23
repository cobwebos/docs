---
title: 教程：创建一个可拍摄照片并在沉浸式阅读器中启动它的 iOS 应用 (Swift)
titleSuffix: Azure Cognitive Services
description: 在本教程中，我们将从头开始构建一个 iOS 应用，并添加“将图片导入沉浸式阅读器”功能。
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: a7e0cb41f32a60e4f00cb60cc3c86e40ab926785
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735124"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>教程：创建一个可通过照片中的内容启动沉浸式阅读器的 iOS 应用 (Swift)

[沉浸式阅读器](https://www.onenote.com/learningtools)是一款经过广泛设计的工具，它实现了可靠的技术以提高阅读理解能力。

[计算机视觉认知服务读取 API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text) 使用 Microsoft 的最新识别模型来检测图像中的文本内容，并将已识别的文本转换为计算机可读的字符流。

在本教程中，我们将从头开始构建一个 iOS 应用，并使用沉浸式阅读器 SDK 集成读取 API 和沉浸式阅读器。 本教程的完整工作示例可在[此处](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/ios)找到。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* 为 Azure Active Directory 身份验证配置的沉浸式阅读器资源。 按照[这些说明](./how-to-create-immersive-reader.md)进行设置。 在配置示例项目属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。
* 使用此示例需要通过 Azure 订阅来订阅计算机视觉认知服务。 [在 Azure 门户中创建计算机视觉认知服务资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)。

## <a name="create-an-xcode-project"></a>创建 Xcode 项目

在 Xcode 中创建新项目。

![新建项目](./media/ios/xcode-create-project.png)

选择“单一视图应用”。 

![新建单一视图应用](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>获取 SDK CocoaPod
若要使用沉浸式阅读器 SDK，最简单的方法是借助 CocoaPod。 若要通过 CocoaPod 进行安装，请执行以下操作：
1. [安装 CocoaPod](http://guides.cocoapods.org/using/getting-started.html) - 按入门指南的要求安装 Cocoapod。
2. 通过在 Xcode 项目的根目录中运行 `pod init`，创建 Podfile。
3.  通过添加 `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'` 将 CocoaPod 添加到 Podfile。 你的 Podfile 应该如下所示（使用你的目标的名称替换 picture-to-immersive-reader-swift）：
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :git => 'https://github.com/microsoft/immersive-reader-sdk.git'
  end
```
4. 在终端的 Xcode 项目的目录中运行 `pod install` 命令，以便安装沉浸式阅读器 SDK Pod。
5. 将 `import immersive_reader_sdk` 添加到所有需要引用此 SDK 的文件中。
6. 若要打开项目，请确保打开 `.xcworkspace` 文件而不是 `.xcodeproj` 文件。

## <a name="acquire-an-azure-ad-authentication-token"></a>获取 Azure AD 身份验证令牌

此部分需要上述 Azure AD 身份验证配置先决条件步骤中的一些值。 请回头参考保存的该会话的文本文件。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

在包含 ViewController.swift 文件的主项目文件夹中，创建名为 Constants.swift 的 Swift 类文件。 将该类替换为以下代码，根据情况添加你的值。 将此文件保留为仅存在于你的计算机上的本地文件，并确保不要将此文件提交到源代码管理中，因为它包含不应公开的机密。 建议不要将机密保留在应用中， 而应使用后端服务来获取令牌，这样就可以将机密保留在应用外部，使之脱离设备。 后端 API 终结点应该以某种形式的身份验证（例如，[OAuth](https://oauth.net/2/)）进行保护，防止未经授权的用户获取令牌并将其用于你的沉浸式阅读器服务和计费；该内容超出了本教程的范围。

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>将应用设置为在没有情节提要的情况下运行

打开 AppDelegate.swift，将此文件替换为以下代码。

## <a name="add-functionality-for-taking-and-uploading-photos"></a>添加拍摄和上传照片的功能

将 ViewController.swift 重命名为 PictureLaunchViewController.swift，并将此文件替换为以下代码。

## <a name="build-and-run-the-app"></a>生成并运行应用

通过选择模拟器或设备目标，在 Xcode 中设置存档方案。
![存档方案](./media/ios/xcode-archive-scheme.png)<br/>
![选择目标](./media/ios/xcode-select-target.png)

在 Xcode 中按 Ctrl + R 或单击播放按钮以运行项目，然后应用就会在指定模拟器或设备上启动。

在应用中会看到：

![示例应用](./media/ios/picture-to-immersive-reader-ipad-app.png)

在应用中按“拍照”按钮或“从库中选择照片”按钮，拍摄或上传一张文本照片，然后沉浸式阅读器就会启动并显示照片中的文本。

![沉浸式阅读器](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)
