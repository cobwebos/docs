---
title: 使用 VS Code 调试 Kusto 查询语言内联 Python - Azure 数据资源管理器
description: 了解如何使用 VS Code 调试 Kusto 查询语言 (KQL) 内联 Python。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444471"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>使用 VS Code 调试 Kusto 查询语言内联 Python

Azure 数据资源管理器支持使用 [python() 插件](/azure/kusto/query/pythonplugin)运行 Kusto 查询语言中嵌入的 Python 代码。 插件运行时托管在沙盒中，这是一个隔离的安全 Python 环境。 python() 插件功能扩展了 Kusto 查询语言的本机功能，并提供了大量的 OSS Python 包。 使用此扩展，你可以在查询中运行高级算法，例如机器学习、人工智能、统计和时间系列。

Kusto 查询语言工具不适用于开发和调试 Python 算法。 因此，请在你喜欢使用的 Python 集成开发环境（例如 Jupyter、PyCharm、VS 或 VS Code）中开发算法。 完成算法后，复制并粘贴到 KQL 中。 为了改进和简化此工作流，Azure 数据资源管理器支持在 Kusto Explorer 或 Web UI 客户端与 VS Code 之间进行集成，以便创作和调试 KQL 内联 Python 代码。 

> [!NOTE]
> 此工作流仅可用来调试相对较小的输入表（最大为几 MB）。 因此，你可能需要限制用于调试的输入。  如果需要处理大型表，请使用 `| take`、`| sample` 或 `where rand() < 0.x` 限制对该表的调试。

## <a name="prerequisites"></a>先决条件

1. 安装 Python [Anaconda 分发版](https://www.anaconda.com/distribution/#download-section)。 在“高级选项”**** 中，选择“将 Anaconda 添加到我的 PATH 环境变量”****。
2. 安装[可视化工作室代码](https://code.visualstudio.com/Download)
3. 安装[适用于 Visual Studio Code 的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。

## <a name="run-your-query-in-your-client-application"></a>在客户端应用程序中运行查询

1. 在客户端应用程序中，为包含内联 Python 的查询添加 `set query_python_debug;` 前缀
1. 运行查询。
    * Kusto 资源管理器：VS 代码自动启动与*debug_python.py*脚本。
    * Kusto Web UI： 
        1. 下载并保存 *debug_python.py*、*df.txt* 和 *kargs.txt*。 在窗口中，选择“允许”****。 将文件**保存**到所选目录中。 

            ![Web UI 将下载内联 python 文件](media/debug-inline-python/webui-inline-python.png)

        1. 右键单击 *debug_python.py* 并使用 VS code 将其打开。 
        *debug_python.py* 脚本包含来自 KQL 查询的内联 Python 代码（以模板代码作为前缀，用以初始化来自 *df.txt* 的数据帧和来自 *kargs.txt* 的参数字典）。    
            
1. 在 VS 代码中，启动 VS 代码调试器：**调试** > **启动调试 （F5），** 选择**Python**配置。 调试器将启动，并自动设置断点来调试内联代码。

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>VS Code 中的内联 Python 调试如何工作？

1. 查询将在服务器中进行分析和执行，直至到达所需的 `| evaluate python()` 子句。
1. 将调用 Python 沙盒，但不运行代码，而是对输入表、参数字典和代码进行序列化，然后将其发送回客户端。
1. 这三个对象保存在三个文件中：*df.txt*、*kargs.txt* 和 *debug_python.py*，这些文件位于所选目录 (Web UI) 或客户端 %TEMP% 目录 (Kusto Explorer) 中。
1. VS code 将启动并预加载 *debug_python.py* 文件，该文件包含一个前缀代码，用以初始化 df 和 kargs（分别来自它们对应的文件），前缀代码后面是嵌入在 KQL 查询中的 Python 脚本。

## <a name="query-example"></a>查询示例

1. 在客户端应用程序中运行以下 KQL 查询：

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    查看生成的表：

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. 使用 `set query_python_debug;` 在客户端应用程序中运行相同的 KQL 查询：

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code 将启动：

    ![启动 VS Code](media/debug-inline-python/launch-vs-code.png)

1. VS Code 将进行调试并在调试控制台中输出“result”数据帧：

    ![VS Code 调试](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Python 沙盒映像与你的本地安装之间可能存在差异。 [通过对插件进行查询来检查特定包的沙盒映像](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)。
