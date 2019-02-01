---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 914c006daf49e22ebec870a549bfdbc63f882647
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55148078"
---
## <a name="test"></a>测试函数

在 Mac 或 Linux 计算机上使用 cURL 或者在 Windows 上使用 Powershell 测试已部署的函数。 执行以下 cURL 命令（请将 `<app_name>` 占位符替换为 Function App 的名称）。 在 URL 的后面附加查询字符串 `&name=<yourname>`。

```powershell
Invoke-WebRequest -Uri "https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>"
```

```bash
curl https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
```  

![函数响应会显示在浏览器中。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

如果无法在命令行中使用 `cURL` 或 `Invoke-WebRequest`，请在 Web 浏览器的地址栏中输入相同的 URL。 同样，请将 `<app_name>` 占位符替换为 Function App 的名称，在 URL 的后面附加查询字符串 `&name=<yourname>`，然后执行请求。

    https://<app_name>.azurewebsites.net/api/MyHttpTrigger?name=<yourname>
   
![函数响应会显示在浏览器中。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
