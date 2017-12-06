---
title: "在 Azure 自动化帐户中创建观察程序任务 | Microsoft Docs"
description: "了解如何在 Azure 自动化帐户中创建观察程序任务，以观察在文件夹中创建的新文件。"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>通过 Azure 自动化观察程序任务，可响应本地数据中心内发生的事件

本教程介绍了如何创建新的观察程序任务，包括：

> [!div class="checklist"]
> * 创建用于在目录中查找新文件的观察程序 runbook。
> * 创建自动化变量，保留观察程序处理文件的最新时间。
> * 创建在观察程序 runbook 找到新文件时调用的操作 runbook。
> * 创建用于选择观察程序 runbook 和操作 runbook 的观察程序任务。
> * 通过将新文件添加到目录来触发观察程序。
> * 检查显示新文件信息的操作 runbook 的输出。  

## <a name="prerequisites"></a>先决条件

完成本教程需要以下各项。
+ Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
+ [自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 runbook 和观察程序任务。
+ 一个[混合 runbook 辅助角色](automation-hybrid-runbook-worker.md)，观察程序任务在其中运行。

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>创建用于查找新文件的观察程序 runbook
1.  打开自动化帐户，然后单击“Runbook”页。
2.  单击“浏览库”按钮。
![UI 中的 Runbook 列表](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  搜索“Watch-NewFile”，然后将 runbook 导入自动化帐户。
![发布 UI 中的 runbook](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  单击“编辑”查看 Runbook 源，然后单击“发布”按钮。

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>创建自动化变量，保留观察程序处理文件的最新时间
1.  打开“共享资源”下的“变量”页，然后单击“添加变量”![UI 中的变量列表](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  输入“Watch-NewFileTimestamp”作为名称
3.  选择 DateTime 作为类型，然后单击“创建”按钮。
![UI 中的“创建”水印变量](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>创建在观察程序 runbook 找到新文件时调用的操作 runbook
1.  单击“流程自动化”类别下的“Runbook”页。
2.  单击“浏览库”按钮。
3.  搜索“Process-NewFile”，然后将 runbook 导入自动化帐户。
4.  单击“编辑”查看 Runbook 源，然后单击“发布”按钮。
![处理 UI 中的观察程序](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>创建用于选择观察程序 runbook 和操作 runbook 的观察程序任务
1.  打开“观察程序任务”页，然后单击“添加观察程序任务”按钮。
![UI 中的观察程序列表](media/automation-watchers-tutorial/WatchersList.png)
2.  输入“WatchMyFolder”作为名称。
3.  选择“配置观察程序”，然后选择“Watch-NewFile”runbook。
![配置 UI 中的观察程序](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  输入以下参数值：
    *   FOLDERPATH。 混合辅助角色上在其中创建新文件的文件夹
    *   EXTENSION。 留空以便处理所有文件扩展名。
    *   RECURSE。 保留默认值。
    *   运行设置。 选择混合辅助角色。
5.  单击“确定”，然后选择返回到观察程序页。
6.  选择“配置操作”，然后选择“Process-NewFile”runbook。
![配置 UI 中的观察程序操作](media/automation-watchers-tutorial/ConfigureAction.png)
7.  输入以下参数值：
    *   EVENTDATA。 留空。 从观察程序 runbook 传入数据。
    *   运行设置。 当此 runbook 在自动化服务中运行时保留为 Azure。
8.  单击“确定”，然后选择返回到观察程序页。
9.  单击“确定”创建观察程序任务。

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>通过将新文件添加到目录来触发观察程序
1.  远程登录到混合辅助角色
2.  将新文本文件添加到观察程序任务正在监视的文件夹中。

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>检查显示新文件信息的操作 runbook 的输出
1.  单击“WatchMyFolder”的观察程序任务
2.  单击“查看观察程序流”，查看观察程序是否找到新文件并启动此操作 runbook。
3.  单击“查看观察程序操作作业”，查看操作 runbook 作业。
![UI 中的观察程序操作作业](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>后续步骤：

有关详细信息，请参阅[我的第一个 PowerShell runbook](automation-first-runbook-textual-powershell.md)。








