---
title: 关于认知服务语音 SDK | Microsoft Docs
description: 可用于语音服务的 SDK 的概述。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ed523493f456e65f7aa5d3ad33914e3e52cd7044
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113382"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>关于认知服务语音 SDK

使用认知服务语音软件开发工具包 (SDK)，应用程序可以直接访问语音服务的功能，这使得软件开发工作更为容易。 当前，该 SDK 提供了对**语音到文本**、**语音翻译**和**意向识别**功能的访问。

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-windows-sdk"></a>获取 Windows SDK

语音 SDK 的 Windows 版本包括 32 位和 64 位 C/C++ 客户端库以及与 C# 一起使用的托管 (.NET) 库。 可以使用 NuGet 在 Visual Studio 中安装该 SDK；只需要搜索 `Microsoft.CognitiveServices.Speech` 即可。

## <a name="get-the-linux-sdk"></a>获取 Linux SDK

通过运行以下 shell 命令确保你具有必需的编译器和库：

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> 这些说明假设你在个人电脑（x86 或 x64）上运行 Ubuntu 16.04。 在不同的 Ubuntu 版本或不同的 Linux 发行版上，请根据你的环境调整这些步骤。

然后，[下载 SDK](https://aka.ms/csspeech/linuxbinary) 并将文件解压缩到你选择的一个目录中。 下表显示了 SDK 文件夹结构。

|路径|Description|
|-|-|
|`license.md`|许可|
|`third-party-notices.md`|第三方声明|
|`include`|用于 C 和 C++ 的头文件|
|`lib/x64`|用于与应用程序链接的本机 x64 库|
|`lib/x86`|用于与应用程序链接的本机 x86 库|

若要创建应用程序，请将必需的二进制文件和（库）复制到开发环境中，并根据需要将它们包括到生成流程中。

## <a name="get-the-java-sdk"></a>获取 Java SDK

将 Java SDK for Android 打包为 [AAR（Android 库）](https://developer.android.com/studio/projects/android-library)，其内附必要的库以及使用它所需的 Android 权限。
它作为包 `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0` 托管在 `https://csspeechstorage.blob.core.windows.net/maven/` 的 Maven 存储库中。
如果使用 Android Studio 项目中的包，需进行以下更改：

* 在项目级 `build.gradle` 文件中，向 `repository` 部分添加以下内容：

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* 在模块级 `build.gradle` 文件中，向 `dependencies` 部分添加以下内容：

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

Java SDK 也是[语音设备 SDK](speech-devices-sdk.md) 的一部分。

## <a name="next-steps"></a>后续步骤

* [获取语音试用订阅](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何在 C# 中识别语音](quickstart-csharp-dotnet-windows.md)
