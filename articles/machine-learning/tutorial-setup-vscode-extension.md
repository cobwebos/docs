---
title: 教程：设置 Azure 机器学习 Visual Studio Code 扩展
titleSuffix: Azure Machine Learning
description: 了解如何设置 Visual Studio Code Azure 机器学习扩展。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157458"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>设置 Azure 机器学习 Visual Studio Code 扩展

了解如何使用 Azure 机器学习 Visual Studio Code 扩展安装和运行脚本。

本教程将介绍以下任务：

> [!div class="checklist"]
> * 安装 Azure 机器学习 Visual Studio Code 扩展
> * 从 Visual Studio Code 登录到 Azure 帐户
> * 使用 Azure 机器学习扩展来运行示例脚本

## <a name="prerequisites"></a>必备条件

- Azure 订阅。 如果没有订阅，注册之后即可试用 [Azure 机器学习免费版或付费版](https://aka.ms/AMLFree)。
- 安装 [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview)，一种轻量型跨平台代码编辑器。 

## <a name="install-the-extension"></a>安装扩展

1. 打开 Visual Studio Code。
1. 从**活动栏**选择“扩展”  图标，打开“扩展”视图。
1. 在“扩展”视图中，搜索“Azure 机器学习”。
1. 选择“安装”  。

> [!NOTE]
> 也可[直接下载安装程序](https://aka.ms/vscodetoolsforai)，以便通过 Visual Studio Marketplace 安装 Azure 机器学习扩展。 

本教程中的其余步骤已使用扩展的**版本 0.6.8** 进行了测试。

## <a name="sign-in-to-your-azure-account"></a>登录 Azure 帐户

若要在 Azure 上预配资源并运行工作负载，必须使用 Azure 帐户凭据登录。 Azure 机器学习会自动安装 Azure 帐户扩展，帮助你进行帐户管理。 请访问以下站点，[详细了解 Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。

1. 从菜单栏选择“视图”>“命令面板”，打开命令面板。  
1. 将命令“Azure:登录”输入文本框，启动登录过程。

## <a name="run-a-script-in-azure"></a>在 Azure 中运行脚本

使用帐户凭据登录到 Azure 后，即可执行此部分中的步骤，了解如何使用该扩展训练机器学习模型。

1. 在计算机上的任何位置下载并解压缩 [VS Code Tools for AI 存储库](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip)。
1. 在 Visual Studio Code 中打开 `mnist-vscode-docs-sample` 目录。
1. 在活动栏中选择“Azure”图标。 
1. 选择“Azure 机器学习”视图顶部的“运行试验”  图标。

    > [!div class="mx-imgBorder"]
    > ![运行试验](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. 命令面板展开后，请按提示操作。

    1. 选择 Azure 订阅。
    1. 选择“创建新的 Azure ML 工作区” 
    1. 选择“TensorFlow 单节点训练”  作业类型。
    1. 输入 `train.py` 作为要训练的脚本。 这是一个文件，其中包含机器学习模型的代码，用于对手写数字的图像分类。
    1. 将要运行的以下包指定为要求。

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. 此时会在文本编辑器中显示如下所示的配置文件。 此配置包含运行训练作业所需的信息，例如，包含训练模型所需的代码以及在上一步指定的任何 Python 依赖项的文件。

    ```json
    {
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. 选择“提交试验”，  在 Azure 中运行试验。 这样就会将 `train.py` 和配置文件发送到 Azure 机器学习工作区。 然后就会在 Azure 中的计算资源上启动训练作业。
1. 几分钟后，会在本地创建一个名为 `output` 的目录，其中包含经训练的 TensorFlow 模型。

## <a name="next-steps"></a>后续步骤

* [教程：使用 Azure 机器学习 Visual Studio Code 扩展训练和部署图像分类 TensorFlow 模型](tutorial-train-deploy-image-classification-model-vscode.md)。
