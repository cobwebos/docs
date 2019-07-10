---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444636"
---
## <a name="create-an-azure-functions-project"></a>创建包含一个函数的 Functions 项目 

使用 Visual Studio Code 中的 Azure Functions 项目模板可创建一个项目，该项目可发布到 Azure 中的函数应用。 函数应用可将函数分组为一个逻辑单元，以用于管理、部署和共享资源。

1. 在 Visual Studio Code 中，按 F1 键打开命令面板。 在命令面板中，搜索并选择 `Azure Functions: Create new project...`。

1. 为项目工作区选择目录位置，然后选择“选择”  。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 在这种情况下，请不要选择属于工作区内的项目文件夹。

1. 按照提示提供以下信息：

    | Prompt | 值 | 说明 |
    | ------ | ----- | ----------- |
    | 选择函数应用项目的语言 | C# 或 JavaScript | 本文支持 C# 和 JavaScript。 对于 Python，请参阅[此 Python 文章](https://code.visualstudio.com/docs/python/tutorial-azure-functions)，对于 PowerShell，请参阅[此 PowerShell 文章](../articles/azure-functions/functions-create-first-function-powershell.md)。  |
    | 为项目的第一个函数选择模板 | HTTP 触发器 | 在新的函数应用中创建一个 HTTP 触发的函数。 |
    | 提供函数名称 | HttpTrigger | 按 Enter 以使用默认名称。 |
    | 提供命名空间 | My.Functions | （仅 C#）C# 类库必须具有命名空间。  |
    | 授权级别 | 函数 | 需要[函数密钥](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys)才能调用函数的 HTTP 终结点。 |
    | 选择打开项目的方式 | 添加到工作区 | 在当前工作区中创建函数应用。 |

Visual Studio Code 将在新的工作区中创建函数应用项目。 此项目包含 [host.json](../articles/azure-functions/functions-host-json.md) 和 [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file) 配置文件以及任何特定于语言的项目文件。 

还会在函数应用项目的 HttpTrigger 文件夹中创建一个新的 HTTP 触发函数。