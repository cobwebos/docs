---
title: 将绑定添加到 Azure Functions 中的现有函数
description: 了解如何将绑定添加到 Azure Functions 项目中的现有函数。
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 23b7b05f9a9f9da5a48511ee555e3b6184a74179
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91654097"
---
# <a name="add-bindings-to-an-existing-function-in-azure-functions"></a>将绑定添加到 Azure Functions 中的现有函数

创建函数时，特定于语言的触发器代码将从一组触发器模板添加到项目中。 如果要使用输入或输出绑定将函数连接到其他服务，则必须在函数中添加特定的绑定定义。 有关绑定的详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

## <a name="local-development"></a>本地开发       

在本地开发函数时，需要更新函数代码以添加绑定。 使用 Visual Studio Code 可以更轻松地将绑定添加到函数。  

### <a name="visual-studio-code"></a>Visual Studio Code

使用 Visual Studio Code 开发函数时，如果函数使用 function.js文件，则 Azure Functions 扩展可以自动将绑定添加到文件的现有 function.js。 若要了解详细信息，请参阅 [添加输入和输出绑定](functions-develop-vs-code.md#add-input-and-output-bindings)。   

### <a name="manually-add-bindings-based-on-examples"></a>基于示例手动添加绑定

将绑定添加到现有函数时，如果你的语言使用，则需要更新有关配置文件的函数代码和 function.js。 .NET 类库和 Java 函数使用特性而不是 function.js，因此，您将需要改为更新。

使用下表查找可用于指导您更新现有函数的特定绑定类型的示例。 首先，选择与你的项目相对应的 "语言" 选项卡。 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>Azure 门户

在 [Azure 门户](https://portal.azure.com)中开发函数时，将在给定函数的 " **集成** " 选项卡中添加输入和输出绑定。 新的绑定将添加到文件 function.js或方法特性中，具体取决于你的语言。 以下文章显示了如何在门户中将绑定添加到现有函数的示例：

+ [队列存储输出绑定](functions-integrate-storage-queue-output-binding.md)
+ [Azure Cosmos DB 输出绑定](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>后续步骤

+ [Azure Functions 触发器和绑定概念](functions-triggers-bindings.md)
