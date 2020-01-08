---
title: 快速入门：用于 Python 的语音 SDK 平台设置 - 语音服务
titleSuffix: Azure Cognitive Services
description: 根据本指南可设置配合使用 Python 和语音服务 SDK 的平台。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/09/2019
ms.author: erhopf
ms.openlocfilehash: 37b0deba6531c8e714b8786f8c815cf323c310ba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75469636"
---
本指南介绍如何安装用于 Python 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

- Python 语音 SDK 包适用于以下操作系统：
  - Windows：x64 和 x86
  - Mac：macOS X 10.12 或更高版本
  - Linux：x64 上的 Ubuntu 16.04、Ubuntu 18.04、Debian 9

## <a name="prerequisites"></a>必备条件

- 支持的 Linux 平台会要求安装某些库（要求安装 `libssl` 以获取安全套接字层支持；要求安装 `libasound2` 以获取声音支持）。 请参阅下面的发行版，了解安装这些库的正确版本所需的命令。

  - 在 Ubuntu 上，请运行以下命令来安装所需的包：

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - 在 Debian 9 上，请运行以下命令来安装所需的包：

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 请注意，首次安装它时，可能需要重启 Windows 才能继续使用本指南。
- 最后，需要 [Python 3.5 或更高版本](https://www.python.org/downloads/)。 若要检查安装，请打开命令提示符并键入命令 `python --version`，然后检查结果。 如果安装正确，将会收到“Python 3.5.1”或类似的响应。

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>使用 Visual Studio Code 安装语音 SDK

1. 下载并安装适用于你的平台的最新受支持版 [Python](https://www.python.org/downloads/)：3.5 或更高版本。
   - 在安装过程中，Windows 用户必须选择“将 Python 添加到 PATH”。
1. 下载并安装 [Visual Studio Code](https://code.visualstudio.com/Download)。
1. 打开 Visual Studio Code 并安装 Python 扩展。 在菜单中选择“文件”   >   “首选项” >   “扩展”。 搜索 **Python** 并单击“安装”。 

   ![安装 Python 扩展](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. 同样在 Visual Studio Code 中，通过集成式命令行安装语音 SDK Python 包：
   1. 打开终端（在下拉菜单中选择“终端” > “新终端”）  
   1. 在打开的终端中输入命令 `python -m pip install azure-cognitiveservices-speech`

安装过程到此完成，接下来可以开始在 Python 中编写语音 SDK 的代码，并可以转到下面的[后续步骤](#next-steps)。 如果你不熟悉 Visual Studio Code，请参阅更详细的 [Visual Studio Code 文档](https://code.visualstudio.com/docs)。 有关 Visual Studio Code 和 Python 的详细信息，请参阅 [Visual Studio Code Python 教程](https://code.visualstudio.com/docs/python/python-tutorial)。

## <a name="install-the-speech-sdk-using-the-command-line"></a>使用命令行安装语音 SDK

如果不使用 Visual Studio Code，可以使用以下命令通过 [PyPI](https://pypi.org/) 安装语音 SDK 的 Python 包。 Visual Studio Code 用户请转到下一附属部分。

```sh
pip install azure-cognitiveservices-speech
```

如果在 macOS 上操作，可能需要运行以下命令才能让上述 `pip` 命令正常运行：

```sh
python3 -m pip install --upgrade pip
```

成功使用 `pip` 安装 `azure-cognitiveservices-speech` 后，可以通过将命名空间导入 Python 项目来使用语音 SDK。 例如：

```py
import azure.cognitiveservices.speech as speechsdk
```

下面的[后续步骤](#next-steps)中所列的代码示例更详细地演示了此过程。

## <a name="support-and-updates"></a>支持和更新

语音 SDK Python 包的更新将通过 PyPI 分发，[发行说明](~/articles/cognitive-services/speech-service/releasenotes.md)中会发布相关通告。
如果有新版本可用，可以使用 `pip install --upgrade azure-cognitiveservices-speech` 命令进行更新。
通过查看 `azure.cognitiveservices.speech.__version__` 变量来检查当前安装的版本。

如果遇到问题或者缺少某项功能，请查看[支持和帮助选项](~/articles/cognitive-services/speech-service/support.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]