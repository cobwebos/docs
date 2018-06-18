---
title: 表存储简介 - Azure 中的对象存储 | Microsoft Docs
description: 使用 Azure 表存储（一种 NoSQL 数据存储）将结构化数据存储在云中。
services: storage
documentationcenter: .net
author: SnehaGunda
manager: kfile
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: storage
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: overview
ms.date: 04/23/2018
ms.author: sngun
ms.openlocfilehash: 044f52cd1fa42653269649b92c7a06a5f623a501
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660491"
---
# <a name="introduction-to-table-storage-in-azure"></a>Azure 中的表存储简介

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Azure 表存储是一项用于在云中存储结构化 NoSQL 数据的服务，通过无架构设计提供键/属性存储。 因为表存储无架构，因此可以很容易地随着应用程序需求的发展使数据适应存储。 对于许多类型的应用程序来说，访问表存储数据速度快且经济高效，在数据量相似的情况下，其成本通常比传统 SQL 要低。

可以使用表存储来存储灵活的数据集，例如 Web 应用程序的用户数据、通讯簿、设备信息，或者服务需要的其他类型的元数据。 可以在表中存储任意数量的实体，并且一个存储帐户可以包含任意数量的表，直至达到存储帐户的容量极限。

[!INCLUDE [storage-table-concepts-include](../../../includes/storage-table-concepts-include.md)]

## <a name="next-steps"></a>后续步骤

* [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。

* [.NET 中的 Azure 表存储入门](../../cosmos-db/table-storage-how-to-use-dotnet.md)

* 查看表服务参考文档，了解有关可用 API 的完整详情：

    * [.NET 存储客户端库参考](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

    * [REST API 参考](http://msdn.microsoft.com/library/azure/dd179355)
