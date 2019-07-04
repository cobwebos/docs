---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: a3f75b7273164abc5318f16e9ab8d9883ff0c0aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172732"
---
## <a name="test"></a>在 Azure 中测试函数

使用 cURL 测试已部署的函数。 使用从上一步复制的 URL，将查询字符串 `&name=<yourname>` 追加到该 URL，如以下示例所示：

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

![使用 cURL 在 Azure 中调用函数。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

还可以将复制的 URL 粘贴到 Web 浏览器的地址中。 同样，在执行请求之前，先将查询字符串 `&name=<yourname>` 追加到该 URL。

![使用 Web 浏览器调用函数。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
