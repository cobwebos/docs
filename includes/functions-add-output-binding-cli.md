---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673347"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>将输出绑定定义添加到函数

尽管一个函数只能有一个触发器，但它可以有多个输入和输出绑定，因此，你无需编写自定义集成代码，就能连接到其他 Azure 服务和资源。 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
在函数文件夹中的 *function.json* 文件内声明这些绑定。 在前一篇快速入门中，*HttpExample* 文件夹中的 *function.json* 文件在 `bindings` 集合中包含两个绑定：  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
每个绑定至少有一个类型、一个方向和一个名称。 在以上示例中，第一个绑定的类型为 `httpTrigger`，方向为 `in`。 对于 `in` 方向，`name` 指定在触发器调用函数时，要发送到该函数的输入参数的名称。  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
集合中的第二个绑定名为 `res`。 此 `http` 绑定是用于写入 HTTP 响应的输出绑定 (`out`)。 

若要从此函数写入 Azure 存储队列，请添加类型为 `queue`、名称为 `msg` 的 `out` 绑定，如以下代码所示：

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
集合中第二个绑定的类型为 `http`，方向为 `out`，在本例中，`$return` 的特殊 `name` 指示此绑定使用函数的返回值，而不是提供输入参数。

若要从此函数写入 Azure 存储队列，请添加类型为 `queue`、名称为 `msg` 的 `out` 绑定，如以下代码所示：

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
集合中的第二个绑定名为 `res`。 此 `http` 绑定是用于写入 HTTP 响应的输出绑定 (`out`)。 

若要从此函数写入 Azure 存储队列，请添加类型为 `queue`、名称为 `msg` 的 `out` 绑定，如以下代码所示：

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
在这种情况下，`msg` 将作为输出参数提供给函数。 对于 `queue` 类型，还必须在 `queueName` 中指定队列的名称，并在 `connection` 中提供 Azure 存储连接的名称（来自 *local.settings.json*）。  
::: zone-end  
