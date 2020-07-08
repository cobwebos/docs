---
title: 通过 Azure 自动化观察程序任务跟踪更新的文件
description: 本文介绍如何在 Azure 自动化帐户中创建观察程序任务，以观察在文件夹中创建的新文件。
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 3369a807410e9e959e8091d5b16c8480803d26bb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830576"
---
# <a name="track-updated-files-with-a-watcher-task"></a>通过观察程序任务跟踪更新的文件

Azure 自动化通过 PowerShell Runbook 使用观察程序任务查找事件并触发操作。 观察程序任务包含两个部分：观察程序和操作。 观察程序 runbook 以观察程序任务中定义的间隔时间并将数据输出到操作 runbook。 

> [!NOTE]
> Azure 中国世纪互联不支持观察程序任务。

> [!IMPORTANT]
> 从 2020 年 5 月开始，支持使用 Azure 逻辑应用监视事件，计划定期任务和触发操作。 请参阅[使用 Azure 逻辑应用计划和运行反复出现的自动化任务、流程和工作流](https://docs.microsoft.com/azure/logic-apps/concepts-schedule-automated-recurring-tasks-workflows)。

本教程引导你创建一个观察程序任务来监视何时向目录中添加了新文件。 学习如何：

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
* PowerShell Runbook。 观察程序任务不支持 PowerShell 工作流 Runbook。

## <a name="import-a-watcher-runbook"></a>导入观察程序 runbook

本教程使用名为 **Watch-NewFile** 的观察程序 runbook 查找某个目录中的新文件。 该观察程序 runbook 检索文件夹中的文件的最后已知写入时间，并查找比该水印更新的任何文件。

可使用 [PowerShell 库](https://www.powershellgallery.com)完成这一导入过程。

1. 导航到 [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd) 的库页面。
2. 在“Azure 自动化”选项卡下单击“部署到 Azure 自动化” 。

还可以使用以下步骤将此 runbook 从门户导入到自动化帐户中。

1. 打开你的自动化帐户，然后单击“Runbook”页。
2. 单击“浏览库”。
3. 搜索“观察程序 runbook”，选择“用于在目录中查找新文件的观察程序 runbook”，然后单击“导入”  。
  ![从 UI 导入自动化 runbook](media/automation-watchers-tutorial/importsourcewatcher.png)
4. 为该 runbook 提供名称和说明，然后单击“确定”以将该 runbook 导入到自动化帐户中。
5. 选择“编辑”，然后单击“发布”。 收到提示时，单击“是”以发布该 runbook。

## <a name="create-an-automation-variable"></a>创建自动化变量

使用一个[自动化变量](automation-variables.md)来存储前面的 runbook 从每个文件读取和存储的时间戳。

1. 在“共享资源”下选择“变量”，并单击“+ 添加变量”  。
1. 输入“Watch-NewFileTimestamp”作为名称。
1. 选择“日期时间”作为类型。
1. 单击“创建”以创建自动化变量。

## <a name="create-an-action-runbook"></a>创建操作 runbook

在观察程序任务中，操作 runbook 用来对从观察程序 runbook 传递给它的数据执行操作。 务必从 [PowerShell 库](https://www.powershellgallery.com)导入名为 Process-NewFile 的预定义操作 runbook。 

创建操作 runbook：

1. 导航到 [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf) 的库页面。
2. 在“Azure 自动化”选项卡下单击“部署到 Azure 自动化” 。

还可以将此 runbook 从 Azure 门户导入到自动化帐户中：

1. 导航到自动化帐户，在“流程自动化”下选择“Runbook” 。
1. 单击“浏览库”。
1. 搜索“观察程序操作”，在目录中选择“处理由观察程序 runbook 触发的事件的监视操作”，然后选择“导入”  。
  ![从 UI 中导入操作 runbook](media/automation-watchers-tutorial/importsourceaction.png)
1. 为该 runbook 提供名称和说明，然后单击“确定”以将该 runbook 导入到自动化帐户中。
1. 选择“编辑”，然后单击“发布”。 收到提示时，单击“是”以发布该 runbook。

## <a name="create-a-watcher-task"></a>创建观察程序任务

在此步骤中，你将配置引用在前面几节中定义的观察程序和操作 runbook 的观察程序任务。

1. 导航到自动化帐户，在“流程自动化”下选择“观察程序任务” 。
1. 选择“观察程序任务”页，然后单击“+ 添加观察程序任务”。
1. 输入“WatchMyFolder”作为名称。

1. 选择“配置观察程序”，然后选择“Watch-NewFile”runbook 。

1. 输入以下参数值：

   * **FOLDERPATH** - 混合 Runbook 辅助角色上用于创建新文件的文件夹，例如 d:\examplefiles。
   * **EXTENSION** - 配置的扩展。 留空以便处理所有文件扩展名。
   * **RECURSE** - 递归操作。 将此值保留为默认值。
   * **RUN SETTINGS** - 用于运行 runbook 的设置。 选择混合辅助角色。

1. 单击“确定”，然后单击“选择”以返回到观察程序页 。
1. 选择“配置操作”，然后选择“Process-NewFile”runbook 。
1. 输入以下参数值：

   * **EVENTDATA** - 时间数据。 留空。 从观察程序 runbook 传入数据。
   * **Run Settings** - 用于运行 runbook 的设置。 当此 runbook 在 Azure 自动化中运行时保留为 Azure。

1. 单击“确定”，然后单击“选择”以返回到观察程序页 。
1. 单击“确定”创建观察程序任务。

![从 UI 配置观察程序操作](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>触发观察程序

必须按照下面描述的方式运行测试，以确保观察程序任务按预期工作。 

1. 远程登录到混合 Runbook 辅助角色。 
2. 打开 **PowerShell** 并在文件夹中创建一个测试文件。

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

以下示例显示了预期的输出。

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>检查输出

1. 导航到自动化帐户，在“流程自动化”下选择“观察程序任务” 。
1. 选择观察程序任务“WatchMyFolder”。
1. 单击“流”下的“查看观察程序流”，查看观察程序是否发现新文件并启动了此操作 runbook 。
1. 若要查看操作 runbook 作业，请单击“查看观察程序操作作业”。 可以选择各个作业并查看作业详细信息。

   ![UI 中的观察程序操作作业](media/automation-watchers-tutorial/WatcherActionJobs.png)

可以在下面的示例中看到当发现了新文件时的预期输出：

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 导入观察程序 runbook
> * 创建自动化变量
> * 创建操作 runbook
> * 创建观察程序任务
> * 触发观察程序
> * 检查输出

单击以下链接可了解有关撰写 runbook 的详细信息。

> [!div class="nextstepaction"]
> [创建 PowerShell Runbook](learn/automation-tutorial-runbook-textual-powershell.md)