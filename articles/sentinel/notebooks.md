---
title: 在 Azure Sentinel 上使用笔记本进行安全搜寻
description: 本文介绍如何使用笔记本和 Azure Sentinel 搜寻功能。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: ded332813a840892f640aa6f6e48debbfe381b4b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889339"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter 笔记本搜寻安全威胁

Azure Sentinel 的基础是数据存储;它结合了高性能查询、动态架构和扩展到大数据量。 Azure 门户和所有 Azure Sentinel 工具都使用一个公共 API 来访问此数据存储。 同一 API 也可用于 [Jupyter](https://jupyter.org/) 笔记本和 Python 等外部工具。 虽然可以在门户中执行许多常见任务，但 Jupyter 扩展了可对此数据执行的操作范围。 它将完整的可编程性与用于机器学习、可视化效果和数据分析的大量库集合组合在一起。 这些属性使 Jupyter 成为安全调查和搜寻的引人注目的工具。

![示例笔记本](./media/notebooks/sentinel-notebooks-map.png)

我们已将 Jupyter 体验集成到 Azure 门户中，使你可以轻松地创建和运行笔记本来分析数据。 Kqlmagic 库提供了一种连接，使你可以从 Azure Sentinel 获取查询并直接在笔记本中运行它们。 查询使用 [Kusto 查询语言](https://kusto.azurewebsites.net/docs/query/index.html)。 Microsoft 的某些安全分析师开发的几个笔记本打包在 Azure Sentinel 中。 其中一些笔记本专门用于特定方案，可以按原样使用。 其他笔记本用作示例，以说明可以复制或修改以便在自己的笔记本中使用的技术和功能。 其他笔记本还可以从 Azure Sentinel 社区 GitHub 导入。

集成的 Jupyter 体验使用 [Azure Notebooks](https://notebooks.azure.com/) 存储、共享和执行笔记本。 如果你的计算机上或其他 JupterHub 环境（如 Azure Databricks）有 Python 环境和 Jupyter，也可以在本地运行这些笔记本。

笔记本包括两个组件：

- 基于浏览器的界面，你可以在其中输入和运行查询和代码，执行结果也显示在此处。
- 一个内核，负责分析和执行代码本身。

Azure Sentinel 笔记本的内核 (VM) 上的 Azure 虚拟机上运行。 如果你的笔记本包括复杂的机器学习模型，则可以使用多种许可选项来利用功能更强大的虚拟机。

Azure Sentinel 笔记本使用许多常见的 Python 库，例如 pandas、matplotlib、bokeh 等。 还提供了许多其他 Python 包供你选择，涵盖的领域包括：

- 可视化效果和图形
- 数据处理和分析
- 统计和数字计算
- 机器学习和深度学习

我们还在 [msticpy](https://github.com/Microsoft/msticpy/) 包中发布了一些开放源代码 Jupyter 安全工具。 此包可用于许多随附的笔记本。 Msticpy 工具专为帮助创建用于搜寻和调查的笔记本而设计，我们正在积极地致力于提供新功能和改进。

[Azure Sentinel GitHub 社区存储库](https://github.com/Azure/Azure-Sentinel)是存储任何未来的 Azure Sentinel 笔记本的位置，这些笔记本由 Microsoft 生成或由社区提供。

若要使用笔记本，必须首先创建一个 Azure 机器学习 (ML) "工作区"。

## <a name="create-an-azure-ml-workspace"></a>创建 Azure ML 工作区

1. 在 Azure 门户中，导航到 " **Azure Sentinel**  >  **威胁管理**  >  **笔记本**"，然后选择 "**启动笔记本**"。

    > [!div class="mx-imgBorder"]
    > ![启动笔记本以启动 azure ml 工作区](./media/notebooks/sentinel-notebooks-launch.png)

1. 在 " **AzureML 工作区**" 下，选择 " **新建**"。

    > [!div class="mx-imgBorder"]
    > ![创建工作区](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. 在 " **机器学习** " 页上，提供以下信息，然后选择 " **查看 + 创建**"。

    |字段|说明|
    |--|--|
    |订阅|选择要使用的 Azure 订阅。|
    |资源组|使用订阅中的现有资源组，或者输入一个名称以创建新的资源组。 资源组保存 Azure 解决方案的相关资源。 在此示例中，我们使用 **AzureMLRG**。|
    |工作区名称|输入用于标识工作区的唯一名称。 在此示例中，我们使用 **testworkspace1**。 名称在整个资源组中必须唯一。 使用易于记忆且区别于其他人所创建工作区的名称。|
    |区域|选择离你的用户和数据资源最近的位置来创建工作区。|
    |工作区版本|在此示例中选择 " **基本** " 作为工作区类型。 工作区类型 (基本 & Enterprise) 确定要访问的功能和定价。|

    > [!div class="mx-imgBorder"]
    > ![提供工作区详细信息](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. 查看信息，验证它是否正确，然后选择 " **创建** " 以开始部署工作区。

    > [!div class="mx-imgBorder"]
    > ![查看工作区详细信息](./media/notebooks/sentinel-notebooks-azureml-review.png)

    在云中创建工作区可能需要几分钟的时间，在此期间，" **概述** " 页将显示当前的部署状态。

    > [!div class="mx-imgBorder"]
    > ![工作区部署](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

部署完成后，可以在新的 Azure ML 工作区中启动笔记本。

> [!div class="mx-imgBorder"]
> ![工作区部署成功](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>使用 Azure ML 工作区启动笔记本

1. 在 Azure 门户中，导航到“Azure Sentinel” > “威胁管理” > “笔记本”，可在此处看到 Azure Sentinel 提供的笔记本  。

    > [!TIP]
    > 选择 " **指南" & "反馈** "，以打开一个窗格，其中包含有关笔记本的其他帮助和指南。
    > ![查看笔记本指南](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. 选择各个笔记本以查看其说明、所需的数据类型和数据源。

    > [!div class="mx-imgBorder"]
    > ![查看笔记本详细信息](./media/notebooks/sentinel-azure-notebooks-view.png)

1. 选择要使用的笔记本，然后选择 " **启动笔记本** 以克隆"，并将笔记本配置为连接到 Azure Sentinel 工作区的新 Azure Notebooks 项目。 完成此过程后，将在 Azure Notebooks 中打开笔记本以供运行。

    > [!div class="mx-imgBorder"]
    > ![选择笔记本](./media/notebooks/sentinel-azure-notebooks-select.png)

1. 在 "AzureML 工作区" 下，选择 Azure ML 工作区，并选择 " **启动**"。

    > [!div class="mx-imgBorder"]
    > ![启动笔记本](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. 选择计算实例。 如果没有计算实例，请执行以下操作：
    1. 选择加号 (+) 以启动 " **新建计算实例** " 向导。

        > [!div class="mx-imgBorder"]
        > ![启动计算实例向导](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. 在 " **新建计算实例** " 页上，提供所需的信息，然后选择 " **创建**"。

        > [!div class="mx-imgBorder"]
        > ![创建计算实例](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. 创建笔记本服务器后，在每个单元中，选择 "运行" 图标以在笔记本中执行代码。

    > [!div class="mx-imgBorder"]
    > ![运行笔记本](./media/notebooks/sentinel-azure-notebooks-run.png)

建议：

- 若要快速了解如何在 Azure Sentinel 中查询数据，请参阅 [使用 AZURE ML 笔记本和 Azure sentinel 指南入门](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) 。

- 你将在 [**示例-笔记本**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) GitHub 子文件夹中找到其他示例笔记本。 这些示例笔记本与数据一起保存，因此更易于查看预期的输出。 建议在 [nbviewer](https://nbviewer.jupyter.org/) 中查看这些笔记本。

- [**操作方法**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos)GitHub 子文件夹包含以下内容：设置默认 Python 版本、配置 DSVM、从笔记本创建 Azure 标记书签和其他主题。

提供的笔记本旨在用作有用的工具以及举例说明和代码示例，以供你开发自己的笔记本。

欢迎你提供宝贵意见，包括建议、针对功能的请求、对现有笔记本的 bug 报告或改进和补充。 请前往 [Azure Sentinel GitHub 社区](https://github.com/Azure/Azure-Sentinel)创建问题或分支并上传贡献。

## <a name="next-steps"></a>后续步骤

本文介绍了如何开始在 Azure Sentinel 中使用 Jupyter 笔记本。 要详细了解 Azure Sentinel，请参阅以下文章：

- [主动搜寻威胁](hunting.md)
- [在搜寻时使用书签保存感兴趣的信息](bookmarks.md)
