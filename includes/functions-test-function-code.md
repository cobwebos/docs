---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 44eb25be12b908c8f951cb20948068da3bfc2928
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203122"
---
## <a name="test"></a>在 Azure 中验证函数

使用 cURL 验证已部署的函数。 使用从上一步复制的 URL（包括功能键），将查询字符串 `&name=<yourname>` 追加到该 URL。

![使用 cURL 在 Azure 中调用函数。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

也可以将复制的 URL（包括功能键）粘贴到 Web 浏览器的地址栏中。 同样，在执行请求之前，先将查询字符串 `&name=<yourname>` 追加到该 URL。

![使用 Web 浏览器调用函数。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
