---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515228"
---
**1.如何向客户通知即将停用的 SDK？**

Microsoft 将提供12个月的提前通知，使你能够快速过渡到受支持的 SDK。 此外，将通过各种通信渠道（Azure 门户、Azure 更新以及与分配的服务管理员的直接通信）通知客户。

**2. 在12个月期间，客户是否可以使用 "已停用" Azure Cosmos DB SDK 来创作应用程序？** 

是的，在12个月的通知期内，客户将拥有使用 "已停用" Azure Cosmos DB SDK 创作、部署和修改应用程序的完全访问权限。 在12个月的通知期内，建议客户根据需要迁移到支持的更新版本的 Azure Cosmos DB SDK。 

**3. 在停用日期之后，使用不受支持的 Azure Cosmos DB SDK 的应用程序会发生什么情况？** 

停用日期后，Azure Cosmos DB 将不再进行 bug 修复、添加新功能，并为已停用的 SDK 版本提供支持。 如果不想升级，则从 SDK 的已淘汰版本发送的请求将继续由 Azure Cosmos DB 服务提供服务。 

**4. 哪些 SDK 版本将包含最新的功能和更新？**

新功能和更新将仅添加到最新的受支持的主要 SDK 版本的最新次版本。 建议始终使用最新版本，以利用新功能、性能改进和 bug 修复。 如果你使用的是旧的、未停用的 SDK 版本，你的 Azure Cosmos DB 的请求仍将起作用，但你将无法访问任何新功能。  

**5. 如果我无法在截止日期之前更新应用程序，该怎么办？**

我们建议尽早升级到最新 SDK。 SDK 标记为要停用后，你将有12个月来更新应用程序。 如果在停用日期后无法更新，则会继续 Azure Cosmos DB 提供从 SDK 的已淘汰版本发送的请求，因此正在运行的应用程序将继续工作。 但 Azure Cosmos DB 将不再进行 bug 修复、添加新功能，并为已停用的 SDK 版本提供支持。 

如果你有支持计划并需要技术支持，[请通过归档支持票证联系我们。](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
    


