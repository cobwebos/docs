---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f4075b8d05c179e8115ff46c9f82751817372491
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842090"
---
## <a name="run-the-function-locally"></a>在本地运行函数

Visual Studio Code 与 [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) 集成，方便你在将内容发布到 Azure 之前在本地开发计算机上运行此项目。

1. 若要调用函数，请按 F5 启动函数应用项目。 来自 Core Tools 的输出会显示在“终端”  面板中。

1. 如果尚未安装 Azure Functions Core Tools，请在提示符下选择“安装”。  安装 Core Tools 后，应用会在“终端”  面板中启动。

1. 在“终端”  面板中，复制 HTTP 触发的函数的 URL 终结点。 

    ![Azure 本地输出](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏中。 将查询字符串 `?name=<yourname>` 追加到此 URL 并执行 GET 请求。 

1. 系统会返回响应，在浏览器中如下所示：

    ![浏览器中的函数 localhost 响应](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. 按 Shift + F5 停止 Core Tools 并断开与调试器的连接。
