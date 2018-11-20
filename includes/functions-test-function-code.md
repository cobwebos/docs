---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ae88a6132c79d9d9f930f6445e53b0f0452c9f2a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262601"
---
## <a name="test"></a>测试函数

在 Mac 或 Linux 计算机上使用 cURL 或者在 Windows 上使用 Bash 测试已部署的函数。 执行以下 cURL 命令（请将 `<app_name>` 占位符替换为 Function App 的名称）。 在 URL 的后面附加查询字符串 `&name=<yourname>`。

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![函数响应会显示在浏览器中。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

如果无法在命令行中使用 cURL，请在 Web 浏览器的地址栏中输入相同的 URL。 同样，请将 `<app_name>` 占位符替换为 Function App 的名称，在 URL 的后面附加查询字符串 `&name=<yourname>`，然后执行请求。

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![函数响应会显示在浏览器中。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
