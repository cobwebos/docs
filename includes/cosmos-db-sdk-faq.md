---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068723"
---
**如何通知即将停用的 SDK？**

Microsoft 将提供12个月的提前通知，并在支持终止 SDK 之前提供12个月的提前通知，以方便顺利过渡到受支持的 SDK。 我们会通过各种通信渠道通知你： Azure 门户、Azure 更新以及与分配的服务管理员的直接通信。

**在12个月期间，我是否可以使用已停用的 Azure Cosmos DB SDK 创作应用程序？** 

是的，你可以在12个月的通知期内使用 Azure Cosmos DB 来创作、部署和修改应用程序。 建议你根据需要在12个月的通知期内迁移到 Azure Cosmos DB SDK 的更新版本。 

**停用日期之后，使用不受支持的 Azure Cosmos DB SDK 的应用程序会发生什么情况呢？** 

停用日期后，Azure Cosmos DB 将不再进行 bug 修复、添加新功能或为已停用的 SDK 版本提供支持。 如果不想升级，则从 SDK 的已淘汰版本发送的请求将继续由 Azure Cosmos DB 服务提供服务。 

**哪些 SDK 版本将包含最新功能和更新？**

新功能和更新将仅添加到最新的受支持的主要 SDK 版本的最新次版本。 建议始终使用最新版本，以利用新功能、性能改进和 bug 修复。 如果你使用的是旧的、非停用的 SDK 版本，你对 Azure Cosmos DB 的请求仍将起作用，但你将无法访问任何新功能。  

**如果无法在截止日期之前更新应用程序，该怎么办？**

我们建议尽早升级到最新 SDK。 将 SDK 标记为停用后，你将有12个月来更新应用程序。 如果在停用日期后无法更新，则会继续 Azure Cosmos DB 提供从该 SDK 的已停用版本发送的请求，因此正在运行的应用程序将继续工作。 但 Azure Cosmos DB 将不再进行 bug 修复、添加新功能或为已停用的 SDK 版本提供支持。 

如果你有支持计划并需要技术支持，请通过归档支持[票证与我们联系。](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
    


