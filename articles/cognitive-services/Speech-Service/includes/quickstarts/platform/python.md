---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 7947c468f5d35869b9185062b8dc479234297486
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673231"
---
本指南介绍如何安装用于 Python 的[语音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。 如果只是需要包名称以便自行开始，请运行 `pip install azure-cognitiveservices-speech`。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>支持的操作系统

- Python 语音 SDK 包适用于以下操作系统：
  - Windows：x64 和 x86
  - Mac：macOS X 10.12 或更高版本
  - Linux：基于 x64 的 Ubuntu 16.04/18.04、Debian 9、RHEL 7/8、CentOS 7/8

## <a name="prerequisites"></a>先决条件

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

  - 在 RHEL/CentOS 上，请运行以下命令来安装所需的包：

        ```sh
        sudo yum update
        sudo yum install alsa-lib openssl python3
        ```

> [!NOTE]
> - 在 RHEL/CentOS 7 上，按照[如何为语音 SDK 配置 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的说明进行操作。
> - 在 RHEL/CentOS 8 上，按照[如何配置 OpenSSL for Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的说明进行操作。

- 在 Windows 上，需要安装适用于平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 请注意，首次安装它时，可能需要重启 Windows 才能继续使用本指南。
- 最后，需要 [Python 3.5 到 3.8](https://www.python.org/downloads/)。 若要检查安装，请打开命令提示符并键入命令 `python --version`，然后检查结果。 如果安装正确，将会收到“Python 3.5.1”或类似的响应。

## <a name="install-the-speech-sdk-from-pypi"></a>从 PyPI 安装语音 SDK

如果使用自己的环境或生成工具，请运行以下命令，从 [PyPI](https://pypi.org/) 安装语音 SDK。 对于 Visual Studio Code 的用户，请跳到下一子部分以按照指导安装。

```sh
pip install azure-cognitiveservices-speech
```

如果在 macOS 上操作，可能需要运行以下命令才能让上述 `pip` 命令正常运行：

```sh
python3 -m pip install --upgrade pip
```

成功使用 `pip` 安装 `azure-cognitiveservices-speech` 后，可以通过将命名空间导入 Python 项目来使用语音 SDK。

```py
import azure.cognitiveservices.speech as speechsdk
```

## <a name="install-the-speech-sdk-using-visual-studio-code"></a>使用 Visual Studio Code 安装语音 SDK

1. 下载并安装适用于你的平台的最新受支持 [Python](https://www.python.org/downloads/) 版本（3.5 到 3.8）。
   - 在安装过程中，Windows 用户必须选择“将 Python 添加到 PATH”。
1. 下载并安装 [Visual Studio Code](https://code.visualstudio.com/Download)。
1. 打开 Visual Studio Code 并安装 Python 扩展。 在菜单中选择“文件” > “首选项” > “扩展”。 搜索 **Python** 并单击“安装”。

   ![安装 Python 扩展](~/articles/cognitive-services/speech-service/media/sdk/qs-python-vscode-python-extension.png)

1. 同样在 Visual Studio Code 中，通过集成式命令行安装语音 SDK Python 包：
   1. 打开终端（在下拉菜单中选择“终端” > “新终端”） 
   1. 在打开的终端中输入命令 `python -m pip install azure-cognitiveservices-speech`

如果你不熟悉 Visual Studio Code，请参阅更详细的 [Visual Studio Code 文档](https://code.visualstudio.com/docs)。 有关 Visual Studio Code 和 Python 的详细信息，请参阅 [Visual Studio Code Python 教程](https://code.visualstudio.com/docs/python/python-tutorial)。

## <a name="support-and-updates"></a>支持和更新

语音 SDK Python 包的更新将通过 PyPI 分发，[发行说明](~/articles/cognitive-services/speech-service/releasenotes.md)中会发布相关通告。
如果有新版本可用，可以使用 `pip install --upgrade azure-cognitiveservices-speech` 命令进行更新。
通过查看 `azure.cognitiveservices.speech.__version__` 变量来检查当前安装的版本。

如果遇到问题或者缺少某项功能，请查看[支持和帮助选项](~/articles/cognitive-services/speech-service/support.md)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [windows](../quickstart-list.md)]
