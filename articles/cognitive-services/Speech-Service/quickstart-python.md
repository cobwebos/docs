---
title: 快速入门：识别语音，Python - 语音服务
titleSuffix: Azure Cognitive Services
description: 按照本指南使用适用于 Python 的语音 SDK 创建语音转文本控制台应用程序。 完成后，可以使用计算机的麦克风实时将语音转录为文本。
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: chlandsi
ms.openlocfilehash: 1c7287b919c46ead4f961aff769da5c8bf68cc6f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226564"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>快速入门：使用适用于 Python 的语音 SDK 识别语音

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

本文介绍如何通过适用于 Python 的语音 SDK 来使用语音服务。 其中演示了如何识别麦克风输入中的语音。

## <a name="prerequisites"></a>先决条件

在开始之前，请满足以下一系列先决条件：

* 语音服务的 Azure 订阅密钥。 [免费获取](get-started.md)。
* [Python 3.5（64 位）](https://www.python.org/downloads/)或更高版本。
* Python 语音 SDK 包适用于 Windows (x64)、Mac（macOS X 10.12 或更高版本）和 Linux（x64 上的 Ubuntu 16.04 或 18.04）。
* 在 Ubuntu 上，运行以下命令安装所需的包：

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* 在 Windows 上，还需要安装适用于你的平台的 [Microsoft Visual C++ Redistributable for Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。

## <a name="install-the-speech-sdk"></a>安装语音 SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

可以在命令行中使用以下命令，通过 [PyPI](https://pypi.org/) 安装认知服务语音 SDK Python 包：

```sh
pip install azure-cognitiveservices-speech
```

认知服务语音 SDK 的当前版本是 `1.2.0`。

## <a name="support-and-updates"></a>支持和更新

语音 SDK Python 包的更新将通过 PyPI 分发，[发行说明](./releasenotes.md)页上会发布相关通告。
如果有新版本可用，可以使用 `pip install --upgrade azure-cognitiveservices-speech` 命令进行更新。
可以通过查看 `azure.cognitiveservices.speech.__version__` 变量来检查当前安装的版本。

如果遇到问题或者缺少某项功能，请查看我们的[支持页面](./support.md)。

## <a name="create-a-python-application-using-the-speech-sdk"></a>使用语音 SDK 创建 Python 应用程序

### <a name="run-the-sample"></a>运行示例

可将本快速入门中的[代码](#quickstart-code)复制到源文件 `quickstart.py`，然后在 IDE 或控制台中运行该代码

```sh
python quickstart.py
```

或者，可以从[认知服务语音示例存储库](https://github.com/Azure-Samples/cognitive-services-speech-sdk/)以 [Jupyter](https://jupyter.org) Notebook 的形式下载本快速入门教程，并将其作为 Notebook 运行。

### <a name="sample-code"></a>代码示例

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>通过 Visual Studio Code 安装并使用语音 SDK

1. 在计算机上[下载](https://www.python.org/downloads/)并安装 64 位版本的 Python（3.5 或更高版本）。
1. [下载](https://code.visualstudio.com/Download)并安装 Visual Studio Code。
1. 打开 Visual Studio Code，在菜单中选择“文件” > “首选项” > “扩展”并搜索“Python”，以安装 Python 扩展。
   ![安装 Python 扩展](media/sdk/qs-python-vscode-python-extension.png)
1. 使用 Windows 资源管理器等工具创建一个文件夹用于存储项目。
1. 在 Visual Studio Code 中单击“文件”图标，然后打开创建的文件夹。
   ![打开文件夹](media/sdk/qs-python-vscode-python-open-folder.png)
1. 单击“新建文件”图标创建新的 Python 源文件 `speechsdk.py`。
   ![创建文件](media/sdk/qs-python-vscode-python-newfile.png)
1. 复制 [Python 代码](#quickstart-code)并将其粘贴到新建的文件，然后保存文件。
1. 插入语音服务订阅信息。
1. 如果已选择 Python 解释器，窗口底部的状态栏左侧会显示它。
   否则，可以通过打开“命令面板”(`Ctrl+Shift+P`) 并键入 **Python:Select Interpreter** 来显示可用 Python 解释器的列表，然后选择适当的解释器。
1. 如果尚未为所选的 Python 解释器安装语音 SDK Python 包，可在 Visual Studio Code 中轻松安装。
   若要安装语音 SDK 包，请再次启动命令面板 (`Ctrl+Shift+P`) 并键入 **Terminal:Create New Integrated Terminal** 来打开终端。
   在打开的终端中，输入命令 `python -m pip install azure-cognitiveservices-speech`，或者输入适用于系统的命令。
1. 若要运行示例代码，请在编辑器中的某个位置单击右键，然后选择“在终端中运行 Python 文件”。
   根据提示讲出几句话，片刻之后，听录的文本应会显示。
   ![运行示例](media/sdk/qs-python-vscode-python-run.png)

如果在遵照这些说明操作时遇到问题，请参阅内容更全面的 [Visual Studio Code Python 教程](https://code.visualstudio.com/docs/python/python-tutorial)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 Python 示例](https://aka.ms/csspeech/samples)
