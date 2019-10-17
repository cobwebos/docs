---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/29/2019
ms.author: glenga
ms.openlocfilehash: f16852cdc18053a3a8e375dc6f14e39bb069afef
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329615"
---
无需编写自己的集成代码，即可使用 Azure Functions 将 Azure 服务和其他资源连接到函数。 这些绑定表示输入和输出，在函数定义中声明。  绑定中的数据作为参数提供给函数。 触发器是一种特殊类型的输入绑定。  尽管一个函数只有一个触发器，但它可以有多个输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 触发器和绑定的概念](../articles/azure-functions/functions-triggers-bindings.md)。