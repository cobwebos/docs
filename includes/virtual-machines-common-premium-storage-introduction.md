---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74005560"
---
本文提供了使用 Azure 高级存储构建高性能应用程序的准则。 可将本文档中提供的说明与适用于应用程序所用技术的性能最佳做法结合使用。 为了说明这些准则，我们在本文档中使用了在高级存储上运行的 SQL Server 作为示例。

由于本文重点介绍针对存储层的性能方案，因此需要优化应用程序层。 例如，若要在 Azure 高级存储上托管 SharePoint 场，可使用本文中的 SQL Server 示例来优化数据库服务器。 另请优化 SharePoint 场的 Web 服务器和应用程序服务器以获取最高性能。

本文帮助解答有关如何在 Azure 高级存储上优化应用程序性能的以下常见问题：

* 如何度量应用程序性能？  
* 为什么看不到预期的高性能？  
* 哪些因素会影响应用程序在高级存储上的性能？  
* 这些因素如何影响应用程序在高级存储上的性能？  
* 如何针对 IOPS、带宽和延迟进行优化？  

我们所提供的这些准则是专门针对高级存储的，因为在高级存储上运行的工作负荷具有高度的性能敏感性。 我们根据需要提供示例。 也可将其中部分准则应用于在使用标准存储磁盘的 IaaS VM 上运行的应用程序。