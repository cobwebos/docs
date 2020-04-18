---
title: 在 Azure 自动化帐户中创建观察程序任务
description: 了解如何在 Azure 自动化帐户中创建观察程序任务，以观察在文件夹中创建的新文件。
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617362"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>创建 Azure 自动化观察程序任务来跟踪本地计算机上的文件更改

Azure 自动化使用观察程序任务使用 PowerShell Runbook 查找事件并触发操作。 观察程序任务包含两个部分，观察器和操作。 观察程序运行簿以观察程序任务中定义的间隔运行，并将数据输出到操作 Runbook。 

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

* Azure 订阅。 如果您还没有，您可以[激活您的 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [自动化帐户](automation-offering-get-started.md)，用于保存观察程序、操作 runbook 和观察程序任务。
* 一个[混合 runbook 辅助角色](automation-hybrid-runbook-worker.md)，观察程序任务在其中运行。
* 电源外壳运行簿。 观察程序任务不支持 PowerShell 工作流运行簿。

> [!NOTE]
> Azure 中国不支持观察程序任务。

## <a name="import-a-watcher-runbook"></a>导入观察程序 runbook

本教程使用名为 **Watch-NewFile** 的观察程序 runbook 查找某个目录中的新文件。 该观察程序 runbook 检索文件夹中的文件的最后已知写入时间，并查找比该水印更新的任何文件。

此导入过程可以通过[PowerShell 库](https://www.powershellgallery.com)完成。

1. 导航到["观看-新文件.ps1"](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd)的库页面。
2. 在**Azure 自动化**选项卡下，单击"**部署到 Azure 自动化**"。

您还可以使用以下步骤从门户将此 Runbook 导入自动化帐户。

1. 打开你的自动化帐户，然后单击“Runbook”页。
2. 单击 **"浏览库**"。
3. 搜索**观察程序运行簿**，选择**在目录中查找新文件的观察程序运行簿**，然后单击"**导入**"。
  ![从 UI 导入自动化 runbook](media/automation-watchers-tutorial/importsourcewatcher.png)
4. 为 Runbook 指定名称和说明，然后单击"**确定"** 将 Runbook 导入自动化帐户。
5. 选择“编辑”****，然后单击“发布”****。 当出现提示时，单击"**是**"以发布 Runbook。

## <a name="create-an-automation-variable"></a>创建自动化变量

使用一个[自动化变量](automation-variables.md)来存储前面的 runbook 从每个文件读取和存储的时间戳。

1. 在 **"共享资源**"下选择**变量**，然后单击 **"添加变量**"。
1. 输入名称的"观看-新文件时间"戳。
1. 选择类型的日期时间。
1. 单击 **"创建**"以创建自动化变量。

## <a name="create-an-action-runbook"></a>创建操作 runbook

在观察程序任务中，操作 runbook 用来对从观察程序 runbook 传递给它的数据执行操作。 您必须从[PowerShell 库](https://www.powershellgallery.com)导入名为 **"进程-新文件**"的预定义操作运行簿。 

要创建操作运行簿，

1. 导航到[进程-新文件.ps1 的](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf)库页面。
2. 在**Azure 自动化**选项卡下，单击"**部署到 Azure 自动化**"。

还可以从 Azure 门户将此 Runbook 导入自动化帐户：

1. 导航到您的自动化帐户，并在 **"流程自动化**"下选择**Runbook。**
1. 单击 **"浏览库**"。
1. 搜索**观察程序操作**，选择**处理由观察程序运行簿触发的事件的观察程序操作**，然后单击"**导入**"。
  ![从 UI 中导入操作 runbook](media/automation-watchers-tutorial/importsourceaction.png)
1. 为 Runbook 指定名称和说明，然后单击"**确定"** 将 Runbook 导入自动化帐户。
1. 选择“编辑”****，然后单击“发布”****。 当出现提示时，单击"**是**"以发布 Runbook。

## <a name="create-a-watcher-task"></a>创建观察程序任务

在此步骤中，配置观察程序任务，引用前面各节中定义的观察程序和操作运行簿。

1. 导航到您的自动化帐户，并在 **"过程自动化**"下选择**观察程序任务**。
1. 选择"观察程序任务"页，然后单击 **"添加观察程序"任务**。
1. 输入 **"监视我的文件夹**"作为名称。

1. 选择 **"配置观察程序**"并选择 **"监视-新文件**"运行簿。

1. 输入以下参数值：

   * **FOLDERPATH** - 混合 Runbook 工作线程上的文件夹，其中创建新文件，例如**d：\示例文件**。
   * **扩展**- 配置的扩展。 留空以便处理所有文件扩展名。
   * **RECURSE** - 递归操作。 保留此值作为默认值。
   * **运行设置**- 用于运行 Runbook 的设置。 选择混合辅助角色。

1. 单击 **"确定**"，然后**选择**返回到"观察程序"页。
1. 选择 **"配置操作**"并选择 **"进程-新文件**"运行簿。
1. 输入以下参数值：

   * **事件数据**- 事件数据。 留空。 从观察程序 runbook 传入数据。
   * **运行设置**- 用于运行 Runbook 的设置。 离开 Azure，因为此 Runbook 在 Azure 自动化中运行。

1. 单击 **"确定**"，然后**选择**返回到"观察程序"页。
1. 单击"**确定"** 以创建观察程序任务。

![从 UI 配置观察程序操作](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>触发观察程序

您必须运行如下所述的测试，以确保观察程序任务按预期工作。 

1. 远程到混合 Runbook 工作线程。 
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

1. 导航到您的自动化帐户，并在 **"过程自动化**"下选择**观察程序任务**。
1. 选择观察程序任务**WatchMyFolder**。
1. 单击 **"流**"下的 **"查看观察程序流**"以查看观察程序已找到新文件并启动操作运行簿。
1. 要查看操作运行簿作业，请单击 **"查看观察程序操作作业**"。 可以选择每个作业以查看作业的详细信息。

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
> [我的第一个PowerShell运行簿](automation-first-runbook-textual-powershell.md)。

