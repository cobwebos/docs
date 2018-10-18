---
title: Azure SAP HANA（大型实例）的数据分层和扩展节点 | Microsoft Docs
description: Azure SAP HANA（大型实例）的数据分层和扩展节点。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87c2cb3e373b76685fca09eb0cfeefdc9216df77
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "44030328"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>使用 SAP HANA 数据分层和扩展节点

SAP 支持面向不同 SAP NetWeaver 版本和 SAP BW/4HANA 的 SAP BW 的数据分层模型。 有关数据分层模型的详细信息，请参阅 SAP 文档 [具有 SAP HANA 扩展节点的 SAP BW/4HANA and SAP BW on HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)。
利用 HANA 大型实例，可以使用 SAP HANA 扩展节点的选项 1 配置（本常见问题解答和 SAP 博客文档中提供详细介绍）。 可使用以下 HANA 大型实例 SKU 配置选项 2 配置：S72m、S192、S192m、S384 和 S384m。 

阅读文档时，可能不会立刻发现此方法的优点。 但如果深入研究 SAP 大小调整准则，便可能发现使用选项 1 和选项 2 SAP HANA 扩展节点的优点。 下面是一些示例：

- SAP HANA 大小调整准则所需的数据量通常是内存所需的两倍。 因此，运行具有热数据的 SAP HANA 实例时，内存中只会填充 50% 或更少数据。 理想状况下，内存中的剩余空间将保留，供 SAP HANA 执行其工作。
- 这意味着，如果在拥有 2 TB 内存的 HANA 大型实例 S192 单元中运行 SAP BW 数据库，只能拥有 1 TB 的数据量。
- 如果仍然运行 S192 HANA 大型实例 SKU，但额外使用选项 1 的其他 SAP HANA 扩展节点，则可获得额外的 2 TB 数据量。 在选项 2 配置中，甚至可获得额外的 4 TB 暖数据量。 与热节点相比，“暖”扩展节点的完整内存容量可供选项 1 用于数据存储。 选项 2 SAP HANA 扩展节点配置中的数据量可使用双倍内存。
- 选项 1 最终将获得 3 TB 数据容量，其中热、暖数据比率为 1:2。 而选项 2 扩展节点配置中将获得 5 TB 数据容量，其中热、暖数据比率为 1:4。

数据量超出内存量的差值越大，请求的暖数据存储在磁盘存储中的可能性就越大。

**后续步骤**
- 请参阅 [Azure 上的 SAP HANA（大型实例）体系结构](hana-architecture.md)