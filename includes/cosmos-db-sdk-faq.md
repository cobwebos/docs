---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90068723"
---
**如何收到即将停用的 SDK 的通知？**

Microsoft 会在即将停用的 SDK 的支持结束之前提前 12 个月进行通知，以便协助平稳地转换到支持的 SDK。 我们会通过以下通信通道通知你：Azure 门户、Azure 更新以及与分配的服务管理员的直接通信。

**在这 12 个月期间，我是否可以使用即将停用的 Azure Cosmos DB SDK 来创作应用程序？** 

可以，你可以在 12 个月的宽限期内使用即将停用的 Azure Cosmos DB SDK 创作、部署和修改应用程序。 建议在 12 个月的宽限期内根据相应情况迁移到支持的较新版本 Azure Cosmos DB SDK。 

**停用日期之后，使用不受支持的 Azure Cosmos DB SDK 的应用程序会发生什么情况？** 

停用日期之后，Azure Cosmos DB 将不再进行 bug 修复、添加新功能或为已停用的 SDK 版本提供支持。 如果不想升级，从已停用的 SDK 版本发送的请求将继续由 Azure Cosmos DB 服务提供服务。 

**哪些 SDK 版本将包含最新功能和更新？**

新功能和更新将仅添加到最新的受支持的主要 SDK 版本的最新次要版本。 建议始终使用最新版本，以充分利用新功能、性能改进和 bug 修补程序。 如果使用的是未停用的旧版本 SDK，则对 Azure Cosmos DB 进行的请求仍然有效，但是你无法访问任何新功能。  

**如果无法在截止日期之前更新应用程序，该怎么办？**

我们建议尽早升级到最新 SDK。 SDK 标记为要停用之后，你将有 12 个月的时间来更新应用程序。 如果无法在停用日期之前更新，从已停用的 SDK 版本发送的请求将继续由 Azure Cosmos DB 提供服务，因此正在运行的应用程序将继续运行。 但 Azure Cosmos DB 将不再进行 bug 修复、添加新功能或为已停用的 SDK 版本提供支持。 

如果你有支持计划并需要技术支持，请创建支持工单以[联系我们](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。
    


