---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.openlocfilehash: b4b2409928b6a4196738c7cc6c7040e781d34686
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "80056604"
---
1. 若要运行函数，请在 Visual Studio 中按 F5。 你可能需要启用防火墙例外，这样工具才能处理 HTTP 请求。 在本地运行函数时，永远不会强制实施授权级别。

2. 从 Azure Functions 运行时输出复制函数的 URL。

    ![Azure 本地运行时](./media/functions-run-function-test-local-vs/functions-debug-local-vs.png)

3. 将 HTTP 请求的 URL 粘贴到浏览器的地址栏中。 将查询字符串 `?name=<YOUR_NAME>` 追加到此 URL 并运行请求。 下图显示了浏览器中函数返回的对本地 GET 请求的响应。 

    ![浏览器中的函数 localhost 响应](./media/functions-run-function-test-local-vs/functions-run-browser-local-vs.png)

4. 若要停止调试，请在 Visual Studio 中按 Shift+F5。