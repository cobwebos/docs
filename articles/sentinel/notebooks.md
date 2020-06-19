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
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652028"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter 笔记本搜寻安全威胁

Azure Sentinel 的基础是数据存储；它结合了高性能查询和动态架构并可扩展到大规模数据卷。 Azure 门户和所有 Azure Sentinel 工具都使用一个公共 API 来访问此数据存储。 同一 API 也可用于 [Jupyter](https://jupyter.org/) 笔记本和 Python 等外部工具。 虽然可以在门户中执行许多常见任务，但 Jupyter 扩展了可对此数据执行的操作范围。 它将完整的可编程性与用于机器学习、可视化效果和数据分析的大量库集合组合在一起。 这些属性使 Jupyter 成为安全调查和搜寻的引人注目的工具。

![示例笔记本](./media/notebooks/sentinel-notebooks-map.png)

我们已将 Jupyter 体验集成到 Azure 门户中，使你可以轻松地创建和运行笔记本来分析数据。 Kqlmagic 库提供了一种连接，使你可以从 Azure Sentinel 获取查询并直接在笔记本中运行它们。 查询使用 [Kusto 查询语言](https://kusto.azurewebsites.net/docs/query/index.html)。 Microsoft 的某些安全分析师开发的几个笔记本打包在 Azure Sentinel 中。 其中一些笔记本专门用于特定方案，可以按原样使用。 其他笔记本用作示例，以说明可以复制或修改以便在自己的笔记本中使用的技术和功能。 其他笔记本还可从 Azure Sentinel GitHub 社区导入。

集成的 Jupyter 体验使用 [Azure Notebooks](https://notebooks.azure.com/) 存储、共享和执行笔记本。 如果你的计算机上或其他 JupterHub 环境（如 Azure Databricks）有 Python 环境和 Jupyter，也可以在本地运行这些笔记本。

笔记本包括两个组件：

- 基于浏览器的界面，你可以在其中输入和运行查询和代码，执行结果也显示在此处。
- 一个内核，负责分析和执行代码本身。 

在 Azure Notebooks 中，默认情况下，此内核在 Azure 免费云计算和存储中运行。 如果你的笔记本包括复杂的机器学习模型或可视化效果，请考虑使用更强大的专用计算资源，如[数据科学虚拟机](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM)。 你的帐户中的笔记本将保持私有，除非你选择共享它们。

Azure Sentinel 笔记本使用许多常见的 Python 库，例如 pandas、matplotlib、bokeh 等。 还提供了许多其他 Python 包供你选择，涵盖的领域包括：

- 可视化效果和图形
- 数据处理和分析
- 统计和数字计算
- 机器学习和深度学习

我们还在 [msticpy](https://github.com/Microsoft/msticpy/) 包中发布了一些开放源代码 Jupyter 安全工具。 此包可用于许多随附的笔记本。 Msticpy 工具专为帮助创建用于搜寻和调查的笔记本而设计，我们正在积极地致力于提供新功能和改进。

初始笔记本包括：

- **引导式调查 - 进程警报**：允许你通过分析受影响的主机上的活动快速会审警报。
- **引导式搜寻 - Windows 主机资源管理器**：允许你浏览主机上的帐户活动、进程执行、网络活动和其他事件。
- **引导式搜寻 - Office365 探索**：在多个 Office 365 数据集中搜寻可疑的 Office 365 活动。

[Azure Sentinel GitHub 社区存储库](https://github.com/Azure/Azure-Sentinel)是存储任何未来的 Azure Sentinel 笔记本的位置，这些笔记本由 Microsoft 生成或由社区提供。

必须具有 Azure Notebooks 帐户才能使用笔记本。 有关详细信息，请参阅[快速入门：从 Azure Notebooks 文档登录并设置用户 ID](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)。 要创建此帐户，可从“Azure Sentinel”-“Notebooks”中的命令栏选择“注册 Azure Notebooks”选项 ：

> [!div class="mx-imgBorder"]
>![“注册 Azure Notebooks”选项](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

可直接从 Azure Sentinel 运行笔记本，也可将所有 Azure Sentinel 笔记本克隆到新的 Azure Notebooks 项目。

## <a name="run-a-notebook-from-azure-sentinel"></a>从 Azure Sentinel 运行笔记本
 
1. 在 Azure 门户中，导航到“Azure Sentinel” > “威胁管理” > “笔记本”，可在此处看到 Azure Sentinel 提供的笔记本  。 

2. 选择各个笔记本以阅读其说明、所需的数据类型和数据源。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![启动笔记本](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. 选择要使用的笔记本，然后选择“启动笔记本(预览)”，将笔记本克隆到连接到 Azure Sentinel 工作区的新 Azure Notebooks 项目并进行配置。 完成此过程后，将在 Azure Notebooks 中打开笔记本以供运行。

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>将 Azure Sentinel 笔记本克隆到新的 Azure Notebooks 项目

此过程将为你创建一个 Azure Notebooks 项目，其中包含 Azure Sentinel 笔记本。 然后，你可以按原样运行笔记本，或者对其进行更改，然后运行它们。

1. 在 Azure 门户中，导航到“Azure Sentinel” > “威胁管理” > “笔记本”，然后从命令栏选择“克隆笔记本”   。
  
    > [!div class="mx-imgBorder"]
    >![“克隆笔记本”选项](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. 显示以下对话框后，请选择“导入”，将 GitHub 存储库克隆到 Azure Notebooks 项目中。 如果你当前没有 Azure Notebooks 帐户，系统会提示你创建一个帐户并登录。

   ![导入笔记本](./media/notebooks/sentinel-notebooks-clone.png)

3. 在“上传 GitHub 存储库”对话框中，请勿选择“以递归方式克隆”，因为此选项是指链接的 GitHub 存储库 。 对于项目名称，请使用默认名称或键入新名称。 然后单击“导入”以开始克隆 GitHub 内容，这可能需要几分钟才能完成。

   ![导入笔记本](./media/notebooks/sentinel-create-project.png)

4. 打开刚刚创建的项目，然后打开“Notebooks”文件夹以查看笔记本。 例如：

   ![导入存储库](./media/notebooks/sentinel-open-notebook1.png)

然后，你可以从 Azure Notebooks 运行笔记本。 要从 Azure Sentinel 返回到这些笔记本，请从“Azure Sentinel”-“笔记本”中的命令栏中，选择“转到笔记本” ：

> [!div class="mx-imgBorder"]
>![“转到笔记本”选项](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>使用笔记本搜寻

每个笔记本都列出了执行搜寻或调查的步骤。 笔记本中所需的库和其他依赖项可以通过笔记本或简单的配置过程进行安装。 在前面的步骤中，将自动预配将笔记本项目重新绑定到 Azure Sentinel 订阅的配置。

1. 如果你还没有位于 Azure Notebooks 中，可从“Azure Sentinel”-“Notebooks”中的命令栏选择“转到 Azure Notebooks”选项 ：
    
    > [!div class="mx-imgBorder"]
    >![“转到笔记本”选项](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    在 Azure Notebooks 中，选择“我的项目”，然后选择包含 Azure Sentinel 笔记本的项目，最后选择“Notebooks”文件夹 。
    
2. 在打开笔记本之前，请注意，默认情况下，已选择“免费计算”来运行笔记本：
    
   ![选择笔记本](./media/notebooks/sentinel-open-notebook2.png)
    
    如果已按照简介所述配置了要使用的数据科学虚拟机 (DSVM)，请在打开第一个笔记本之前选择 DSVM 并进行身份验证。 

3. 选择一个笔记本并将其打开。
    
    首次打开笔记本时，系统可能会提示你选择内核版本。 如果未出现提示，可以从“内核” >  “更改内核”选择内核版本，然后选择至少为3.6 的版本 。 所选内核版本将显示在笔记本窗口的右上方：
    
   ![选择笔记本](./media/notebooks/sentinel-select-kernel.png)

4. 在对下载的笔记本进行任何更改之前，最好是创建原始笔记本的副本，然后在副本上进行操作。 为此，请选择“文件” > “创建副本” 。 使用副本，可以安全地更新到未来版本的笔记本，而不会覆盖任何数据。
    
    现在你就可以运行或编辑选定的笔记本了。

建议：

- 如需快速了解如何在 Azure Sentinel 中查询数据，请参阅主“Notebooks”文件夹中的 [GetStarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) 笔记本。 

- 可在“Sample-Notebooks”子文件夹中找到其他示例笔记本。 这些示例笔记本与数据一起保存，因此更易于查看预期的输出。 建议在 [nbviewer](https://nbviewer.jupyter.org/) 中查看这些笔记本。 

- “HowTos”文件夹包含笔记本说明，例如：设置默认 Python 版本、配置 DSVM、从笔记本创建 Azure Sentinel 书签和其他主题。

提供的笔记本旨在用作有用的工具以及举例说明和代码示例，以供你开发自己的笔记本。

欢迎你提供宝贵意见，包括建议、针对功能的请求、对现有笔记本的 bug 报告或改进和补充。 请前往 [Azure Sentinel GitHub 社区](https://github.com/Azure/Azure-Sentinel)创建问题或分支并上传贡献。

## <a name="next-steps"></a>后续步骤

本文介绍了如何开始在 Azure Sentinel 中使用 Jupyter 笔记本。 要详细了解 Azure Sentinel，请参阅以下文章：

- [主动搜寻威胁](hunting.md)
- [在搜寻时使用书签保存感兴趣的信息](bookmarks.md)
