---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6ad5bafe66e6e39503d3987aa6ddd9159c532fa0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72237289"
---
我们会根据存储帐户的使用情况，对 Azure 存储进行计费。 存储帐户中的所有对象会作为组共同计费。 

存储成本根据以下几个因素计算：区域/位置、帐户类型、访问层、存储容量、复制方案、存储事务和数据流出量。

* **区域**指的是帐户所在的地理区域。
* **帐户类型**指的是正在使用的存储帐户类型。 
* **访问层**指的是为常规用途 v2 或 Blob 存储帐户指定的数据使用模式。
* 存储**容量**指的是存储帐户中用来存储数据的配额。
* **复制**可以确定一次保留的数据副本的数量以及保留位置。
* **事务**指的是对 Azure 存储进行的所有读取和写入操作。
* **数据流出量**指的是传出某个 Azure 区域的数据。 当不在同一区域中的应用程序访问存储帐户中的数据时，需要为数据流出量付费。 有关使用资源组对同一区域内的数据和服务进行分组以限制数据流出费用的信息，请参阅[什么是 Azure 资源组？](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。 

[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/) 页提供基于帐户类型、存储容量、复制和交易的详细定价信息。 [数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/) 提供了针对数据流出量的详细定价信息。 可以使用 [Azure 存储定价计算器](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) 来帮助估算成本。

