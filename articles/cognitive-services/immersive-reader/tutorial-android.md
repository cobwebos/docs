---
title: 教程：使用 Android 代码示例启动沉浸式阅读器
titleSuffix: Azure Cognitive Services
description: 在本教程中，你将配置并运行一个示例 Android 应用程序，该应用程序将启动沉浸式阅读器。
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: 637b4d988a4845369a441dce55f0043d873879f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516429"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>教程：使用 Android Java 代码示例启动沉浸式阅读器

在[概述](./overview.md)中，你了解了沉浸式阅读器是什么以及它如何实施经过验证的技术以提高语言学习者、新兴读者和有学习差异的学生的阅读理解能力。 本教程介绍如何创建可启动沉浸式阅读器的 Android 应用程序。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用示例项目配置并运行 Android 应用。
> * 获取访问令牌。
> * 启动沉浸式阅读器以显示示例内容。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

* 为 Azure Active Directory 身份验证配置的沉浸式阅读器资源。 按照[这些说明](./how-to-create-immersive-reader.md)进行设置。 在配置环境属性时，将需要在此处创建的一些值。 将会话的输出保存到文本文件中，以供将来参考。
* [Git](https://git-scm.com/)。
* [沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)。
* [Android Studio](https://developer.android.com/studio)。

## <a name="configure-authentication-credentials"></a>配置身份验证凭据

1. 启动 Android Studio 并从“immersive-reader-sdk/js/samples/quickstart-java-android”目录 (Java) 或“immersive-reader-sdk/js/samples/quickstart-kotlin”目录 (Kotlin) 中打开项目。

1. 在 /assets 文件夹内创建名为“env”的文件。 添加以下名称和值，并根据需要提供值。 不要将此文件提交到源代码管理中，因为它包含不应公开的机密。
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>启动沉浸式阅读器以显示示例内容

从 AVD 管理器中选择设备模拟器并运行该项目。

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](./reference.md)。
* 在 [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) 上查看代码示例。