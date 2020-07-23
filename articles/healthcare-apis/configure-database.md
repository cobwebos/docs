---
title: 在 Azure API for FHIR 中配置数据库设置
description: 本文介绍如何在 Azure API for FHIR 中配置数据库设置。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: adc6fdf144927d10f811a00aa33f244cfdc25042
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870957"
---
# <a name="configure-database-settings"></a>配置数据库设置 

Azure API for FHIR 使用数据库存储其数据。 底层数据库的性能取决于预配服务期间选择的请求单位 (RU) 数，以及预配服务后数据库设置中的 RU 数。

在设置底层数据库的性能时，Azure API for FHIR 将借用 Cosmos DB 中 RU 的概念（请参阅 [Azure Cosmos DB 中的请求单位](https://docs.microsoft.com/azure/cosmos-db/request-units)）。 

必须预配吞吐量才能确保始终为数据库提供足够的系统资源。 应用程序所需的 RU 数取决于执行的操作。 操作既包括简单的读写，也包括更复杂的查询。 

> [!NOTE]
> 由于不同的操作消耗不同的 RU 数，因此我们将在响应标头中返回每个 API 调用实际消耗的 RU 数。 这样，你便可以分析应用程序消耗的 RU 数。

## <a name="update-throughput"></a>更新吞吐量
若要在 Azure 门户中更改此设置，请导航到 Azure API for FHIR 并打开“数据库”边栏选项卡。 接下来，根据性能需求将“预配吞吐量”更改为所需值。 最大可以将该值更改为 10,000 RU/秒。 如果需要更大的值，请联系 Azure 支持部门。

> [!NOTE] 
> 该值越大，则 Azure API for FHIR 吞吐量越高，但服务费用也越高。

![配置 Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何更新 Azure API for FHIR 的 RU。 接下来请部署完全托管式的 Azure API for FHIR：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)