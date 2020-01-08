---
title: 了解 Visual Studio Code 中的 Azure 流分析作业
description: 本文介绍如何将 Azure 流分析作业导出到本地项目、列出作业和查看作业实体。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 1d3a02d3778f9b4113767c5f755d675aeadd901b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479236"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>了解 Visual Studio Code （预览版）的 Azure 流分析

适用于 Visual Studio Code 扩展的 Azure 流分析为开发人员提供了管理其流分析作业的轻型体验。 它可以在 Windows、Mac 和 Linux 上使用。 利用 Azure 流分析扩展，可以：

- [创建](quick-create-vs-code.md)、启动和停止作业
- 将现有作业导出到本地项目
- 列出作业和查看作业实体

## <a name="export-a-job-to-a-local-project"></a>将作业导出到本地项目

若要将作业导出到本地项目，请在 Visual Studio Code 中找到要导出的**流分析资源管理器**中的作业。 然后为项目选择一个文件夹。 项目会导出到你选择的文件夹，你可以继续从 Visual Studio Code 管理该作业。 有关使用 Visual Studio Code 管理流分析作业的详细信息，请参阅 Visual Studio Code[快速入门](quick-create-vs-code.md)。

![在 Visual Studio Code 中导出 ASA 作业](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>列出作业和查看作业实体

可使用作业视图从 Visual Studio 中与 Azure 流分析作业进行交互。


1. 单击 Visual Studio Code 活动栏上的**Azure**图标，然后展开 "**流分析" 节点**。 你的作业应显示在你的订阅下。

   ![打开流分析资源管理器](./media/vscode-explore-jobs/open-explorer.png)

2. 展开你的作业节点，你可以打开并查看作业查询、配置、输入、输出和功能。 

3. 右键单击作业节点，然后选择 "门户" 节点中的 "**打开作业视图**"，以在 Azure 门户中打开作业视图。

   ![在门户中打开作业视图](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio Code 中创建 Azure 流分析云作业（预览）](quick-create-vs-code.md)
