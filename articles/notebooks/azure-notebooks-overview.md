---
title: Azure Notebooks 概述
description: 使用免费的 Azure Notebooks 服务在云中运行 Jupyter 笔记本，不需要进行设置或配置。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9cea5a8e-c52d-4bdc-9e4a-cecdc1ad02c1
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/11/2019
ms.author: kraigb
ms.openlocfilehash: ffceeeeb5a55b0fab1dd1cf91aebfcd4cfd852ef
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54848591"
---
# <a name="overview-of-azure-notebooks"></a>Azure Notebooks 概述

Azure Notebooks 是免费的托管服务，用于在云中开发和运行 Jupyter 笔记本而无需进行安装。 [Jupyter](https://jupyter.org/)（以前称为 IPython）是一个开放源代码项目，可让你将 Markdown 文本、可执行代码、持久性数据、图形和可视化效果轻松合并到单个可共享画布，即笔记本（jupyter.org 提供的图片）：

[![Jupyter 笔记本的示例](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

由于代码、图形和说明性文本的这种组合功能强大，因此 Jupyter 在许多方面已变得很常用，例如数据科学指令、数据清理和转换、数值模拟、统计建模，以及机器学习模型的开发。

## <a name="hassle-free-experience"></a>一站式体验

使用 Azure Notebooks 可以快速开始原型制作、数据科学、学术研究或学习 Python 编程：

- 数据科学家可以快速访问完整的 Anaconda 环境，不需安装。
- 教师可以为学生提供一站式 Python 环境。
- 演讲者可以进行类似的演讲或网络研讨会，不需要求参加者花 45 分钟的时间来安装软件。
- 开发人员或爱好者可以使用 Notebooks 作为一个快速的代码便签簿。

人们可以使用可通过浏览器访问的云服务（例如 Azure Notebooks（预览版））在 Notebooks 上进行协作，这样 Notebooks 的功能会变得更强大。 在云中，用户不需在本地安装 Jupyter，也不需担心如何维护一个环境。 云还可以简化你与其他经授权用户的笔记本（以及关联的数据文件）共享，这样就不需使用复杂的操作通过外部方式（例如源代码管理存储库）来共享笔记本。 使用 Azure Notebooks，用户还可以将笔记本复制（或“克隆”）到自己的帐户中进行修改或试验，这特别适用于指导。

由于 Azure Notebooks 是常规的代码创作、执行和共享平台，因此可以将它用于许多不同的场景：

- 学习新的编程语言 - 尝试 [Frontpage 教程](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb)之一
- 学习数据科学 - 试用 [Jake VanderPlas 的书籍](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook)
- 为数百学生[授课](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas)
- 以联机方式或会议方式提供网络研讨会，不需花时间进行安装 
- 允许 GitHub 用户直接加载并运行笔记本，方法是[创建 GitHub 启动徽章](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge)
- 在代码是可执行文件的情况下，提供[类似 PowerPoint 的幻灯片](https://notebooks.azure.com/help/jupyter-notebooks/slides)！

简而言之，Azure Notebooks 可以提高工作效率和生产力。

> [!Note]
> 有关 Jupyter 本身的详细信息，可查看 [jupyter.org](https://jupyter.org/) 和 [Jupyter 文档](https://jupyter-notebook.readthedocs.io/en/latest/)。

## <a name="pricing-and-quotas"></a>定价和配额

Azure Notebooks 是一项免费服务，但每个项目有 4GB 内存和 1GB 数据的限制，防止滥用。 超过这些限制的用户会看到 Captcha 质询，回答正确后即可继续运行笔记本。

若要解除所有限制，请通过使用 Azure Active Directory 的某个帐户（例如公司帐户）登录到 Azure Notebooks。 如果该帐户与某个 Azure 订阅相关联，则可连接到该订阅中的任何 Azure Data Science Virtual Machine 实例。 有关详细信息，请参阅[管理和配置项目 - 计算层](configure-manage-azure-notebooks-projects.md#compute-tier)。

## <a name="available-kernels-and-environments"></a>可用内核和环境

对于每个笔记本，请选择用于运行任何代码单元的内核（即运行时环境）。 Azure Notebooks 支持以下内核：

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0（即将弃用）
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Azure Notebooks 还包括除基础发行版之外的额外包。 例如，Python 内核包括 numpy、pandas、scikit-learn、matplotlib 和 bokeh 库。

也可自定义一个项目，为该项目中的所有笔记本创建一个环境。 有关详细信息，请参阅[快速入门：在自定义环境下创建项目](quickstart-create-jupyter-notebook-project-environment.md)。

除了基础发行版，Azure Notebooks 还预安装了许多额外的可供数据科学家使用的包。 也可使用每种语言的典型过程来安装你自己的包。

## <a name="pre-configured-jupyter-extensions"></a>预配置的 Jupyter 扩展

Azure Notebooks 预配置了以下 Jupyter 扩展：

- [RISE](https://github.com/damianavila/RISE)：Jupyter 幻灯片扩展（也称 live_reveal）。 有关详细信息，请参阅[运行笔记本幻灯片](present-jupyter-notebooks-slideshow.md)。
- [JupyterLab](https://github.com/jupyterlab/jupyterlab)：适用于 Jupyter 笔记本的完整计算环境。
- [Altair](https://github.com/ellisonbg/altair)：适用于 Python 的声明性统计可视化库。
- [BQPlot](https://github.com/bloomberg/bqplot)：适用于 Jupyter Notebooks 的交互式绘图框架。
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets)：适用于 Jupyter Notebooks 的交互式 HTML 小组件。

## <a name="issues-and-getting-help"></a>问题和帮助

由于 Azure Notebooks 仍为预览版，因此此服务出现临时中断的频率或时间长度可能会超出其他 Azure 服务。 某些功能可能不完整，或者包含 Bug。

目前，建议不要将 Azure Notebooks 预览版用于业务关键性应用程序或敏感的笔记本和数据。

若要讨论有关 Azure Notebooks 的问题，请将问题提交到 [GitHub 存储库](https://github.com/Microsoft/AzureNotebooks/issues)。

## <a name="next-steps"></a>后续步骤  

- [浏览示例笔记本](azure-notebooks-samples.md)

- 快速入门：

  - [创建和共享笔记本](quickstart-create-share-jupyter-notebook.md)
  - [克隆笔记本](quickstart-clone-jupyter-notebook.md)
  - [迁移本地 Jupyter 笔记本](quickstart-migrate-local-jupyter-notebook.md)
  - [使用自定义环境](quickstart-create-jupyter-notebook-project-environment.md)
  - [登录并设置用户 ID](quickstart-sign-in-azure-notebooks.md)

- 教程：

  - [创建和运行笔记本](tutorial-create-run-jupyter-notebook.md  )

- 操作指南文章：
  
  - [创建和克隆项目](create-clone-jupyter-notebooks.md)
  - [配置和管理项目](configure-manage-azure-notebooks-projects.md)
  - [从笔记本中安装包](install-packages-jupyter-notebook.md)
  - [演示幻灯片放映](present-jupyter-notebooks-slideshow.md)
  - [处理数据文件](work-with-project-data-files.md)
  - [访问数据资源](access-data-resources-jupyter-notebooks.md)
  - [使用 Azure 机器学习服务](use-machine-learning-services-jupyter-notebooks.md)
