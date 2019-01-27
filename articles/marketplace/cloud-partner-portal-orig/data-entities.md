---
title: 数据实体 | Microsoft Docs
description: 数据实体的概述。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 579856ab87aaf8d051f2e3c161bb2d0e2f693ed5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446572"
---
# <a name="data-entities"></a>数据实体

本文阐明了数据实体的定义并概述了数据实体。 本文包括了关于以下各项的信息：数据实体的功能、它们支持的方案、用于它们的类别，以及用于创建它们的方法。

## <a name="overview"></a>概述

数据实体是数据库表的物理实现的一种抽象。 例如，在规范化表中，每个客户的大部分数据可能存储在客户表中，而剩余部分可能分散在一组相关表中。 在这种情况下，客户概念的数据实体显示为一个非规范化视图，其中每个行包含来自客户表和其相关表的所有数据。 数据实体将业务概念封装为一种使开发和集成更为容易的格式。 数据实体的抽象本质可以简化应用程序开发和自定义。 以后，抽象还会将应用程序代码与各个版本之间物理表不可避免的变动进行隔离。

总结：数据实体提供基础表架构的概念抽象和封装（非规范化视图）来表示主要数据概念和功能。

## <a name="capabilities"></a>功能

数据实体具有以下功能：

- 它将 AXD、数据导入/导出框架 (DIXF) 实体和聚合查询等分散化的概念替换为单一的概念。
- 它提供单个堆栈来捕获业务逻辑，并实现诸如导入/导出、集成和可编程性之类的方案。
- 它是用于为应用程序生命周期管理 (ALM) 和演示数据方案导出和导入数据包的主要机制。
- 它可以公开为 OData 服务，然后用于表格式同步集成方案和 Microsoft Office 集成中。

有关详细信息，请参阅[数据实体](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities)。
