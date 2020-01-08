---
title: 复制或备份 Azure 流分析作业
description: 本文介绍如何复制或备份 Azure 流分析作业。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: d55f36a5b18eca2622216b6be9906a57a6f421c5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457584"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>复制或备份 Azure 流分析作业

可以使用 Visual Studio Code 或 Visual Studio 来复制或备份已部署的 Azure 流分析作业。 

## <a name="before-you-begin"></a>开始之前
* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/)。

* 登录 [Azure 门户](https://portal.azure.com/)。

* 安装适用于[Visual Studio Visual Studio Code 或 Azure 流分析工具](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code#install-the-azure-stream-analytics-extension)的[azure 流分析扩展](https://docs.microsoft.com/azure/stream-analytics/quick-create-vs-code#install-the-azure-stream-analytics-extension)。  

## <a name="visual-studio-code"></a>Visual Studio Code

1. 单击 "Visual Studio Code" 活动栏中的 " **Azure** " 图标，然后展开 "**流分析**" 节点。 你的作业应显示在你的订阅下。

   ![打开流分析资源管理器](./media/vscode-explore-jobs/open-explorer.png)

2. 若要将作业导出到本地项目，请在 Visual Studio Code 中找到要导出的**流分析资源管理器**中的作业。 然后为项目选择一个文件夹。

    ![在 Visual Studio Code 中导出 ASA 作业](./media/vscode-explore-jobs/export-job.png)

    项目会导出到所选文件夹并添加到当前工作区。

    ![在 Visual Studio Code 中导出 ASA 作业](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. 若要使用其他名称将作业发布到另一个区域或备份，请选择 "**从订阅中选择" 以**在查询编辑器中发布（\*script.asaql），然后按照说明进行操作。

    ![在 Visual Studio Code 中发布到 Azure](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. 按照将[部署的 Azure 流分析作业导出到项目说明](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project)。

2. 在查询编辑器中打开 "script.asaql \*" 文件，选择 "脚本编辑器" 中的 "**提交到 Azure** "，并按照说明使用新名称将作业发布到另一个区域或备份。

## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Visual Studio Code 创建流分析作业](quick-create-vs-code.md)
* [快速入门：使用 Visual Studio 创建流分析作业](stream-analytics-quick-create-vs.md)
* [使用 Azure 管道通过 CI/CD 部署 Azure 流分析作业](stream-analytics-tools-visual-studio-cicd-vsts.md)
