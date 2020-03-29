---
title: 在本地调试 PowerShell Azure 函数
description: 了解如何使用 PowerShell 开发功能。
author: tylerleonhardt
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 133e89bd9187ae5e48fa208b407678760d31adfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163754"
---
# <a name="debug-powershell-azure-functions-locally"></a>在本地调试 PowerShell Azure 函数

Azure 函数允许您将函数开发为 PowerShell 脚本。

您可以使用以下标准开发工具在本地调试 PowerShell 函数，就像使用以下标准开发工具一样调试任何 PowerShell 脚本：

* [可视化工作室代码](https://code.visualstudio.com/)：微软的免费、轻量和开源文本编辑器，带有 PowerShell 扩展，提供完整的 PowerShell 开发体验。
* PowerShell 控制台：使用用于调试任何其他 PowerShell 进程的命令进行调试。

[Azure 函数核心工具](functions-run-local.md)支持 Azure 函数的本地调试，包括 PowerShell 函数。

## <a name="example-function-app"></a>示例函数应用

本文中使用的函数应用具有单个 HTTP 触发功能，并且具有以下文件：

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

此函数应用程序类似于您完成[PowerShell 快速启动](functions-create-first-function-powershell.md)时得到的应用程序。

中的`run.ps1`函数代码如下所示：

```powershell
param($Request)

$name = $Request.Query.Name

if($name) {
    $status = 200
    $body = "Hello $name"
}
else {
    $status = 400
    $body = "Please pass a name on the query string or in the request body."
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = $status
    Body = $body
})
```

## <a name="set-the-attach-point"></a>设置附加点

要调试任何 PowerShell 函数，该函数需要停止才能附加调试器。 `Wait-Debugger` cmdlet 停止执行并等待调试器。

您只需向`Wait-Debugger``if`语句上方的 cmdlet 添加调用，如下所示：

```powershell
param($Request)

$name = $Request.Query.Name

# This is where we will wait for the debugger to attach
Wait-Debugger

if($name) {
    $status = 200
    $body = "Hello $name"
}
# ...
```

调试从语句`if`开始。 

就`Wait-Debugger`位，您现在可以使用 Visual Studio 代码或 PowerShell 控制台调试函数。

## <a name="debug-in-visual-studio-code"></a>可视化工作室代码中的调试

要在可视化工作室代码中调试 PowerShell 功能，您必须安装以下内容：

* [Visual Studio 代码的 PowerShell 扩展](/powershell/scripting/components/vscode/using-vscode)
* [适用于 Visual Studio Code 的 Azure Functions 扩展](functions-create-first-function-vs-code.md)
* [PowerShell 核心 6.2 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)

安装这些依赖项后，加载现有的 PowerShell 函数项目，或[创建第一个 PowerShell 函数项目](functions-create-first-function-powershell.md)。

>[!NOTE]
> 如果项目没有所需的配置文件，系统将提示您添加这些文件。

### <a name="set-the-powershell-version"></a>设置 PowerShell 版本

PowerShell 核心与 Windows PowerShell 并排安装。 将 PowerShell Core 设置为 PowerShell 版本，以便与 Visual Studio 代码的 PowerShell 扩展版一起使用。

1. 按 F1 显示命令托盘，然后搜索`Session`。

1. 选择**电源外壳：显示会话菜单**。

1. 如果您的**当前会话**不是**PowerShell 核心 6，** 请选择 **"切换到：PowerShell 核心 6**"。

打开 PowerShell 文件后，可以看到窗口右下角以绿色显示的版本。 选择此文本还会显示会话菜单。 要了解更多信息，请参阅[选择要使用的 PowerShell 版本](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)。

### <a name="start-the-function-app"></a>启动函数应用

验证`Wait-Debugger`在要附加调试器的函数中设置的。  添加`Wait-Debugger`后，您可以使用 Visual Studio 代码调试函数应用。

选择**调试**窗格，然后**附加到 PowerShell 函数**。

![调试器](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

您还可以按 F5 键开始调试。

启动调试操作执行以下任务：

* 在`func extensions install`终端中运行以安装函数应用所需的任何 Azure 函数扩展。
* 在`func host start`终端中运行以在函数主机中启动函数应用。
* 将 PowerShell 调试器附加到函数运行时内的 PowerShell 运行空间。

>[!NOTE]
> 您需要确保 PSWorkerInProcConininSToBound 设置为 1，以确保在可视化工作室代码中获得正确的调试体验。 这是默认值。

运行函数应用后，您需要单独的 PowerShell 控制台来调用 HTTP 触发功能。

在这种情况下，PowerShell 控制台是客户端。 `Invoke-RestMethod`用于触发函数。

在 PowerShell 控制台中，运行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

您会注意到不会立即返回响应。 这是因为`Wait-Debugger`已经附加了调试器，PowerShell 执行尽可能快地进入中断模式。 这是因为[BreakAll的概念](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)，这是稍后解释。 按下该`continue`按钮后，调试器现在在 之后的`Wait-Debugger`行上中断。

此时，调试器已连接，您可以执行所有正常的调试器操作。 有关在 Visual Studio 代码中使用调试器的详细信息，请参阅[官方文档](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)。

继续并完全调用脚本后，您会注意到：

* 已返回`Invoke-RestMethod`结果的 PowerShell 控制台
* 可视化工作室代码中的 PowerShell 集成控制台正在等待脚本执行

稍后，当您调用同一函数时，PowerShell 扩展中的调试器将在`Wait-Debugger`之后中断。

## <a name="debugging-in-a-powershell-console"></a>在 PowerShell 控制台中调试

>[!NOTE]
> 本节假定您已阅读[Azure 函数核心工具文档](functions-run-local.md)，并知道如何使用 命令`func host start`启动函数应用。

打开控制台，`cd`进入函数应用的目录中，并运行以下命令：

```sh
func host start
```

在函数应用运行并`Wait-Debugger`到位后，您可以附加到进程。 您确实需要两个 PowerShell 控制台。

其中一个控制台充当客户端。 从中，您调用`Invoke-RestMethod`以触发函数。 例如，您可以运行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

您会注意到它不会返回响应，这是 的结果`Wait-Debugger`。 PowerShell 运行空间正在等待连接调试器。 让我们附上它。

在其他 PowerShell 控制台中，运行以下命令：

```powershell
Get-PSHostProcessInfo
```

此 cmdlet 返回类似于以下输出的表：

```output
ProcessName ProcessId AppDomainName
----------- --------- -------------
dotnet          49988 None
pwsh            43796 None
pwsh            49970 None
pwsh             3533 None
pwsh            79544 None
pwsh            34881 None
pwsh            32071 None
pwsh            88785 None
```

记下`ProcessId`表中的项的`ProcessName`。 `dotnet` 此过程是您的函数应用。

接下来，运行以下代码段：

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

启动后，调试器将中断并显示类似以下输出的内容：

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

此时，您将在[PowerShell 调试器](/powershell/module/microsoft.powershell.core/about/about_debuggers)中的断点处停止。 从这里，您可以执行所有常见的调试操作、单步执行、单步执行、继续、退出等操作。 要查看控制台中可用的完整调试命令集，请运行 。 `h` `?`

您还可以在此级别使用`Set-PSBreakpoint`cmdlet 设置断点。

继续并完全调用脚本后，您会注意到：

* 执行的 PowerShell 控制台`Invoke-RestMethod`现已返回结果。
* 执行的 PowerShell 控制台`Debug-Runspace`正在等待脚本执行。

您可以再次调用相同的函数（例如使用`Invoke-RestMethod`），调试器在`Wait-Debugger`命令后就会中断。

## <a name="considerations-for-debugging"></a>调试注意事项

调试函数代码时，请记住以下问题。

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll`可能导致调试器在意外位置中断

PowerShell 扩展使用`Debug-Runspace`，这反过来又依赖于 PowerShell 的功能`BreakAll`。 此功能告诉 PowerShell 停止执行的第一个命令。 此行为使您能够在调试的运行空间中设置断点。

Azure 函数运行时在实际调用`run.ps1`脚本之前运行一些命令，因此调试器最终可能在`Microsoft.Azure.Functions.PowerShellWorker.psm1`或`Microsoft.Azure.Functions.PowerShellWorker.psd1`中分解。

如果发生此中断，请运行`continue``c`或 命令跳过此断点。 然后，在预期的断点停止。

## <a name="next-steps"></a>后续步骤

要了解有关使用 PowerShell 开发函数的更多内容，请参阅[Azure 函数 PowerShell 开发人员指南](functions-reference-powershell.md)。
