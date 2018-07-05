---
title: 适用于 C++ 和 Linux 的语音 SDK 快速入门 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 获取信息和代码示例，帮助你快速开始使用认知服务中适用于 Linux 和 C++ 的语音 SDK。
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753574"
---
# <a name="quickstart-for-c-and-linux"></a>适用于 C + + 和 Linux 的快速入门

认知服务语音 SDK 的当前版本是 `0.4.0`。

适用于 Linux 的认知服务语音 SDK 可用于构建 64 位和 32 位应用程序。 所需的文件可以作为 tar 文件从 https://aka.ms/csspeech/linuxbinary 下载。

> [!NOTE]
> 如果要寻找适用于 C++ 和 Windows 的快速入门，请前往[此处](quickstart-cpp-windows.md)。
> 如果要寻找适用于 C# 和 Windows 的快速入门，请前往[此处](quickstart-csharp-windows.md)。

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 这些说明假设你在个人电脑（x86 或 x64）上运行 Ubuntu 16.04。
> 在不同的 Ubuntu 版本或不同的 Linux 发行版上，你需要调整所需的步骤。

## <a name="prerequisites"></a>先决条件

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>获取二进制程序包

[!include[License Notice](includes/license-notice.md)]

1. 选择要放置语音 SDK 二进制文件和头文件的目录（绝对路径）。
   例如，选择主目录下的路径 `speechsdk`：

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. 如果该目录尚不存在，则创建该目录：

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. 下载并提取包含语音 SDK 二进制文件的 `.tar.gz` 存档：

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 验证所提取的程序包的顶级目录的内容：

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   它应当会显示第三方通知和许可证文件，以及用于存放头文件的 `include` 目录和用于存放二进制文件的 `lib` 目录。

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>代码示例

以下代码识别来自麦克风的英语语音。
请将其放置在一个名为 `quickstart-linux.cpp` 的文件中：

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

将代码中的订阅密钥替换为你获得的密钥。

## <a name="building"></a>生成

> [!NOTE]
> 确保将下面的生成命令复制并粘贴为“单个行”。

* 运行以下命令在 x64 计算机上生成应用程序：

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* 运行以下命令在 x86 计算机上生成应用程序：

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>正在运行

若要运行应用程序，需要将加载程序的库路径配置为指向语音 SDK 库。

* 在 x64 计算机上，运行：

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* 在 x86 计算机上，运行：

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

如下运行应用程序：

```sh
./quickstart-linux
```

如果一切正常，应当会看到与以下内容类似的输出：

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>下载示例

有关最新的示例集，请参阅[认知服务语音 SDK 示例 GitHub 存储库](https://aka.ms/csspeech/samples)。

## <a name="next-steps"></a>后续步骤

* 访问[示例页](samples.md)来查看更多示例。
