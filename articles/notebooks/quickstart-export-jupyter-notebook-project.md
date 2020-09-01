---
title: 从 Azure Notebooks 预览版导出 Jupyter Notebook 项目
description: 快速导出 Jupyter Notebook 项目。
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: a2e76f2c1956df4d0de59cfb429dc1c9dce85a62
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892093"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>快速入门：导出 Azure Notebooks 预览版中的 Jupyter Notebook 项目

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

在本快速入门中，你将下载一个 Azure Notebooks 项目，以在其他 Jupyter Notebook 解决方案中使用。 

## <a name="prerequisites"></a>先决条件

现有的 Azure Notebooks 项目。

## <a name="export-an-azure-notebooks-project"></a>导出 Azure Notebooks 项目

1. 转到 [Azure Notebooks](https://notebooks.azure.com) 并登录。 有关详细信息，请参阅[快速入门 - 登录 Azure Notebooks](quickstart-sign-in-azure-notebooks.md)。

1. 在公用个人资料页面中，选择页面顶部的“我的项目”：

    ![浏览器窗口顶部的“我的项目”链接](media/quickstarts/my-projects-link.png)

1. 选择一个项目。
1. 单击“下载”按钮，以触发包含所有项目文件的 zip 文件下载。
1. 或者，在特定项目页面中，单击“下载项目”按钮以下载给定项目的所有文件。

下载项目文件后，可以将其与其他 Jupyter 笔记本解决方案一起使用。 以下各部分中介绍的一些选项包括： 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces)
- [Azure 机器学习](#use-notebooks-with-azure-machine-learning)
- [Azure 实验室服务](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>为笔记本创建环境

如果要创建与 Azure Notebooks 预览版的环境匹配的环境，则可以使用 GitHub 中提供的脚本文件。

1. 导航到 Azure Notebooks [GitHub 存储库](https://github.com/microsoft/AzureNotebooks)或[直接访问环境文件夹](https://aka.ms/aznbrequirementstxt)。
1. 在命令提示符下，导航到要用于项目的目录。
1. 下载环境文件夹的内容，并按照自述文件说明安装 Azure Notebooks 包依赖项。


## <a name="use-notebooks-in-visual-studio-code"></a>在 Visual Studio Code 中使用 Notebooks

[VS Code](https://code.visualstudio.com/) 是可在本地使用或连接到远程计算的免费代码编辑器。 结合 Python 扩展，它为 Python 开发提供了完整的环境，包括使用 Jupyter Notebooks 的丰富本机体验。 

![VS Code Jupyter Notebook 支持](media/vs-code-jupyter-notebook.png)

[下载](#export-an-azure-notebooks-project)项目文件后，可以将其与 VS Code 一起使用。 有关将 VS Code 与 Jupyter Notebook 一起使用的指南，请参阅[在 Visual Studio Code 中使用 Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support) 和 [Visual Studio Code 中的数据科学](https://code.visualstudio.com/docs/python/data-science-tutorial)教程。

你还可以将 [Azure Notebooks 环境脚本](#create-an-environment-for-notebooks)与 Visual Studio Code 一起使用，以创建与 Azure Notebooks 预览版相匹配的环境。

## <a name="use-notebooks-in-visual-studio-codespaces"></a>在 Visual Studio Codespace 中使用 Notebooks

Visual Studio Codespace 提供了云托管环境，你可以在其中使用 Visual Studio Code 或 Web 浏览器来编辑笔记本。 它提供与 VS Code 相同的出色 Jupyter 体验，但无需在设备上安装任何内容。 如果不想设置本地环境并希望使用支持云的解决方案，则最好选择创建 Codespace。 开始操作：

1. [下载](#export-an-azure-notebooks-project)项目文件。
1. [创建 GitHub 存储库](https://help.github.com/github/getting-started-with-github/create-a-repo)来存储笔记本。 
1. [将文件添加](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository)到存储库。
1. 在[浏览器](https://docs.microsoft.com/visualstudio/online/how-to/browser)、[Visual Studio](https://docs.microsoft.com/visualstudio/online/how-to/vside) 或 [Visual Studio Code](https://docs.microsoft.com/visualstudio/online/how-to/vscode) 中设置 Visual Studio Codespace。

## <a name="use-notebooks-with-azure-machine-learning"></a>将 Notebooks 与 Azure 机器学习一起使用

Azure 机器学习提供了端到端机器学习平台，使用户能够在 Azure 上更快地生成和部署模型。 借助 Azure ML，你可以在 VM 或共享群集计算环境上运行 Jupyter Notebook。 如果需要基于云的解决方案来处理包括试验跟踪、数据集管理等 ML 工作负载，我们建议使用 Azure 机器学习。 若要开始使用 Azure ML，请执行以下操作：

1. [下载](#export-an-azure-notebooks-project)项目文件。
1. 在 Azure 门户中[创建工作区](../machine-learning/how-to-manage-workspace.md)。

   ![创建工作区](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. 打开 [Azure Studio（预览版）](https://ml.azure.com/)。
1. 使用左侧导航栏，选择“Notebooks”。
1. 单击“上传文件”按钮，然后上传从 Azure Notebooks 下载的项目文件。

有关 Azure ML 和运行 Jupyter Notebook 的其他信息，可以查看[文档](../machine-learning/how-to-run-jupyter-notebooks.md)或尝试使用 Microsoft Learn 上的[机器学习简介](https://docs.microsoft.com/learn/modules/intro-to-azure-machine-learning-service/)模块。


## <a name="use-azure-lab-services"></a>使用 Azure 实验室服务

[Azure 实验室服务](https://azure.microsoft.com/services/lab-services/)使教师可以轻松为整个教室设置并提供对预配置 VM 的按需访问。 如果你正在寻找一种在定制的教室环境中使用 Jupyter Notebook 和云计算的方法，则实验室服务是一个不错的选择。

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 [下载](#export-an-azure-notebooks-project)项目文件后，可以将其与 Azure 实验室服务一起使用。 有关设置实验室的指导，请参阅[设置实验室以使用 Python 和 Jupyter Notebook 讲授数据科学](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>使用 GitHub

GitHub 提供了一种免费的、由源代码管理支持的方式来存储笔记本（和其他文件）、与他人共享笔记本以及进行协作。 如果你正在寻找一种共享项目并与他人协作的方法，那么 GitHub 是一个不错的选择，可以将它与 [Visual Studio Codespace](#use-notebooks-in-visual-studio-codespaces) 结合使用以获得出色的开发体验。 开始使用 GitHub

1. [下载](#export-an-azure-notebooks-project)项目文件。
1. [创建 GitHub 存储库](https://help.github.com/github/getting-started-with-github/create-a-repo)来存储笔记本。 
1. [将文件添加](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository)到存储库。

## <a name="next-steps"></a>后续步骤

- [了解 Visual Studio Code 中的 Python](https://code.visualstudio.com/docs/python/python-tutorial)
- [了解 Azure 机器学习和 Jupyter Notebook](../machine-learning/how-to-run-jupyter-notebooks.md)
- [了解 Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/)
- [了解 Azure 实验室服务](https://azure.microsoft.com/services/lab-services/)
- [了解 GitHub](https://help.github.com/github/getting-started-with-github/)
