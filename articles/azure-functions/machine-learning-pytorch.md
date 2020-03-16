---
title: 将 PyTorch 模型部署为 Azure Functions 应用程序
description: 将 PyTorch 中预先训练的 ResNet 18 深度神经网络与 Azure Functions 配合使用来将 1000 个 ImageNet 标签中的 1 个分配给图像。
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.openlocfilehash: 17acb7e351d5f1c009a6a8a14717e987fae3e895
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376903"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>教程：使用 PyTorch 将预先训练的图像分类模型部署到 Azure Functions

在本文中，你将了解如何使用 Python、PyTorch 和 Azure Functions 加载预先训练的模型，以根据图像内容对图像进行分类。 由于你是在本地完成所有操作，而不会在云中创建任何 Azure 资源，因此，完成本教程不会产生任何费用。

> [!div class="checklist"]
> * 初始化用于在 Python 中开发 Azure Functions 的本地环境。
> * 将预先训练的 PyTorch 机器学习模型导入到某个函数应用。
> * 构建一个无服务器 HTTP API，用于将图像归类为 1000 个 ImageNet [类](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a)中的一个。
> * 从 Web 应用使用该 API。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Python 3.7.4 或更高版本](https://www.python.org/downloads/release/python-374/)。 （Python 3.8.x 和 Python 3.6.x 也通过 Azure Functions 进行了验证。）
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- 代码编辑器，如 [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>先决条件检查

1. 在终端或命令窗口中，运行 `func --version` 检查 Azure Functions Core Tools 的版本是否为 2.7.1846 或以上。
1. 运行 `python --version` (Linux/MacOS) 或 `py --version` (Windows)，检查报告的 Python 版本是否为 3.7.x。

## <a name="clone-the-tutorial-repository"></a>克隆教程存储库

1. 在终端或命令窗口中，使用 Git 克隆以下存储库：

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. 导航到该文件夹并检查其内容。

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *start* 是本教程的工作文件夹。
    - *end* 是供你参考的最终结果和完整实现。
    - *resources* 包含机器学习模型和帮助器库。
    - *frontend* 是调用函数应用的网站。

## <a name="create-and-activate-a-python-virtual-environment"></a>创建并激活 Python 虚拟环境

导航到 *start* 文件夹，运行以下命令以创建并激活名为 `.venv` 的虚拟环境。


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

如果 Python 未在 Linux 分发版中安装 venv 包，请运行以下命令：

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
.venv\scripts\activate
```

---

所有后续命令将在这个已激活的虚拟环境中运行。 （若要退出虚拟环境，请运行 `deactivate`。）


## <a name="create-a-local-functions-project"></a>创建本地函数项目

在 Azure Functions 中，有一个函数项目是一个或多个单独函数（每个函数响应特定的触发器）的容器。 项目中的所有函数共享相同的本地和宿主配置。 在本部分中，你将创建一个函数项目，其中包含单个名为 `classify` 的、提供 HTTP 终结点的样板函数。 在稍后的部分，你将添加更具体的代码。

1. 在 *start* 文件夹中，使用 Azure Functions Core Tools 初始化 Python 函数应用：

    ```
    func init --worker-runtime python
    ```

    初始化后，*start* 文件夹包含项目的各个文件，其中包括名为 [local.settings.json](functions-run-local.md#local-settings-file) 和 [host.json](functions-host-json.md) 的配置文件。 由于 *local.settings.json* 可以包含从 Azure 下载的机密，因此，默认情况下，该文件会从 *.gitignore* 文件的源代码管理中排除。

    > [!TIP]
    > 由于函数项目绑定到特定的运行时，因此必须使用相同的语言编写项目中的所有函数。

1. 使用以下命令将一个函数添加到项目，其中，`--name` 参数是该函数的唯一名称，`--template` 参数指定该函数的触发器。 `func new` 创建一个与函数名称匹配的、包含项目所选语言适用的代码文件的子文件夹，以及一个名为 *function.json* 的配置文件。

    ```
    func new --name classify --template "HTTP trigger"
    ```

    此命令创建与函数名称匹配的文件夹 *classify*。 该文件夹中有两个文件： *\_\_init\_\_.py*，其中包含函数代码；*function.json*，描述函数的触发器及其输入和输出绑定。 有关这些文件的内容的详细信息，请参阅 Python 快速入门中的[检查文件内容](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents)。


## <a name="run-the-function-locally"></a>在本地运行函数

1. 通过启动 *start* 文件夹中的本地 Azure Functions 运行时主机来启动函数：

    ```
    func start
    ```

1. 看到输出中显示了 `classify` 终结点后，请导航到 URL ```http://localhost:7071/api/classify?name=Azure```。 消息“Hello Azure!” 应会显示在输出中。

1. 按 **Ctrl**-**C** 停止主机。


## <a name="import-the-pytorch-model-and-add-helper-code"></a>导入 PyTorch 模型并添加帮助器代码

若要修改 `classify` 函数以根据图像内容对图像进行分类，请使用一个预先训练的 [ResNet](https://arxiv.org/abs/1512.03385) 模型。 该预先训练的模型来自 [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/)，它将图像归类为 1000 个 [ImageNet 类](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a)中的一个。 然后，将一些帮助器代码和依赖项添加到项目。

1. 在 *start* 文件夹中，运行以下命令来将预测代码和标签复制到 *classify* 文件夹中。

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. 验证 *classify* 文件夹是否包含名为 *predict.py* 和 *labels.txt* 的文件。 如果不包含，请检查是否在 *start* 文件夹中运行了该命令。

1. 在文本编辑器中打开 *start/requirements.txt*，添加帮助器代码所需的依赖项，应当如下所示：

    ```txt
    azure-functions
    requests
    numpy==1.15.4
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-win_amd64.whl; sys_platform == 'win32' and python_version == '3.8'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.8'
    torchvision==0.5.0
    ```

1. 保存 *requirements.txt*，然后从 *start* 文件夹中运行以下命令来安装依赖项。


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

安装可能需要几分钟时间，在此过程中，你可以转到下一部分来修改函数。
> [!TIP]
> >在 Windows 上，可能会遇到“由于 EnvironmentError: [Errno 2]，无法安装包。没有此类文件或目录:”，后接类似于 *sharded_mutable_dense_hashtable.cpython-37.pyc* 的文件的长路径名。 通常，发生此错误的原因是文件夹路径的深度太长。 在这种情况下，请将注册表项 `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` 设置为 `1`，以启用长路径。 或者检查 Python 解释器的安装位置。 如果该位置的路径很长，请尝试在路径较短的文件夹中重新安装。

## <a name="update-the-function-to-run-predictions"></a>更新函数以运行预测

1. 在文本编辑器中打开 *classify/\_\_init\_\_.py*，并在现有的 `import` 语句后面添加以下行，以导入标准 JSON 库和 *predict* 帮助器：

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. 请将 `main` 函数的整个内容替换为以下代码：

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    此函数接收名为 `img` 的查询字符串参数中的图像 URL。 然后，它从帮助器库调用 `predict_image_from_url`，以使用 PyTorch 模型下载图像并对其进行分类。 然后，该函数返回包含结果的 HTTP 响应。

    > [!IMPORTANT]
    > 由于此 HTTP 终结点是由另一个域中托管的网页调用的，因此响应包含 `Access-Control-Allow-Origin` 标头，以满足浏览器的跨源资源共享 (CORS) 要求。
    >
    > 在生产应用程序中，请将 `*` 更改为网页的特定源，以提高安全性。

1. 保存更改，然后在假设依赖项已完成安装的条件下，再次使用 `func start` 启动本地函数宿主。 请务必在已激活虚拟环境的情况下运行 *start* 文件夹中的宿主。 否则，宿主将会启动，但在调用函数时会出现错误。

    ```
    func start
    ```

1. 在浏览器中打开以下 URL，以使用伯恩山犬图像的 URL 调用该函数，并确认返回的 JSON 是否将图像归类为伯恩山犬。

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
    ```

1. 让宿主保持运行，因为在下一步骤中需要使用它。

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>运行本地 Web 应用前端来测试函数

存储库的 *frontend* 文件夹中提供了一个简单的应用，让你从另一 Web 应用测试函数终结点的调用。

1. 打开新的终端或命令提示符，并激活虚拟环境（根据前文的[创建并激活 Python 虚拟环境](#create-and-activate-a-python-virtual-environment)中所述）。

1. 导航到存储库的 *frontend* 文件夹。

1. 使用 Python 启动一个 HTTP 服务器：

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    python -m http.server
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. 在浏览器中导航到 `localhost:8000`，然后在文本框中输入以下照片 URL 之一，或使用任何可公开访问的图像的 URL。

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. 选择“提交”以调用函数终结点来对图像进行分类。 

    ![已完成项目的屏幕截图](media/machine-learning-pytorch/screenshot.png)

    如果在提交图像 URL 时浏览器报告错误，请检查运行函数应用的终端。 如果出现类似于“找不到模块 'PIL'”的错误，原因可能是在 *start* 文件夹中启动了函数应用，但未事先激活先前创建的虚拟环境。 如果仍出现错误，请在已激活虚拟环境的情况下再次运行 `pip install -r requirements.txt`，并查看错误。

## <a name="clean-up-resources"></a>清理资源

由于整篇教程在计算机本地运行，因此没有任何 Azure 资源或服务需要清理。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何使用 Azure Functions 生成和自定义 HTTP API 终结点，以使用 PyTorch 模型对图像进行分类。 此外，你还了解了如何从 Web 应用调用 API。 可以使用本教程中所述的方法生成具有不同复杂性的 API，所有这些 API 都可在 Azure Functions 提供的无服务器计算模型上运行。

另请参阅：

- [使用 Visual Studio Code 将函数部署到 Azure](https://code.visualstudio.com/docs/python/tutorial-azure-functions)。
- [Azure Functions Python 开发人员指南](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [有关使用 Azure CLI 将函数部署到 Azure Functions 的指南](./functions-run-local.md#publish)
