---
title: 有关用于 Azure 上的机器学习的 Visual Studio Tools 的快速入门文章 | Microsoft Docs
description: 本文介绍了如何开始使用用于机器学习的 Visual Studio Tools，包括创建试验、训练模型以及操作 Web 服务。
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: 735bfd911a738d4e71974f5cdcd94afa50acc502
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932976"
---
# <a name="visual-studio-tools-for-ai"></a>用于 AI 的 Visual Studio Tools
用于 AI 的 Visual Studio Tools 是一个开发扩展，用于生成、测试和部署深度学习/AI 解决方案。 它具有与 Azure 机器学习的无缝集成，尤其是运行历史记录视图，其中详细列出了以前的训练和自定义指标的性能。 它还提供了一个示例资源管理器视图，允许通过 [Microsoft Cognitive Toolkit（以前称为 CNTK）](http://www.microsoft.com/en-us/cognitive-toolkit)、[Google TensorFlow](https://www.tensorflow.org) 和其他深度学习框架浏览和启动新项目。 最后，它针对计算目标提供了一个资源管理器，可以使用该资源管理器提交作业以在远程环境（例如 Azure 虚拟机或具有 GPU 的 Linux 服务器）中训练模型。 它还可用于快速访问 [Azure Batch AI（预览版）](https://docs.microsoft.com/azure/batch-ai/)。
 
## <a name="getting-started"></a>入门 
若要开始，首先需下载并安装 [Visual Studio](https://www.visualstudio.com/downloads/)。 打开 Visual Studio 后，执行以下步骤：
1. 单击 Visual Studio 中的菜单栏上的“工具”，选择“扩展和更新...”
2. 单击“联机”选项卡，选择“搜索 Visual Studio Marketplace”。
3. 搜索“用于 AI 的 Visual Studio”。 
3. 单击“**下载**”按钮。 
4. 安装后重启 Visual Studio。 

在重载 Visual Studio 后，该扩展被激活。 [详细了解如何查找扩展](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions)。

> [!NOTE]
> 用于 AI 的 Visual Studio Tools 需要 Visual Studio 2015 或 2017 的专业版或企业版。 它不支持 Apple OSX 版本。 


## <a name="exploring-project-samples"></a>浏览项目示例
用于 AI 的 Visual Studio Tools 提供了一个示例浏览器。 通过示例浏览器，可以轻松发现示例，并且仅需单击几下便可试用这些示例。 若要打开该浏览器，请按以下步骤执行操作：   
1. 在菜单栏中，单击“AI Tools”。
2. 单击“Azure 机器学习库”。

此时会打开包含所有 Azure ML 示例的选项卡。

## <a name="creating-a-new-project-from-the-sample-explorer"></a>从示例浏览器中创建一个新项目 
可以浏览不同示例并获取有关它们的详细信息。 一直浏览，直至找到“Classifying Iris”示例。 若要基于此示例创建新项目，请按以下步骤操作：
1. 单击项目示例上的“安装”按钮，此时会打开一个新对话框。 
2. 选择资源组、帐户和工作区。
3. 项目类型可以保留为“常规”。
4. 输入项目路径和项目名称，然后按 Enter。 
5. 此时会打开一个提示保存解决方案的对话框，请单击“保存”。 

完成后，会在 Visual Studio 的新实例中打开新项目。 

> [!TIP]
> 需要登录才能访问 Azure 资源。 从嵌入的终端中，输入“az login”，并按照说明进行操作。 

## <a name="submitting-experiment-with-the-new-project"></a>使用新项目提交试验
新项目在 Visual Studio 中打开后，向计算目标（本地目标或装有 Docker 的 VM）提交作业。
若要提交作业，请执行以下操作： 
1. 在解决方案资源管理器中右键单击要提交的文件，然后选择“设为启动文件”。
2. 选择项目名称，然后右键单击并选择“提交作业...”。
3. 此时会打开新对话框，用于选择要执行脚本的群集（或计算目标）。
4. 单击“提交”

在提交作业后，嵌入的终端会显示运行进度。

## <a name="view-list-of-jobs"></a>作业的视图列表
提交作业后，即可在运行历史记录中列出作业。
1. 在“服务器资源管理器”中，单击“AI Tools”。
2. 然后选择“Azure 机器学习”
3. 单击“作业”菜单。

此时作业资源管理器会列出此项目的所有已提交试验。 

## <a name="view-job-details"></a>查看作业详细信息
在作业资源管理器视图打开的情况下，单击列表中的第一个运行。
此时会加载“作业摘要”面板，以及“日志和输出”面板。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [如何配置 Azure 机器学习以使用 IDE](./how-to-configure-your-IDE.md)
