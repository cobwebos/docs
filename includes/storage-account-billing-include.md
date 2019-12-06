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
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851616"
---
根据存储帐户使用情况对 Azure 存储进行计费。 存储帐户中的所有对象会作为组共同计费。 

存储成本根据以下因素计算： 

* **区域**指的是帐户所在的地理区域。
* **帐户类型**是指您正在使用的存储帐户的类型。 
* **访问层**指的是为常规用途 V2 或 Blob 存储帐户指定的数据使用模式。
* 存储**容量**是指用来存储数据的存储帐户配额量。
* **复制**可以确定一次保留的数据副本的数量以及保留位置。
* **事务**指的是对 Azure 存储进行的所有读取和写入操作。
* **数据流出量**指的是传出某个 Azure 区域的数据。 如果存储帐户中的数据被不在同一区域中运行的应用程序访问，则需要支付数据传出费用。 有关使用资源组对同一区域内的数据和服务进行分组以限制数据流出费用的信息，请参阅[什么是 Azure 资源组？](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)。 

[Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/) 页提供基于帐户类型、存储容量、复制和交易的详细定价信息。 [数据传输定价详细信息](https://azure.microsoft.com/pricing/details/data-transfers/) 提供了针对数据流出量的详细定价信息。 可以使用 [Azure 存储定价计算器](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) 来帮助估算成本。

