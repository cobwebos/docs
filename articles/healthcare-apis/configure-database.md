---
title: 在 Azure API for FHIR 中配置数据库设置
description: 本文介绍如何在 Azure API for FHIR 中配置数据库设置。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: matjazl
ms.openlocfilehash: 652445a96acfa0358211d1d97e0fcf288989d6ba
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795773"
---
# <a name="configure-database-settings"></a>配置数据库设置 

Azure API for FHIR 使用数据库存储其数据。 底层数据库的性能取决于预配服务期间选择的请求单位 (RU) 数，以及预配服务后数据库设置中的 RU 数。

在设置底层数据库的性能时，Azure API for FHIR 将借用 Cosmos DB 中 RU 的概念（请参阅 [Azure Cosmos DB 中的请求单位](https://docs.microsoft.com/azure/cosmos-db/request-units)）。 

必须预配吞吐量才能确保始终为数据库提供足够的系统资源。 应用程序所需的 RU 数取决于执行的操作。 操作既包括简单的读写，也包括更复杂的查询。 

> [!NOTE]
> 由于不同的操作消耗不同的 RU 数，因此我们将在响应标头中返回每个 API 调用实际消耗的 RU 数。 这样，你便可以分析应用程序消耗的 RU 数。

## <a name="update-throughput"></a>更新吞吐量

若要在 Azure 门户中更改此设置，请导航到 Azure API for FHIR 并打开“数据库”边栏选项卡。 接下来，根据性能需求将“预配吞吐量”更改为所需值。 最大可以将该值更改为 10,000 RU/秒。 如果需要更大的值，请联系 Azure 支持部门。

如果数据库吞吐量大于 10000 RU/s，或者数据库中存储的数据大于 50 GB，则客户端应用程序必须能够处理继续标记。 每增加 10000 RU/秒的吞吐量或存储的数据量大于 50 GB 时，将在数据库中创建新的分区。 多个分区创建一个多页面响应，其中使用继续标记实现分页。

> [!NOTE] 
> 较高的值意味着更高的 Azure API 用于 FHIR 的吞吐量和更高的服务成本。

![配置 Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>后续步骤

本文介绍了如何更新适用于 FHIR 的 Azure API 的 RUs。 接下来，为 FHIR 部署完全托管的 Azure API：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
