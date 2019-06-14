---
title: 测试使用 Visual Studio Code （预览版） 在本地 Azure Stream Analytics 查询
description: 本文介绍如何使用 Azure Stream Analytics 工具在本地查询测试用于 Visual Studio Code。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827945"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>测试使用 Visual Studio Code 在本地的 Stream Analytics 查询

针对 Visual Studio Code 的 Azure Stream Analytics 工具可用于测试你的 Stream Analytics 作业使用示例数据在本地。

使用此[快速入门](quick-create-vs-code.md)若要了解如何创建使用 Visual Studio Code 的 Stream Analytics 作业。

## <a name="run-queries-locally"></a>在本地运行的查询

适用于 Visual Studio Code 的 Azure Stream Analytics 扩展可用于测试你的 Stream Analytics 作业使用示例数据在本地。

1. 一旦创建 Stream Analytics 作业，使用**Ctrl + Shift + P**打开命令面板。 然后键入，然后选择**ASA:Add Input**。

    ![在 Visual Studio code 中添加 ASA 输入](./media/vscode-local-run/add-input.png)

2. 选择**本地输入**。

    ![在 Visual Studio code 中添加 ASA 本地输入](./media/vscode-local-run/add-local-input.png)

3. 选择 **+ 新本地输入**。

    ![添加新的 ASA 本地 Visual Studio code 中输入](./media/vscode-local-run/add-new-local-input.png)

4. 输入在查询中使用的相同输入的别名。

    ![添加新的 ASA 本地输入的别名](./media/vscode-local-run/new-local-input-alias.png)

5. 在中**LocalInput_DefaultLocalStream.json**文件中，输入你的本地数据文件的所在位置的文件路径。

    ![在 Visual Studio 中输入本地文件路径](./media/vscode-local-run/local-file-path.png)

6. 返回到您的查询编辑器，并选择**在本地运行**。

    ![选择在查询编辑器中本地运行](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>后续步骤

* [在 Visual Studio Code （预览版） 中创建 Azure Stream Analytics 云作业](quick-create-vs-code.md)

* [探索 Azure Stream Analytics 作业使用 Visual Studio Code （预览版）](vscode-explore-jobs.md)
