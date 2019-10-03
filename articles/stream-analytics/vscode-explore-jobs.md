---
title: 探索 Azure Stream Analytics 作业使用 Visual Studio Code （预览版）
description: 本文介绍如何将 Azure Stream Analytics 作业导出到本地项目、 列出作业和作业视图的实体。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 8674d478646c8f9be6b32521c6624752ac6df052
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827795"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>了解 Azure Stream Analytics 使用 Visual Studio Code （预览版）

Visual Studio Code 扩展 Azure Stream Analytics 开发人员的轻型版用于管理 Stream Analytics 作业。 它可以在 Windows、 Mac 和 Linux 上使用。 使用 Azure Stream Analytics 扩展中，你可以：

- [创建](quick-create-vs-code.md)、 启动和停止作业
- 将现有作业导出到本地项目
- 列出作业并查看作业实体

## <a name="export-a-job-to-a-local-project"></a>将作业导出到本地项目

若要将作业导出到本地项目中，找到你想要在导出的作业**Stream Analytics 资源管理器**在 Visual Studio Code。 然后选择你的项目的文件夹。 项目会导出到您选择的文件夹，可以继续管理该作业从 Visual Studio Code。 使用 Visual Studio Code 用于管理 Stream Analytics 作业的详细信息，请参阅 Visual Studio Code[快速入门](quick-create-vs-code.md)。

![在 Visual Studio Code 中的导出 ASA 作业](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>列出作业和查看作业实体

可使用作业视图从 Visual Studio 中与 Azure 流分析作业进行交互。


1. 单击**Azure** Visual Studio Code 活动栏上的图标，然后展开**Stream Analytics 节点**。 你的作业应显示你的订阅。

   ![打开 Stream Analytics 资源管理器](./media/vscode-explore-jobs/open-explorer.png)

2. 展开作业节点，你可以打开并查看作业查询、 配置、 输入、 输出和函数。 

3. 右键单击作业节点，然后选择**门户中打开作业视图**节点以在 Azure 门户中打开作业视图。

   ![在门户中打开作业视图](./media/vscode-explore-jobs/open-job-view.png)

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio Code （预览版） 中创建 Azure Stream Analytics 云作业](quick-create-vs-code.md)
