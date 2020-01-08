---
title: 使用 VS Code 调试 Kusto 查询语言内联 Python-Azure 数据资源管理器
description: 了解如何使用 VS Code 调试 Kusto 查询语言（KQL）内联 Python。
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444471"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>使用 VS Code 调试 Kusto 查询语言内联 Python

Azure 数据资源管理器支持使用[python （）插件](/azure/kusto/query/pythonplugin)运行使用 Kusto 查询语言嵌入的 python 代码。 插件运行时托管在沙箱中，这是一个隔离和安全的 Python 环境。 Python （）插件功能将 Kusto 查询语言本机功能扩展到 OSS Python 包的巨大存档。 此扩展使你能够在查询过程中运行高级算法，如机器学习、人工智能、统计和时间系列。

Kusto 查询语言工具不适用于开发和调试 Python 算法。 因此，在你最喜爱的 Python 集成开发环境（如 Jupyter、PyCharm、VS 或 VS Code）上制定算法。 完成算法后，复制并粘贴到 KQL。 为了改进和简化此工作流，Azure 数据资源管理器支持 Kusto 资源管理器或 Web UI 客户端之间的集成，以及用于创作和调试 KQL 内联 Python 代码的 VS Code。 

> [!NOTE]
> 此工作流仅可用于调试相对较小的输入表（最大为 MB）。 因此，您可能需要限制输入以便进行调试。  如果需要处理大型表，请将其限制为使用 `| take`、`| sample`或 `where rand() < 0.x`进行调试。

## <a name="prerequisites"></a>必备组件

1. 安装 Python [Anaconda 分发](https://www.anaconda.com/distribution/#download-section)版。 在 "**高级选项**" 中，选择 **"将 Anaconda 添加到我的 PATH 环境变量**"。
2. 安装 [Visual Studio Code](https://code.visualstudio.com/Download)
3. 安装[适用于 Visual Studio Code 的 Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。

## <a name="run-your-query-in-your-client-application"></a>在客户端应用程序中运行查询

1. 在客户端应用程序中，使用 `set query_python_debug;`
1. 运行查询。
    * Kusto 资源管理器： *debug_python 通过 py*脚本自动启动 VS Code。
    * Kusto Web UI： 
        1. 下载并保存*debug_python py*、 *df .txt*和*kargs*。 在窗口中，选择 "**允许**"。 **保存**所选目录中的文件。 

            ![Web UI 下载内联 python 文件](media/debug-inline-python/webui-inline-python.png)

        1. 右键单击*debug_python py* ，然后打开 with VS code。 
        *Debug_python py*脚本包含 KQL 查询中的内联 python 代码，并以模板代码为前缀，以从初始化输入数据帧和*kargs* *中的*参数字典。    
            
1. 在 VS code 中，启动 VS code 调试器： **Debug** > **启动调试（F5）** ，选择 " **Python**配置"。 调试器将启动并自动为调试内联代码而自动断点。

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>VS Code 中的内联 Python 调试是如何工作的？

1. 在达到所需的 `| evaluate python()` 子句之前，将在服务器中分析并执行查询。
1. 将调用 Python 沙盒，但不运行代码，而是序列化输入表、参数的字典和代码，然后将其发送回客户端。
1. 这三个对象保存在选定目录（Web UI）或客户端% TEMP% 目录（Kusto 资源管理器）中的三个文件*中：* *kargs、* *debug_python 和 py* 。
1. 将启动 VS code，debug_python 并使用*py*文件（其中包含用于从各自的文件中初始化 df 和 kargs 的前缀代码）进行预加载，后跟 KQL 查询中嵌入的 python 脚本。

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

    请参阅生成的表：

    | 不可以  | 四  |
    |---------|---------|
    | 第     |   第      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. 使用 `set query_python_debug;`在客户端应用程序中运行相同的 KQL 查询：

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code 已启动：

    ![启动 VS code](media/debug-inline-python/launch-vs-code.png)

1. VS Code 调试并在调试控制台中打印 "result" 数据帧：

    ![VS code 调试](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Python 沙盒映像和本地安装之间可能存在差异。 [通过查询插件来检查特定包的沙盒映像](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl)。
