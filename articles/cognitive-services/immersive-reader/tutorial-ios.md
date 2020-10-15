---
title: 教程：使用 Swift iOS 代码示例启动沉浸式阅读器
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将配置并运行一个示例 Swift 应用程序，该应用程序将启动沉浸式阅读器。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: ce9d3af8f7517e2acae5264197b842d47085279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516361"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>教程：使用 Swift iOS 代码示例启动沉浸式阅读器

在[概述](./overview.md)中，你了解了沉浸式阅读器是什么以及它如何实施经过验证的技术以提高语言学习者、新兴读者和有学习差异的学生的阅读理解能力。 本教程介绍如何创建可启动沉浸式阅读器的 iOS 应用程序。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用示例项目配置并运行适用于 iOS 的 Swift 应用。
> * 获取访问令牌。
> * 启动沉浸式阅读器以显示示例内容。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

* 为 Azure Active Directory 身份验证配置的沉浸式阅读器资源。 按照[这些说明](./how-to-create-immersive-reader.md)进行设置。 在配置环境属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。
* [macOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/)。
* [沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>配置身份验证凭据

1. 打开 Xcode 并打开 immersive-reader-sdk/js/samples/ios/quickstart-swift/quickstart-swift.xcodeproj。
1. 在顶部菜单中，选择“产品” > “方案” > “编辑方案”  。
1. 在“运行”视图中，选择“参数”选项卡。
1. 在“环境变量”部分中，添加以下名称和值。 提供在创建沉浸式阅读器资源时给出的值。

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

不要将此更改提交到源代码管理中，因为它包含不应公开的机密。

## <a name="start-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

在 Xcode 中，选择“Ctrl+R”运行项目。

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)。
* 在 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) 上查看代码示例。
