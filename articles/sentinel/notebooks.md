---
title: 将笔记本与 Azure 哨兵一起用于安全搜索
description: 本文介绍如何使用笔记本与 Azure Sentinel 搜索功能一起。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581831"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>使用 Jupyter 笔记本电脑来查找安全威胁

Azure 哨兵的基础是数据存储;它结合了高性能查询、动态架构和缩放到海量数据卷。 Azure 门户和所有 Azure 哨兵工具都使用通用 API 访问此数据存储。 相同的 API 也可用于外部工具，如[聚居笔记本](https://jupyter.org/)和 Python。 虽然许多常见任务可以在门户中执行，但 Jupyter 扩展了可以使用此数据执行操作的范围。 它将完整的可编程性与用于机器学习、可视化和数据分析的大量库相结合。 这些属性使Jupyter成为安全调查和狩猎的引人注目的工具。

![示例笔记本](./media/notebooks/sentinel-notebooks-map.png)

我们将 Jupyter 体验集成到 Azure 门户中，使您能够轻松创建和运行笔记本来分析数据。 *Kqlmagic*库提供胶水，允许您从 Azure Sentinel 获取查询并直接在笔记本中运行它们。 查询使用[库斯托查询语言](https://kusto.azurewebsites.net/docs/query/index.html)。 由微软一些安全分析师开发的几种笔记本与 Azure Sentinel 一起打包。 其中一些笔记本是为特定方案构建的，可以作为本样使用。 其他示例旨在说明您可以复制或调整以用于您自己的笔记本的技术和功能。 也可以从 Azure Sentinel 社区 GitHub 导入其他笔记本。

集成的 Jupyter 体验使用[Azure 笔记本](https://notebooks.azure.com/)来存储、共享和执行笔记本。 如果计算机上有 Python 环境和 Jupyter，或者在其他 JupterHub 环境（如 Azure 数据块）中，也可以在本地运行这些笔记本。

笔记本有两个组件：

- 输入和运行查询和代码以及显示执行结果的基于浏览器的界面。
- 负责解析和执行代码本身的*内核*。 

默认情况下，在 Azure 笔记本中，此内核在 Azure*免费云计算和存储*上运行。 如果您的笔记本包含复杂的机器学习模型或可视化效果，请考虑使用功能更强大的专用计算资源，如[数据科学虚拟机](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)（DSVM）。 除非您选择共享笔记本，否则帐户中的笔记本将保持私密。

Azure Sentinel 笔记本使用许多流行的 Python 库，如熊猫、matplotlib、bokeh 等。 有很多其他 Python 包供您选择，包括以下方面：

- 可视化和图形
- 数据处理和分析
- 统计和数值计算
- 机器学习和深度学习

我们还发布了一些开源的Jupyter安全工具在一个名为[msticpy](https://github.com/Microsoft/msticpy/)的包。 此包用于许多包含的笔记本。 Msticpy 工具专为帮助创建用于狩猎和调查的笔记本电脑而设计，我们正在积极开发新功能和改进。

初始笔记本包括：

- **引导式调查 - 进程警报**：允许您通过分析受影响主机或主机上的活动来快速分流警报。
- **引导式寻线 - Windows 主机资源管理器**：允许您浏览主机上的帐户活动、进程执行、网络活动和其他事件。
- **引导式搜索 - Office365-探索**：在多个 Office 365 数据集中搜索可疑的 Office 365 活动。

[Azure 哨兵社区 GitHub 存储库](https://github.com/Azure/Azure-Sentinel)是 Microsoft 构建或由社区提供的任何未来 Azure Sentinel 笔记本的位置。

要使用笔记本，必须具有 Azure 笔记本帐户。 有关详细信息，请参阅快速入门：从 Azure 笔记本文档中[登录并设置用户 ID。](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) 要创建此帐户，可以使用 Azure Sentinel - 笔记本中的命令栏中的"**注册 Azure 笔记本****"选项**：

> [!div class="mx-imgBorder"]
>![注册 Azure 笔记本选项](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

可以从 Azure 哨兵直接运行笔记本，也可以将所有 Azure Sentinel 笔记本克隆到新的 Azure 笔记本项目。

## <a name="run-a-notebook-from-azure-sentinel"></a>从 Azure 哨兵运行笔记本
 
1. 从 Azure 门户导航到**Azure 哨兵** > **威胁管理** > **笔记本**，您可以在其中查看 Azure Sentinel 提供的笔记本。 

2. 选择单个笔记本来读取其说明、所需数据类型和数据源。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![启动笔记本](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. 选择要使用的笔记本，然后选择 **"启动笔记本（预览）"** 以克隆笔记本并将其配置为连接到 Azure Sentinel 工作区的新 Azure 笔记本项目。 该过程完成后，笔记本将在 Azure 笔记本中打开，以便您运行。

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>将 Azure 哨兵笔记本克隆到新的 Azure 笔记本项目

此过程会为您创建一个 Azure 笔记本项目，其中包含 Azure Sentinel 笔记本。 然后，您可以运行笔记本，或进行更改，然后运行它们。

1. 从 Azure 门户导航到**Azure 哨兵** > **威胁管理** > **笔记本**，然后从命令栏中选择 **"克隆笔记本**"：
  
    > [!div class="mx-imgBorder"]
    >![克隆笔记本选项](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. 出现以下对话框时，选择 **"导入**"以将 GitHub 存储库克隆到 Azure 笔记本项目中。 如果没有现有的 Azure 笔记本帐户，系统将提示您创建一个帐户并登录。

   ![导入笔记本](./media/notebooks/sentinel-notebooks-clone.png)

3. 在 **"上传 GitHub 存储库"** 对话框中，不要**递归选择"克隆"，** 因为此选项引用链接的 GitHub 存储库。 对于项目名称，请使用默认名称或键入新名称。 然后单击 **"导入**"开始克隆 GitHub 内容，这可能需要几分钟才能完成。

   ![导入笔记本](./media/notebooks/sentinel-create-project.png)

4. 打开刚刚创建的项目，然后打开**笔记本**文件夹以查看笔记本。 例如：

   ![导入回购](./media/notebooks/sentinel-open-notebook1.png)

然后，可以从 Azure 笔记本运行笔记本。 要从 Azure Sentinel 返回到这些笔记本，请选择从**Azure Sentinel 中的**命令栏**转到笔记本**- 笔记本：

> [!div class="mx-imgBorder"]
>![转到笔记本选项](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>使用笔记本进行寻线

每个笔记本都会引导您完成进行狩猎或调查的步骤。 笔记本所需的库和其他依赖项可以从笔记本本身或通过简单的配置过程安装。 将笔记本项目关联回 Azure Sentinel 订阅的配置将在上述步骤中自动预配。

1. 如果您尚未在 Azure 笔记本中，则可以使用**Azure Sentinel - 笔记本**中的命令栏中的"**转到笔记本**"选项：
    
    > [!div class="mx-imgBorder"]
    >![转到笔记本选项](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    在 Azure 笔记本中，选择 **"我的项目**"，然后选择包含 Azure Sentinel 笔记本的项目，最后选择 **"笔记本"** 文件夹。
    
2. 在打开笔记本之前，请注意，默认情况下，选择"自由计算"来运行笔记本：
    
   ![选择笔记本](./media/notebooks/sentinel-open-notebook2.png)
    
    如果已配置数据科学虚拟机 （DSVM） 以使用，请在打开第一个笔记本之前选择 DSVM 并进行身份验证。 

3. 选择一个笔记本来打开它。
    
    首次打开笔记本时，系统可能会提示您选择内核版本。 如果未提示您，则可以从**内核** >  **更改内核**中选择内核版本，然后选择至少 3.6 的版本。 所选内核版本显示在笔记本窗口的右上角：
    
   ![选择笔记本](./media/notebooks/sentinel-select-kernel.png)

4. 在对已下载的笔记本进行任何更改之前，最好先制作一份原始笔记本并处理该副本。 为此，请选择"**文件** > **复制**"。 使用副本可让您安全地更新到将来版本的笔记本，而无需覆盖任何数据。
    
    现在，您可以运行或编辑所选笔记本。

建议：

- 有关在 Azure Sentinel 中查询数据的快速介绍，请查看主**笔记本**文件夹中的[Get开始](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb)笔记本。 

- 您可以在 **"示例笔记本"** 子文件夹中找到其他示例笔记本。 这些示例笔记本已保存数据，因此更易于查看预期输出。 我们建议在[nbviewer](https://nbviewer.jupyter.org/)中查看这些笔记本。 

- **"HowTos"** 文件夹包含描述笔记本，例如：设置默认 Python 版本、配置 DSVM、从笔记本创建 Azure Sentinel 书签以及其他主题。

提供的笔记本既可用作有用的工具，也可用作可用于开发自己笔记本的插图和代码示例。

我们欢迎反馈，无论是建议、功能请求、贡献笔记本、错误报告，还是对现有笔记本的改进和添加。 转到[Azure 哨兵社区 GitHub](https://github.com/Azure/Azure-Sentinel)以创建问题或分叉并上载贡献。

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何在 Azure Sentinel 中使用 Jupyter 笔记本。 要详细了解 Azure Sentinel，请参阅以下文章：

- [主动寻找威胁](hunting.md)
- [使用书签在狩猎时保存有趣的信息](bookmarks.md)
