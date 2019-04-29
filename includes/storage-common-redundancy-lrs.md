---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/12/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 32032f729283cb3f2a786412b563fdee88ba4c8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729601"
---
本地冗余存储 (LRS) 可在一年中提供至少 99.999999999%（11 个 9）的对象持久性。 LRS 通过将数据复制到一个存储缩放单元来提供此对象持久性。 你在其中创建存储帐户的区域中的一个数据中心承载着该缩放单元。 只有在数据已写入到所有副本后，到 LRS 存储帐户的写入请求才会成功返回。 每个副本驻留在存储缩放单元内的不同容错域和升级域中。

存储缩放单元是指存储节点的机架的集合。 容错域 (FD) 是一组表示物理故障单元的节点。 可以将容错域视为属于同一物理机架的节点。 升级域 (UD) 是一组在服务升级（推出）过程中一起升级的节点。 副本分布在同一存储缩放单元内的各个 UD 和 FD 中。 此体系结构可以确保，当硬件故障影响单个机架时或者当节点在服务升级期间进行升级时，你的数据可用。

与其他选项相比，LRS 是成本最低的复制选项，提供的持久性也最低。 如果发生数据中心级灾难（例如火灾或洪灾），则所有副本都可能会丢失或无法恢复。 若要缓解此风险，Microsoft 建议使用区域冗余存储 (ZRS) 或异地冗余存储 (GRS)。

* 如果应用程序存储着在发生数据丢失时可轻松重构的数据，则可以选择 LRS。
* 由于数据治理需要，某些应用程序被限制为只能在一个国家/地区内复制数据。 在某些情况下，在其中为 GRS 帐户复制数据的配对区域可能在另一个国家/地区。 有关配对区域的详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。
