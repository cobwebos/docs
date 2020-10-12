---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648276"
---
Azure Functions 支持跨域资源共享 (CORS)。 CORS 在[门户中](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors)以及通过 [Azure CLI](/cli/azure/functionapp/cors) 进行配置。 CORS 允许的来源列表应用于函数应用级别。 启用 CORS 后，响应包含 `Access-Control-Allow-Origin` 标头。 有关详细信息，请参阅 [跨域资源共享](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors)。 