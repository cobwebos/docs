---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2019
ms.author: glenga
ms.openlocfilehash: 9c972508c98e87771154bd26cc166c6bea4201ee
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279343"
---
## <a name="test"></a>在 Azure 中验证函数

可以使用 Web 浏览器来验证已部署的函数。  将 URL（包括功能键）复制到 Web 浏览器的地址栏中。 在执行请求之前，先将查询字符串 `&name=<yourname>` 追加到该 URL。

![使用 Web 浏览器调用函数。](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  

或者，可以使用 cURL 来验证已部署的函数。 使用从上一步复制的 URL（包括功能键），将查询字符串 `&name=<yourname>` 追加到该 URL。

![使用 cURL 在 Azure 中调用函数。](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png) 

