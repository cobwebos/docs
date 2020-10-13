---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 617608f382d3331e59ae92c9eb272347c736b768
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828790"
---
Functions 允许使用密钥来增大开发期间访问 HTTP 函数终结点的难度。 除非 HTTP 触发的函数中的 HTTP 访问级别设置为 `anonymous`，否则请求中必须包含 API 访问密钥。 

尽管密钥提供默认的安全机制，但可能需要考虑使用其他选项来保护生产环境中的 HTTP 终结点。 例如，在公共应用中分发共享机密通常不是一个好做法。 如果从公共客户端调用函数，则可能要考虑实现另一种安全机制。 有关详细信息，请参阅[在生产环境中保护 HTTP 终结点](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#secure-an-http-endpoint-in-production)。

续订函数密钥值时，必须手动将更新的密钥值重新分发给调用你的函数的所有客户端。  

#### <a name="authorization-scopes-function-level"></a>授权范围（函数级别）

函数级密钥有两个访问权限作用域：

* **函数**：这些密钥仅适用于在其下定义它们的特定函数。 这些密钥用作 API 密钥时，只允许访问该函数。

* **主机**：主机范围的密钥可用于访问函数应用中的所有函数。 这些密钥用作 API 密钥时，可以访问 Function App 中的任何函数。 

命名每个密钥方便引用，并且在函数和主机级别存在名为“default”的默认密钥。 函数密钥优先于主机密钥。 如果为两个密钥定义的名称相同，则使用函数密钥。

#### <a name="master-key-admin-level"></a>主密钥（管理员级别） 

每个函数应用还有一个名为 `_master` 的管理员级主机密钥。 除了提供对应用中所有函数的主机级的访问权限外，主密钥还提供对运行时 REST API 的管理访问权限。 无法撤消此密钥。 设置 `admin` 访问级别时，请求必须使用主密钥；使用任何其他密钥会导致访问失败。

> [!CAUTION]  
> 由于主密钥在函数应用中授予提升的权限，不应与第三方共享此密钥或在本机客户端应用程序中分发此密钥。 选择管理访问级别时请慎重。
