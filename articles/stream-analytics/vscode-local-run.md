---
title: 测试 Visual Studio Code 的 Azure 流分析查询
description: 本文介绍如何通过用于 Visual Studio Code 的 Azure 流分析工具在本地测试查询。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924969"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>在本地测试流分析查询 Visual Studio Code

可以使用 Azure 流分析工具 Visual Studio Code 来使用示例数据在本地测试流分析作业。

使用本[快速入门](quick-create-vs-code.md)教程了解如何使用 Visual Studio Code 创建流分析作业。

## <a name="prerequisites"></a>必备组件
* 安装[.net CORE SDK](https://dotnet.microsoft.com/download)。
* 重新启动 Visual Studio Code。
 
## <a name="run-queries-locally"></a>在本地运行查询

你可以使用适用于 Visual Studio Code 的 Azure 流分析扩展来使用示例数据在本地测试流分析作业。

1. 创建流分析作业后，使用**Ctrl + Shift + P**打开命令面板。 然后键入并选择**ASA： Add Input**。

    ![在 Visual Studio code 中添加 ASA 输入](./media/vscode-local-run/add-input.png)

2. 选择 "**本地输入**"。

    ![在 Visual Studio code 中添加 ASA 本地输入](./media/vscode-local-run/add-local-input.png)

3. 选择 " **+ 新本地输入**"。

    ![在 Visual Studio code 中添加新的 ASA 本地输入](./media/vscode-local-run/add-new-local-input.png)

4. 输入在查询中使用的相同输入别名。

    ![添加新的 ASA 本地输入别名](./media/vscode-local-run/new-local-input-alias.png)

5. 在**LocalInput_DefaultLocalStream**文件中，输入本地数据文件所在的文件路径。

    ![在 Visual Studio 中输入本地文件路径](./media/vscode-local-run/local-file-path.png)

6. 返回到查询编辑器，选择 "**本地运行**"。

    ![在查询编辑器中选择 "本地运行"](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio Code 中创建 Azure 流分析云作业（预览）](quick-create-vs-code.md)

* [浏览 Visual Studio Code 的 Azure 流分析作业（预览）](vscode-explore-jobs.md)
