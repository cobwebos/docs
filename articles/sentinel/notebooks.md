---
title: 使用 Azure 中的笔记本进行搜寻功能 |Microsoft Docs
description: 本文介绍如何通过 Azure Sentinel 搜寻功能使用笔记本。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2019
ms.author: rkarlin
ms.openlocfilehash: 1b78f6f0773e114a4dda536213e2684d8fbd706d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483281"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter 笔记本查找安全威胁

Azure Sentinel 的基础是数据存储;它结合了高性能查询、动态架构和扩展到大数据量。 Azure 门户和所有 Azure Sentinel 工具都使用一个公共 API 来访问此数据存储。 同一 API 也可用于[Jupyter](https://jupyter.org/)笔记本和 Python 等外部工具。 虽然可以在门户中执行许多常见任务，但 Jupyter 扩展了可对此数据执行的操作的范围。 它将完整的可编程性与用于机器学习、可视化和数据分析的大量库组合在一起。 这些属性使 Jupyter 成为安全调查和搜寻的引人注目的工具。

![示例笔记本](./media/notebooks/sentinel-notebooks-map.png)

我们已将 Jupyter 体验集成到 Azure 门户中，使你可以轻松地创建和运行用于分析数据的笔记本。 *Kqlmagic*库提供了胶水，可让你从 Azure Sentinel 获取查询并直接在笔记本中运行它们。 查询使用[Kusto 查询语言](https://kusto.azurewebsites.net/docs/query/index.html)。 Microsoft 的某些安全分析师开发的几个笔记本使用 Azure Sentinel 打包。 其中一些笔记本是针对特定方案而构建的，可以按原样使用。 其他功能旨在作为示例来说明可以复制或改编以便在自己的笔记本中使用的技术和功能。 其他笔记本还可以从 Azure Sentinel 社区 GitHub 导入。

集成的 Jupyter 体验使用[Azure Notebooks](https://notebooks.azure.com/)来存储、共享和执行笔记本。 如果你的计算机上或其他 JupterHub 环境（如 Azure Databricks）具有 Python 环境和 Jupyter，也可以在本地运行这些笔记本。

笔记本有两个组件：

- 基于浏览器的界面，您可以在其中输入和运行查询和代码，以及执行结果的显示位置。
- 一个*内核*，负责分析和执行代码本身。 

在 Azure Notebooks 中，默认情况下，此内核在 Azure*免费云计算和存储*中运行。 如果你的笔记本包括复杂的机器学习模型或可视化效果，则考虑使用更强大的专用计算资源，如[数据科学虚拟机](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)（DSVM）。 你的帐户中的笔记本将保密，除非你选择共享它。

Azure Sentinel 笔记本使用许多常用的 Python 库，如 pandas、matplotlib、bokeh 等。 还有很多其他 Python 包供你选择，包括以下方面：

- 可视化效果和图形
- 数据处理和分析
- 统计信息和数字计算
- 机器学习和深度学习

我们还在名为[msticpy](https://github.com/Microsoft/msticpy/)的包中发布了一些开源 Jupyter 安全工具。 此软件包用于许多随附的笔记本。 Msticpy 工具专为帮助创建用于搜寻和调查的笔记本而设计，并且我们正在积极地致力于提供新的功能和改进。

初始笔记本包括：

- **引导式调查-过程警报**：允许您通过分析受影响的主机上的活动快速会审警报。
- **指导性搜寻-Windows 主机资源管理器**：允许您在主机上浏览帐户活动、进程执行、网络活动和其他事件。
- **引导式 Office365：探索**多个 office 365 数据集中的可疑 Office 365 活动。

[Azure Sentinel 社区 GitHub 存储库](https://github.com/Azure/Azure-Sentinel)是适用于任何将来由 Microsoft 生成或由社区提供的 azure sentinel 笔记本的位置。

若要使用这些笔记本，你必须有一个 Azure Notebooks 帐户。 有关详细信息，请参阅[快速入门：登录并设置](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)Azure Notebooks 文档中的用户 ID。 若要创建此帐户，可以使用**Azure Sentinel-笔记本**中命令栏中的 "**注册 Azure Notebooks** " 选项：

> [!div class="mx-imgBorder"]
>![注册 Azure Notebooks 选项](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

可以直接从 Azure Sentinel 运行笔记本，或将所有 Azure Sentinel 笔记本克隆到新的 Azure Notebooks 项目。

## <a name="run-a-notebook-from-azure-sentinel"></a>从 Azure Sentinel 运行笔记本
 
1. 在 Azure 门户中，导航到**Azure sentinel** > **威胁管理** > **笔记本**，可在其中查看 azure sentinel 提供的笔记本。 

2. 选择各个笔记本以阅读其说明、所需的数据类型和数据源。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![启动笔记本](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. 选择要使用的笔记本，然后选择 "**启动笔记本（预览）** "，将笔记本克隆并将其配置为连接到 Azure Sentinel 工作区的新 Azure Notebooks 项目。 完成此过程后，将在 Azure Notebooks 中打开笔记本以供运行。

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>将 Azure Sentinel 笔记本克隆到新的 Azure Notebooks 项目

此过程将为你创建一个 Azure Notebooks 项目，其中包含 Azure Sentinel 笔记本。 然后，你可以按原样运行笔记本，或者对其进行更改，然后运行它们。

1. 在 Azure 门户中，导航到**Azure Sentinel** > "**威胁管理**" > **笔记本**，然后从命令栏中选择 "**克隆笔记本**"：
  
    > [!div class="mx-imgBorder"]
    >![克隆笔记本 "选项](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. 显示以下对话框时，选择 "**导入**"，将 GitHub 存储库克隆到 Azure Notebooks 项目。 如果没有现有 Azure Notebooks 帐户，系统会提示创建一个帐户并登录。

   ![导入笔记本](./media/notebooks/sentinel-notebooks-clone.png)

3. 在 "**上传 GitHub 存储库**" 对话框中，请不要选择 "**以递归方式克隆**"，因为此选项是指链接的 GitHub 存储库。 对于 "项目名称"，请使用默认名称或键入新名称。 然后单击 "**导入**" 以开始克隆 GitHub 内容，这可能需要几分钟才能完成。

   ![导入笔记本](./media/notebooks/sentinel-create-project.png)

4. 打开刚刚创建的项目，然后打开 "**笔记本**" 文件夹查看笔记本。 例如：

   ![导入存储库](./media/notebooks/sentinel-open-notebook1.png)

然后，你可以从 Azure Notebooks 运行笔记本。 若要从 Azure Sentinel 返回到这些笔记本，**请从** **Azure sentinel-笔记本**：

> [!div class="mx-imgBorder"]
>![中转到笔记本选项](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>使用笔记本进行查寻

每个笔记本将引导你完成进行查寻或调查的步骤。 笔记本中所需的库和其他依赖项可以通过笔记本或简单的配置过程进行安装。 在前面的步骤中，将自动预配将笔记本项目重新绑定到 Azure Sentinel 订阅的配置。

1. 如果尚未 Azure Notebooks，可以使用**Azure Sentinel-笔记本**中命令栏的 "从以下位置**中转到你的笔记本**" 选项：
    
    > [!div class="mx-imgBorder"]
    >![中转到笔记本选项](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    在 Azure Notebooks 中，选择 "**我的项目**"，然后选择包含 Azure Sentinel 笔记本的项目，最后选择 "**笔记本**" 文件夹。
    
2. 在打开笔记本之前，请注意，默认情况下，选择 "免费计算" 运行笔记本：
    
   ![选择笔记本](./media/notebooks/sentinel-open-notebook2.png)
    
    如果已配置数据科学虚拟机（DSVM），如简介中所述，请在打开第一个笔记本之前选择 DSVM 并进行身份验证。 

3. 选择要打开的笔记本。
    
    首次打开笔记本时，系统可能会提示你选择内核版本。 如果没有提示，可以从**内核** >  **更改内核**中选择内核版本，然后选择至少为3.6 的版本。 所选内核版本将显示在笔记本窗口的右上方：
    
   ![选择笔记本](./media/notebooks/sentinel-select-kernel.png)

4. 在对下载的笔记本进行任何更改之前，最好是创建原始笔记本的副本并进行复制。 为此，请选择 "**文件**" > "**创建副本**"。 使用副本，可以安全地更新到未来版本的笔记本，而不会覆盖任何数据。
    
    你现在已准备好运行或编辑选定的笔记本。

建议：

- 若要快速了解如何在 Azure Sentinel 中查询数据，请查看主**笔记本**文件夹中的[GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb)笔记本。 

- 你将在**示例-笔记本**子文件夹中找到其他示例笔记本。 这些示例笔记本与数据一起保存，因此更易于查看预期的输出。 建议在[nbviewer](https://nbviewer.jupyter.org/)中查看这些笔记本。 

- **操作方法**文件夹包含用于说明的笔记本，例如：设置默认 Python 版本、配置 DSVM、从笔记本创建 Azure 标记书签和其他主题。

提供的笔记本旨在作为有用的工具，以及可用于开发自己的笔记本的插图和代码示例。

我们欢迎您提供反馈，无论是建议、针对功能的请求、对现有笔记本的 bug 报告或改进。 请通过[Azure Sentinel 社区 GitHub](https://github.com/Azure/Azure-Sentinel)来创建问题或分叉，并上传发布内容。

## <a name="next-steps"></a>后续步骤

本文介绍了如何开始在 Azure Sentinel 中使用 Jupyter 笔记本。 要详细了解 Azure Sentinel，请参阅以下文章：

- [主动寻找威胁](hunting.md)
- [在搜寻时使用书签保存感兴趣的信息](bookmarks.md)
