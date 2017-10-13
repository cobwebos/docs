---
title: "有关用于 Azure 上的机器学习的 Visual Studio Code 工具的快速入门文章 | Microsoft Docs"
description: "本文介绍了如何开始使用用于机器学习的 Visual Studio Code 工具，包括创建试验、训练模型以及操作 web 服务。"
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: get-started-article
ms.date: 09/12/2017
ms.openlocfilehash: 680c1afab1af31cfef51b1c82d2db49f452ba6ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="visual-studio-code-tools-for-ai"></a>用于 AI 的 Visual Studio Code 工具
用于 AI 的 Visual Studio Code 工具是一个开发扩展，用于生成、测试和部署深度学习 / AI 解决方案。 它具有与 Azure 机器学习的无缝集成，尤其是运行历史记录视图，其中详细列出了以前的训练和自定义指标的性能。 它还提供了一个示例资源管理器视图，允许通过 [Microsoft Cognitive Toolkit（以前称为 CNTK）](http://www.microsoft.com/en-us/cognitive-toolkit)、[Google TensorFlow](https://www.tensorflow.org) 和其他深度学习框架浏览和启动新项目。 最后，它针对计算目标提供了一个资源管理器，可以使用该资源管理器提交作业以在远程环境（例如 Azure 虚拟机或具有 GPU 的 Linux 服务器）中训练模型。 
 
## <a name="getting-started"></a>入门 
若要开始，首先需要下载并安装 [Visual Studio Code](https://code.visualstudio.com/Download)。 打开 Visual Studio Code 后，执行以下操作：
1. 单击活动栏中的扩展图标。 
2. 搜索“用于 AI 的 Visual Studio Code 工具”。 
3. 单击“安装”按钮。 
4. 在安装后，单击“重载”按钮。 

在重载 Visual Studio Code 后，该扩展被激活。 [详细了解如何安装扩展](https://code.visualstudio.com/docs/editor/extension-gallery)。

## <a name="exploring-project-samples"></a>浏览项目示例
用于 AI 的 Visual Studio Code 工具提供了一个示例浏览器。 通过示例浏览器，可以轻松发现示例，并且仅需单击几下便可试用这些示例。 若要打开浏览器，请如下所述执行操作：   
1. 打开命令面板（视图 > **命令面板**或者 **Ctrl+Shift+P**）。
2. 输入“AI Sample”。 
3. 此时会出现“AI: Open Azure ML Sample Explorer”建议，选择该建议并按 Enter。 

另外，还可以单击示例浏览器按钮。

## <a name="creating-a-new-project-from-the-sample-explorer"></a>从示例浏览器中创建一个新项目 
可以浏览不同示例并获取有关它们的详细信息。 一直浏览，直至找到“Classifying Iris”示例。 若要基于此示例创建新项目，请执行以下操作：
1. 单击项目示例上的“安装”按钮，注意所提示的命令，这些命令将引导你完成创建新项目的步骤。 
2. 为项目选择一个名称，例如“Iris”。
3. 选择一个文件夹路径以创建项目并按 Enter。 
4. 选择一个现有的工作区并按 Enter。

随即会创建该项目。

> [!TIP]
> 需要登录才能访问 Azure 资源。 从嵌入的终端中，输入“az login”，并按照说明进行操作。 

## <a name="submitting-experiment-with-the-new-project"></a>使用新项目提交试验
新项目在 Visual Studio Code 中打开，我们将向不同的计算目标（本地和具有 docker 的 VM）提交作业。
用于 AI 的 Visual Studio Code 工具提供了多种用来提交试验的方法。 
1. 上下文菜单（右键单击）- **AI: 提交作业**。
2. 从命令面板中：“AI: Submit Job”。
3. 另外，还可以直接使用 Azure CLI、机器学习命令以及使用嵌入的终端来运行命令。

打开 iris_sklearn.py，单击右键并选择“AI: 提交作业”。
1. 选择你的平台：“Azure 机器学习”。
2. 选择你的运行配置：“Docker-Python”。

> [!NOTE]
> 如果是首次提交作业，则会收到消息“未找到机器学习配置，正在创建...”。 随即会打开一个 JSON 文件，将其保存 (**Ctrl+S**)。

在提交作业后，嵌入的终端会显示运行进度。 

## <a name="view-list-of-jobs"></a>作业的视图列表
在提交作业后，可以从运行历史记录中列出作业。
1. 打开命令面板（视图 > **命令面板**或者 **Ctrl+Shift+P**）。
2. 输入“AI List”。
3. 此时会出现“AI: List Jobs”建议，选择该建议并按 Enter。
4. 选择平台“Azure 机器学习”。

作业列表视图将打开，其中会显示所有运行和一些相关信息。

## <a name="view-job-details"></a>查看作业详细信息
在作业列表视图保持打开的情况下，单击列表中的第一个运行。
若要深入研究某个作业的结果，请单击顶部的**作业 ID** 来查看详细信息。 

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [如何配置 Azure 机器学习以使用 IDE](./how-to-configure-your-IDE.md)
