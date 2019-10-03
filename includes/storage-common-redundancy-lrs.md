---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219022"
---
本地冗余存储（LRS）将数据在单个数据中心内复制三次。 LRS 在给定的一年中提供至少 99.999999999% （11个9）的对象持久性。 与其他选项相比，LRS 是成本最低的复制选项，提供的持久性也最低。

如果发生数据中心级灾难（例如火灾或洪水），则使用 LRS 的存储帐户中的所有副本可能丢失或无法恢复。 为了缓解此风险，Microsoft 建议使用区域冗余存储（ZRS）、异地冗余存储（GRS）或异地冗余存储（GZRS）。

仅在将数据写入所有三个副本后，才成功返回对 LRS 存储帐户的写入请求。

在以下情况下，你可能希望使用 LRS：

* 如果应用程序存储着在发生数据丢失时可轻松重构的数据，则可以选择 LRS。
* 由于数据治理要求，某些应用程序限制为仅在国家/地区内复制数据。 在某些情况下，为 GRS 帐户复制数据的配对区域可能位于其他国家/地区。 有关配对区域的详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。
