---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949957"
---
## <a name="create-the-local-function-app-project"></a>创建本地函数应用项目

从命令行运行以下命令，以便在当前本地目录的 `MyFunctionProj` 文件夹中创建一个函数应用项目。 也会在 `MyFunctionProj` 中创建一个 GitHub 存储库。

```command
func init MyFunctionProj
```

当系统提示时，请从下面的语言选项中选择一个辅助角色运行时：

+ `dotnet`：创建一个 .NET 类库项目 (.csproj)。
+ `node`：创建一个基于 Node.js 的项目。 选择 `javascript` 或 `typescript`。 
+ `python`：对于 Python 项目，请改为完成[在 Azure 中创建 HTTP 触发函数](../articles/azure-functions/functions-create-first-function-python.md)。
+ `powershell`：对于 PowerShell 项目，请改为完成[在 Azure 中创建第一个 PowerShell 函数](../articles/azure-functions/functions-create-first-function-powershell.md)。 


创建项目后，使用以下命令导航到新的 `MyFunctionProj` 项目文件夹。

```command
cd MyFunctionProj
```
