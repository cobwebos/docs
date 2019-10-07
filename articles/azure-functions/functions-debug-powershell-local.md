---
title: 本地调试 PowerShell Azure Functions
description: 了解如何使用 PowerShell 开发函数。
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: 5b396ef6b00d53a313ed4fb426685c12e2c1549d
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981847"
---
# <a name="debug-powershell-azure-functions-locally"></a>本地调试 PowerShell Azure Functions

Azure Functions 允许你将函数作为 PowerShell 脚本进行开发。

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

你可以使用以下标准开发工具，在本地调试 PowerShell 函数：

* [Visual Studio Code](https://code.visualstudio.com/)：Microsoft 的免费、轻型和开源文本编辑器，其中提供了 PowerShell 扩展，提供完整的 PowerShell 开发体验。
* PowerShell 控制台：使用与调试任何其他 PowerShell 进程相同的命令进行调试。

[Azure Functions Core Tools](functions-run-local.md)支持 Azure Functions 的本地调试，包括 PowerShell 函数。

## <a name="example-function-app"></a>示例函数应用

本文中使用的函数应用有一个 HTTP 触发的函数，并且包含以下文件：

```
PSFunctionApp
 | - HttpTriggerFunction
 | | - run.ps1
 | | - function.json
 | - local.settings.json
 | - host.json
 | - profile.ps1
```

此函数应用与完成[PowerShell 快速入门](functions-create-first-function-powershell.md)时获取的应用类似。

@No__t-0 中的函数代码类似于以下脚本：

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

若要调试任何 PowerShell 函数，该函数需要停止才能附加调试器。 @No__t-0 cmdlet 将停止执行并等待调试器。

只需在 `if` 语句上添加对 @no__t cmdlet 的调用，如下所示：

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

调试从 `if` 语句开始。 

使用 `Wait-Debugger` 后，你现在可以使用 Visual Studio Code 或 PowerShell 控制台调试函数。

## <a name="debug-in-visual-studio-code"></a>在 Visual Studio Code 中调试

若要在 Visual Studio Code 中调试 PowerShell 函数，则必须安装以下各项：

* [适用于 Visual Studio Code 的 PowerShell 扩展](/powershell/scripting/components/vscode/using-vscode)
* [适用于 Visual Studio Code 的 Azure Functions 扩展](functions-create-first-function-vs-code.md)
* [PowerShell Core 6.2 或更高版本](/powershell/scripting/install/installing-powershell-core-on-windows)

安装这些依赖项后，加载现有的 PowerShell 函数项目，或者[创建第一个 Powershell 函数项目](functions-create-first-function-powershell.md)。

>[!NOTE]
> 如果你的项目没有所需的配置文件，则系统会提示你添加它们。

### <a name="set-the-powershell-version"></a>设置 PowerShell 版本

PowerShell Core 与 Windows PowerShell 并行安装。 将 PowerShell Core 设置为 powershell 版本即可用于 Visual Studio Code 的 PowerShell 扩展。

1. 按 F1 显示命令托盘，然后搜索 `Session`。

1. 选择 **PowerShell：显示会话菜单 @ no__t-0。

1. 如果**当前会话**不是**PowerShell Core 6**，请选择 **Switch：PowerShell Core 6 @ no__t-0。

打开 PowerShell 文件后，会看到该版本在窗口右下角显示为绿色。 选择此文本还会显示 "会话" 菜单。 若要了解详细信息，请参阅[选择用于扩展的 PowerShell 版本](/powershell/scripting/components/vscode/using-vscode#choosing-a-version-of-powershell-to-use-with-the-extension)。

### <a name="start-the-function-app"></a>启动函数应用

验证是否在要附加调试器的函数中设置了 `Wait-Debugger`。  添加 `Wait-Debugger` 后，可以使用 Visual Studio Code 调试 function app。

选择 "**调试**" 窗格，并**附加到 PowerShell 函数**。

![程序](https://user-images.githubusercontent.com/2644648/56166073-8a7b3780-5f89-11e9-85ce-36ed38e221a2.png)

还可以按 F5 键开始调试。

启动调试操作执行以下任务：

* 在终端中运行 `func extensions install` 以安装函数应用所需的任何 Azure Functions 扩展。
* 在终端中运行 `func host start`，以启动函数主机中的函数应用。
* 将 PowerShell 调试器附加到函数运行时中的 PowerShell 运行空间。

>[!NOTE]
> 需要确保 PSWorkerInProcConcurrencyUpperBound 设置为1，以确保 Visual Studio Code 中的调试体验正确。 这是默认值。

函数应用运行后，需要一个单独的 PowerShell 控制台来调用 HTTP 触发的函数。

在这种情况下，PowerShell 控制台就是客户端。 @No__t-0 用于触发函数。

在 PowerShell 控制台中运行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

你会注意到响应不会立即返回。 这是因为 `Wait-Debugger` 已附加调试器，并且 PowerShell 执行会尽快进入中断模式。 这是由于 BreakAll 的[概念](#breakall-might-cause-your-debugger-to-break-in-an-unexpected-place)，稍后将对此进行说明。 按下 `continue` 按钮后，调试器会立即在 `Wait-Debugger` 之后的行上中断。

此时，已附加调试器，可以执行所有正常的调试器操作。 有关在 Visual Studio Code 中使用调试器的详细信息，请参阅[官方文档](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions)。

继续并完全调用脚本后，你会注意到：

* 执行 `Invoke-RestMethod` 的 PowerShell 控制台返回了结果
* Visual Studio Code 中的 PowerShell 集成控制台正在等待脚本执行

以后调用相同的函数时，PowerShell 扩展中的调试器会在 `Wait-Debugger` 后立即中断。

## <a name="debugging-in-a-powershell-console"></a>在 PowerShell 控制台中调试

>[!NOTE]
> 本部分假设你已阅读[Azure Functions Core Tools 文档](functions-run-local.md)并知道如何使用 @no__t 的命令来启动函数应用。

在函数应用的目录中打开控制台，`cd`，并运行以下命令：

```sh
func host start
```

在运行函数应用并且 `Wait-Debugger` 就绪后，可以附加到进程。 需要两个以上的 PowerShell 控制台。

其中一个控制台充当客户端。 从此开始，调用 `Invoke-RestMethod` 来触发函数。 例如，你可以运行以下命令：

```powershell
Invoke-RestMethod "http://localhost:7071/api/HttpTrigger?Name=Functions"
```

你会注意到，它不会返回响应，这是 @no__t 的结果。 PowerShell 运行空间现在正在等待要附加的调试程序。 接下来，我们来吧。

在其他 PowerShell 控制台中，运行以下命令：

```powershell
Get-PSHostProcessInfo
```

此 cmdlet 将返回一个表，该表类似于以下输出：

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

记下表中项的 `ProcessId`，其 @no__t 为-1，@no__t 为2。 此过程是函数应用。

接下来，运行以下代码片段：

```powershell
# This enters into the Azure Functions PowerShell process.
# Put your value of `ProcessId` here.
Enter-PSHostProcess -Id $ProcessId

# This triggers the debugger.
Debug-Runspace 1
```

启动后，调试器将中断，并显示类似于以下输出的内容：

```
Debugging Runspace: Runspace1

To end the debugging session type the 'Detach' command at the debugger prompt, or type 'Ctrl+C' otherwise.

At /Path/To/PSFunctionApp/HttpTriggerFunction/run.ps1:13 char:1
+ if($name) { ...
+ ~~~~~~~~~~~
[DBG]: [Process:49988]: [Runspace1]: PS /Path/To/PSFunctionApp>>
```

此时，你会在[PowerShell 调试器](/powershell/module/microsoft.powershell.core/about/about_debuggers)中的断点处停止。 在这里，你可以执行所有常见的调试操作：逐过程执行、逐过程执行、继续、退出以及其他操作。 若要查看控制台中可用的完整调试命令集，请运行 `h` 或 `?` 命令。

你还可以在此级别上设置断点，并在 `Set-PSBreakpoint` cmdlet。

继续并完全调用脚本后，你会注意到：

* 执行 `Invoke-RestMethod` 的 PowerShell 控制台现在返回了结果。
* 在其中执行 `Debug-Runspace` 的 PowerShell 控制台正在等待脚本执行。

您可以再次调用相同的函数（例如，使用 `Invoke-RestMethod`），并且调试器将在 @no__t 1 命令后立即中断。

## <a name="considerations-for-debugging"></a>调试注意事项

在调试函数代码时，请注意以下问题。

### <a name="breakall-might-cause-your-debugger-to-break-in-an-unexpected-place"></a>`BreakAll` 可能导致调试程序在意外的位置中断

PowerShell 扩展使用 `Debug-Runspace`，后者又依赖于 PowerShell 的 @no__t 一项功能。 此功能告知 PowerShell 在执行的第一个命令处停止。 此行为使你有机会在调试的运行空间内设置断点。

在实际调用 @no__t 0 脚本之前，Azure Functions 运行时将运行几个命令，因此，调试器可能会在 `Microsoft.Azure.Functions.PowerShellWorker.psm1` 或 `Microsoft.Azure.Functions.PowerShellWorker.psd1` 中中断。

如果发生此中断，请运行 @no__t 0 或 `c` 命令跳过此断点。 然后，在预期的断点处停止。

## <a name="next-steps"></a>后续步骤

若要了解有关使用 PowerShell 开发函数的详细信息，请参阅[Azure Functions powershell 开发人员指南](functions-reference-powershell.md)。
