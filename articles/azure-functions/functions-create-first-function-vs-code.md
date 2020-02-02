---
title: 在 Azure 中使用 Visual Studio Code 创建你的第一个函数
description: 使用 Visual Studio Code 中的 Azure Functions扩展创建一个简单的 HTTP 触发函数并将其发布到 Azure。
ms.topic: quickstart
ms.date: 01/10/2020
ms.custom: mvc, devcenter
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 0540c7b01d693975f34515c7d13f0477ac74d4a1
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842094"
---
# <a name="quickstart-create-an-azure-functions-project-using-visual-studio-code"></a>快速入门：使用 Visual Studio Code 创建 Azure Functions 项目

在本文中，你将使用 Visual Studio Code 来创建响应 HTTP 请求的函数。 在本地测试代码后，将代码部署到 Azure Functions 的无服务器环境。 完成本快速入门会从你的 Azure 帐户中扣取最多几美分的费用。 

我们还为本文提供了[基于 CLI 的版本](functions-create-first-azure-function-azure-cli.md)。

## <a name="prerequisites"></a>必备条件

+ 安装在某个[受支持的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上的 [Visual Studio Code](https://code.visualstudio.com/)。 
::: zone pivot="programming-language-csharp"
+ Visual Studio Code 的 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
::: zone-end
::: zone pivot="programming-language-python"
+ Azure Functions 支持的 [Python 3.7](https://www.python.org/downloads/release/python-375/) 或 [Python 3.6](https://www.python.org/downloads/release/python-368/)。 目前不支持 Python 3.8。 

+ Visual Studio Code 的 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2+](https://www.microsoft.com/net/download)

+ [Visual Studio Code 的 PowerShell 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)。 
::: zone-end

+ Visual Studio Code 的 [Azure Functions 扩展](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 

+ 具有活动订阅的 [Azure 帐户](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-an-azure-functions-project"></a>创建本地项目 

在本部分，你将使用 Visual Studio Code 以所选语言创建一个本地 Azure Functions 项目。 稍后在本文中，你要将函数代码发布到 Azure。 

1. 在活动栏中选择“Azure”图标，然后在“Azure:  函数”区域中选择“创建新项目...”图标。 

    ![选择“创建新项目”](media/functions-create-first-function-vs-code/create-new-project.png)

1. 为项目工作区选择目录位置，然后选择“选择”  。

    > [!NOTE]
    > 这些步骤已设计为在工作区之外完成。 在这种情况下，请不要选择属于工作区内的项目文件夹。

1. 根据提示提供以下信息：

    ::: zone pivot="programming-language-csharp"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 选择函数项目的语言 | C# |
    | 选择版本 | Azure Functions v2 | 
    | 为项目的第一个函数选择模板 | HTTP 触发器 | 
    | 提供函数名称 | HttpExample | 
    | 提供命名空间 | My.Functions | 
    | 授权级别 | 匿名 | 
    | 选择打开项目的方式 | 添加到工作区 |

    ::: zone-end

    ::: zone pivot="programming-language-javascript"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 选择函数项目的语言 | JavaScript | 
    | 选择版本 | Azure Functions v2 | 
    | 为项目的第一个函数选择模板 | HTTP 触发器 | 
    | 提供函数名称 | HttpExample | 
    | 授权级别 | 匿名 | 
    | 选择打开项目的方式 | 添加到工作区 |

    ::: zone-end

    ::: zone pivot="programming-language-typescript"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 选择函数项目的语言 | TypeScript | 
    | 选择版本 | Azure Functions v2 | 
    | 为项目的第一个函数选择模板 | HTTP 触发器 | 
    | 提供函数名称 | HttpExample | 
    | 授权级别 | 匿名 | 
    | 选择打开项目的方式 | 添加到工作区 |

    ::: zone-end

    ::: zone pivot="programming-language-powershell"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 选择函数项目的语言 | PowerShell | 
    | 选择版本 | Azure Functions v2 | 
    | 为项目的第一个函数选择模板 | HTTP 触发器 | 
    | 提供函数名称 | HttpExample | 
    | 授权级别 | 匿名 | 
    | 选择打开项目的方式 | 添加到工作区 |

    ::: zone-end

    ::: zone pivot="programming-language-python"

    | Prompt | 值 | 
    | ------ | ----- | 
    | 选择函数项目的语言 | Python | 
    | 选择版本 | Azure Functions v2 | 
    | 选择一个 Python 别名以创建虚拟环境 | Python 别名 | 
    | 为项目的第一个函数选择模板 | HTTP 触发器 | 
    | 提供函数名称 | HttpExample | 
    | 授权级别 | 匿名 | 
    | 选择打开项目的方式 | 添加到工作区 | 

    ::: zone-end

1. Visual Studio Code 将执行以下操作：

    + 在新工作区中创建包含 [host.json](functions-host-json.md) 和 [local.settings.json](functions-run-local.md#local-settings-file) 配置文件的 Azure Functions 项目。 

    ::: zone pivot="programming-language-csharp"

    + 创建一个实现函数的 [HttpExample.cs 类库文件](functions-dotnet-class-library.md#functions-class-library-project)。

    ::: zone-end
        
    ::: zone pivot="programming-language-javascript"
    
    + 在根文件夹中创建 package.json 文件。

    + 创建一个 HttpExample 文件夹，其中包含 [function.json 定义文件](functions-reference-node.md#folder-structure)和 [index.js 文件](functions-reference-node.md#exporting-a-function)（包含函数代码的 Node.js 文件）
    
    ::: zone-end
    
    ::: zone pivot="programming-language-typescript"
    
    + 在根文件夹中创建 package.json 文件和 tsconfig.json 文件。

    + 创建一个 HttpExample 文件夹，其中包含 [function.json 定义文件](functions-reference-node.md#folder-structure)和 [index.ts 文件](functions-reference-node.md#typescript)（包含函数代码的 TypeScript 文件）
    
    ::: zone-end
    
    ::: zone pivot="programming-language-powershell"
    
    + 创建一个 HttpExample 文件夹，其中包含 [function.json 定义文件](functions-reference-python.md#programming-model)和 run.ps1 文件（包含函数代码）。
    
    ::: zone-end
    
    ::: zone pivot="programming-language-python"
    
    + 创建一个项目级别的 requirements.txt 文件，其中列出了函数所需的包。
    
    + 创建一个 HttpExample 文件夹，其中包含 [function.json 定义文件](functions-reference-python.md#programming-model)和 \_\_init\_\_.py 文件（包含函数代码）。
    
    ::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-python"

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

::: zone-end

确认该函数可以在本地计算机上正确运行以后，可以使用 Visual Studio Code 将项目直接发布到 Azure。 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>在 Azure 中运行函数

1. 从“输出”  面板复制 HTTP 触发器的 URL。 同样，请将 `name` 查询字符串作为 `?name=<yourname>` 添加到此 URL 的末尾并执行请求。

    调用 HTTP 触发的函数的 URL 应采用以下格式：

        http://<functionappname>.azurewebsites.net/api/httpexample?name=<yourname> 

1. 将 HTTP 请求的这个新 URL 粘贴到浏览器的地址栏中。 以下示例演示浏览器中函数返回的对远程 GET 请求的响应： 

    ![浏览器中的函数响应](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="clean-up-resources"></a>清理资源

若要继续执行下一步骤[将 Azure 存储队列绑定添加到函数](functions-add-output-binding-storage-queue-vs-code.md)，需要保留到目前为止构建的所有资源。

否则，可以使用以下步骤删除函数应用及其相关资源，以免产生任何额外的费用。

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

若要详细了解 Functions 的费用，请参阅[估算消耗计划成本](functions-consumption-costs.md)。

## <a name="next-steps"></a>后续步骤

你已使用 Visual Studio Code 通过简单的 HTTP 触发函数创建了函数应用。 在下一篇文章中，将通过添加输出绑定来扩展该函数。 此绑定将 HTTP 请求中的字符串写入 Azure 队列存储队列中的消息。 

> [!div class="nextstepaction"]
> [将 Azure 存储队列绑定添加到函数](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
