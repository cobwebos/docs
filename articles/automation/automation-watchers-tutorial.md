---
title: 在 Azure 自动化帐户中创建观察程序任务
description: 了解如何在 Azure 自动化帐户中创建观察程序任务，以观察在文件夹中创建的新文件。
services: automation
ms.service: automation
ms.component: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: article
ms.date: 03/19/2017
ms.openlocfilehash: 2ae5fe2f2cd01eb9a0d7105c34c1dc216479720c
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>创建 Azure 自动化观察程序任务来跟踪本地计算机上的文件更改

Azure 自动化使用观察程序任务监视事件并触发操作。 本教程引导你创建一个观察程序任务来监视何时向目录中添加了新文件。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 导入观察程序 runbook
> * 创建自动化变量
> * 创建操作 runbook
> * 创建观察程序任务
> * 触发观察程序
> * 检查输出

## <a name="prerequisites"></a>先决条件

完成本教程需要以下各项：

* Azure 订阅。 如果还没有帐户，可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 runbook 和观察程序任务。
* 一个[混合 runbook 辅助角色](automation-hybrid-runbook-worker.md)，观察程序任务在其中运行。

## <a name="import-a-watcher-runbook"></a>导入观察程序 runbook

本教程使用名为 **Watch-NewFile** 的观察程序 runbook 查找某个目录中的新文件。 该观察程序 runbook 检索文件夹中的文件的最后已知写入时间，并查找比该水印更新的任何文件。 在此步骤中，你将此 runbook 导入到你的自动化帐户中。

1. 打开你的自动化帐户，然后单击“Runbook”页。
1. 单击“浏览库”按钮。
1. 搜索“Watcher runbook”，在目录中选择“Watcher runbook that looks for new files in a directory”，然后选择“导入”。
  ![从 UI 导入自动化 runbook](media/automation-watchers-tutorial/importsourcewatcher.png)
1. 为该 runbook 提供名称和说明，然后选择“确定”以将该 runbook 导入到自动化帐户中。
1. 选择“编辑”，然后单击“发布”。 出现提示时，选择“是”以发布该 runbook。

## <a name="create-an-automation-variable"></a>创建自动化变量

使用一个[自动化变量](automation-variables.md)来存储前面的 runbook 从每个文件读取和存储的时间戳。 

1. 在“共享资源”下选择“变量”，并选择“+ 添加变量”。
1. 输入“Watch-NewFileTimestamp”作为名称
1. 选择“日期时间”作为类型。
1. 单击“创建”按钮。 这将创建该自动化变量。

## <a name="create-an-action-runbook"></a>创建操作 runbook

在观察程序任务中，操作 runbook 用来对从观察程序 runbook 传递给它的数据执行操作。 在此步骤中，你将更新一个名为“Process-NewFile”的预定义的操作 runbook。

1. 导航到你的自动化帐户，从“流程自动化”类别下选择“Runbook”。
1. 单击“浏览库”按钮。
1. 搜索“Watcher action”，在目录中选择“Watcher action that processes events triggered by a watcher runbook”，然后选择“导入”。
  ![从 UI 中导入操作 runbook](media/automation-watchers-tutorial/importsourceaction.png)
1. 为该 runbook 提供名称和说明，然后选择“确定”以将该 runbook 导入到自动化帐户中。
1. 选择“编辑”，然后单击“发布”。 出现提示时，选择“是”以发布该 runbook。

## <a name="create-a-watcher-task"></a>创建观察程序任务

观察程序任务包含两个部分。 观察程序和操作。 观察程序以观察器任务中定义的间隔运行。 来自观察程序 runbook 的数据被传递到操作 runbook。 在此步骤中，你将配置引用在前面的步骤中定义的观察程序和操作 runbook 的观察程序任务。

1. 导航到你的自动化帐户，从“流程自动化”类别下选择“观察程序任务”。
1. 选择“观察程序任务”页，然后单击“添加观察程序任务”按钮。
1. 输入“WatchMyFolder”作为名称。

1. 选择“配置观察程序”，然后选择“Watch-NewFile”runbook。

1. 输入以下参数值：

   * **文件夹路径** - 混合辅助角色上在其中创建新文件的文件夹。 d:\examplefiles
   * **扩展名** - 留空以便处理所有文件扩展名。
   * **递归** - 将此值保留为默认值。
   * **运行设置** - 选择混合辅助角色。

1. 单击“确定”，然后选择返回到观察程序页。
1. 选择“配置操作”，然后选择“Process-NewFile”runbook。
1. 输入以下参数值：

   *    **事件数据** - 保留为空。 从观察程序 runbook 传入数据。  
   *    **运行设置** - 当此 runbook 在自动化服务中运行时保留为 Azure。

1. 单击“确定”，然后选择返回到观察程序页。
1. 单击“确定”创建观察程序任务。

![从 UI 配置观察程序操作](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>触发观察程序

若要测试观察程序是否按预期工作，需要创建一个测试文件。

远程登录到混合辅助角色。 打开 **PowerShell** 并在文件夹中创建一个测试文件。
  
   ```PowerShell-interactive
   New-Item -Name ExampleFile1.txt
   ```

以下示例显示了预期的输出。

```
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>检查输出

1. 导航到你的自动化帐户，从“流程自动化”类别下选择“观察程序任务”。
1. 选择观察程序任务“WatchMyFolder”。
1. 单击“流”下的“查看观察程序流”，查看观察程序是否发现新文件并启动了此操作 runbook。
1. 若要查看操作 runbook 作业，请单击“查看观察程序操作作业”。 可以选择每个作业并查看作业详细信息。

   ![UI 中的观察程序操作作业](media/automation-watchers-tutorial/WatcherActionJobs.png)

可以在下面的示例中看到当发现了新文件时的预期输出：

```
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 导入观察程序 runbook
> * 创建自动化变量
> * 创建操作 runbook
> * 创建观察程序任务
> * 触发观察程序
> * 检查输出

单击以下链接可了解有关撰写 runbook 的详细信息。

> [!div class="nextstepaction"]
> [我的第一个 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
