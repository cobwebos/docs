---
title: 在 Azure Functions 中使用 Python 和 TensorFlow 进行机器学习推理
description: 本教程演示如何在 Azure Functions 中应用 TensorFlow 机器学习模型
author: anthonychu
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: e7c4e1bbd23d43d5e11ab8b26c3d4e1215b4946b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230508"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>教程：在 Azure Functions 中使用 Python 和 TensorFlow 应用机器学习模型

本文演示如何在 Azure Functions 中结合机器学习模型使用 Python 和 TensorFlow 来根据图像内容对图像分类。

在本教程中，学习： 

> [!div class="checklist"]
> * 初始化用于在 Python 中开发 Azure Functions 的本地环境
> * 将自定义 TensorFlow 机器学习模型导入函数应用
> * 生成一个无服务器 HTTP API 用于预测照片中是否包含狗或猫
> * 从 Web 应用程序使用 API

![已完成项目的屏幕截图](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件 

若要在 Python 中创建 Azure Functions，需要安装几个工具。

- [Python 3.6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>克隆教程存储库

若要开始，请打开终端并使用 Git 克隆以下存储库：

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

该存储库包含几个文件夹。

- *start*：这是本教程的工作文件夹
- *end*：这是供你参考的最终结果和完整实现
- *resources*：包含机器学习模型和帮助器库
- *frontend*：一个调用函数应用的网站

## <a name="create-and-activate-a-python-virtual-environment"></a>创建并激活 Python 虚拟环境

Azure Functions 需要 Python 3.6.x。 需要创建一个虚拟环境，以确保使用所需的 Python 版本。

将当前工作目录更改为 *start* 文件夹。 然后创建并激活名为 *.venv* 的虚拟环境。 根据安装的 Python，用于创建 Python 3.6 虚拟环境的命令可能与以下说明中所述不同。

#### <a name="linux-and-macos"></a>Linux 和 macOS：

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

现在，终端提示带有 `(.venv)` 前缀，表示已成功激活虚拟环境。 确认虚拟环境中的 `python` 是否确实为 Python 3.6.x。

```console
python --version
```

> [!NOTE]
> 在本教程的余下部分，你将在虚拟环境中运行命令。 如果需要在终端中重新激活虚拟环境，请根据你的操作系统执行相应的激活命令。

## <a name="create-an-azure-functions-project"></a>创建 Azure Functions 项目

在 *start* 文件夹中，使用 Azure Functions Core Tools 初始化 Python 函数应用。

```console
func init --worker-runtime python
```

一个函数应用可以包含一个或多个 Azure Functions。 在编辑器中打开 *start* 文件夹并检查内容。

- [*local.settings.json*](functions-run-local.md#local-settings-file)：包含用于本地开发的应用程序设置
- [*host.json*](functions-host-json.md)：包含 Azure Functions 主机和扩展的设置
- [*requirements.txt*](functions-reference-python.md#package-management)：包含此应用程序所需的 Python 包

## <a name="create-an-http-function"></a>创建 HTTP 函数

该应用程序需要单个 HTTP API 终结点，该终结点使用图像 URL 作为输入，并返回有关图像是否包含狗或猫的预测。

在终端中，使用 Azure Functions Core Tools 搭建名为 *classify* 的新 HTTP 函数。

```console
func new --language python --template HttpTrigger --name classify
```

此时会创建名为 *classify* 的新文件夹，其中包含两个文件。

- *\_\_init\_\_.py*：main 函数的文件
- *function.json*：描述函数触发器及其输入和输出绑定的文件

### <a name="run-the-function"></a>运行函数

在已激活 Python 虚拟环境的终端中启动函数应用。

```console
func start
```

打开浏览器并导航到以下 URL。 该函数应会执行并返回 *Hello Azure!*

```
http://localhost:7071/api/classify?name=Azure
```

使用 `Ctrl-C` 停止函数应用。

## <a name="import-the-tensorflow-model"></a>导入 TensorFlow 模型

你将使用一个预生成的 TensorFlow 模型，该模型已使用 Azure 自定义视觉服务进行训练并从中导出。

> [!NOTE]
> 若要使用自定义视觉服务的免费层生成自己的模型，可以遵照[示例项目存储库中的说明](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)操作。

该模型包含 <REPOSITORY_ROOT>/resources/model  文件夹中的两个文件：*model.db* 和 *labels.txt*。 请将这些文件复制到 *classify* 函数的文件夹中。

#### <a name="linux-and-macos"></a>Linux 和 macOS：

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

请务必在上述命令中包含 \*。 确认 *classify* 现在是否包含名为 *model.pb* 和 *labels.txt* 的文件。

## <a name="add-the-helper-functions-and-dependencies"></a>添加帮助器函数和依赖项

*resources* 文件夹中名为 *predict.py* 的文件内包含一些用于准备输入图像并使用 TensorFlow 进行预测的帮助器函数。 请将此文件复制到 *classify* 函数的文件夹中。

#### <a name="linux-and-macos"></a>Linux 和 macOS：

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

确认 *classify* 现在是否包含名为 *predict.py* 的文件。

### <a name="install-dependencies"></a>安装依赖项

帮助器库具有一些需要安装的依赖项。 在编辑器中打开 *start/requirements.txt*，并将以下依赖项添加到该文件。

```txt
tensorflow==1.14
Pillow
requests
```

保存文件。

在已激活虚拟环境的终端中，在 *start* 文件夹中运行以下命令以安装依赖项。 某些安装步骤可能需要几分钟才能完成。

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>在全局变量中缓存模型

在编辑器中打开 *predict.py*，并查看文件顶部附近的 `_initialize` 函数。 请注意，首次执行该函数时已从磁盘加载 TensorFlow 模型，并且该模型已保存到全局变量中。 后续执行 `_initialize` 函数时，将跳过从磁盘加载模型的步骤。 使用此方法将模型缓存在内存中可以加速后续的预测。

有关全局变量的详细信息，请参阅 [Azure Functions Python 开发人员指南](functions-reference-python.md#global-variables)。

## <a name="update-function-to-run-predictions"></a>更新函数以运行预测

在编辑器中打开 *classify/\_\_init\_\_.py*。 导入前面已添加到同一文件夹中的 *predict* 库。 在该文件中现有的其他 import 语句下面添加以下 `import` 语句。

```python
import json
from .predict import predict_image_from_url
```

将函数模板代码替换为以下代码。

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

确保保存所做的更改。

此函数接收名为 `img` 的查询字符串参数中的图像 URL。 它从帮助器库调用 `predict_image_from_url`，以下载图像并使用 TensorFlow 模型返回预测结果。 然后，该函数返回包含结果的 HTTP 响应。

由于 HTTP 终结点是由另一个域中托管的网页调用的，因此 HTTP 响应包含 `Access-Control-Allow-Origin` 标头，以满足浏览器的跨域资源共享 (CORS) 要求。

> [!NOTE]
> 在生产应用程序中，请将 `*` 更改为网页的特定源，以提高安全性。

### <a name="run-the-function-app"></a>运行函数应用

确保 Python 虚拟环境仍处于激活状态，并使用以下命令启动函数应用。

```console
func start
```

在浏览器中打开此 URL，以使用猫照片的 URL 调用你的函数。 确认返回了有效的预测结果。

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

使函数应用保持运行。

### <a name="run-the-web-app"></a>运行 Web 应用

*frontend* 文件夹中提供了一个在函数应用中使用 HTTP API 的简单 Web 应用。

单独打开一个终端并切换到 *frontend* 文件夹。  使用 Python 3.6 启动一个 HTTP 服务器。

#### <a name="linux-and-macos"></a>Linux 和 macOS：

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

在浏览器中，导航到终端中显示的 HTTP 服务器 URL。 此时应会显示一个 Web 应用。 在文本框中输入以下照片 URL 之一。 也可以使用可公开访问的猫或狗照片的 URL。

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

单击“提交”时，将会调用该函数应用，并在页面上显示结果。

## <a name="clean-up-resources"></a>清理资源
整篇教程在计算机本地运行，因此没有任何 Azure 资源或服务需要清理。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何使用 Azure Functions 生成和自定义 HTTP API，以使用 TensorFlow 模型做出预测。 此外，你还了解了如何从 Web 应用程序调用 API。

可以使用本教程中所述的方法生成具有不同复杂性的 API，所有这些 API 都可在 Azure Functions 提供的无服务器计算模型上运行。

若要将函数应用部署到 Azure，请使用 [Azure Functions Core Tools](./functions-run-local.md#publish) 或 [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions)。

> [!div class="nextstepaction"]
> [Azure Functions Python 开发人员指南](./functions-reference-python.md)
