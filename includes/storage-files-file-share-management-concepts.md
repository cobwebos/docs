---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597770"
---
Azure 文件共享部署到*存储帐户*中，这些帐户是表示共享存储池的顶级对象。 此存储池可用于部署多个文件共享以及其他存储资源，如 Blob 容器、队列或表。 部署到存储帐户中的所有存储资源都共享适用于该存储帐户的限制。 要查看存储帐户的当前限制，请参阅 Azure[文件可伸缩性和性能目标](../articles/storage/files/storage-files-scale-targets.md)。

有两种主要类型的存储帐户将用于 Azure 文件部署： 
- **通用版本 2 （GPv2） 存储帐户**：GPv2 存储帐户允许您在基于标准/基于硬盘（基于 HDD）的硬件上部署 Azure 文件共享。 除了存储 Azure 文件共享外，GPv2 存储帐户还可以存储其他存储资源，如 Blob 容器、队列或表。 
- **文件存储存储帐户**：文件存储存储帐户允许您在基于高级/固态磁盘（基于 SSD）的硬件上部署 Azure 文件共享。 文件存储帐户只能用于存储 Azure 文件共享;因此，文件存储帐户只能用于存储 Azure 文件共享。无法在 FileStorage 帐户中部署其他存储资源（blob 容器、队列、表等）。

在 Azure 门户、PowerShell 或 CLI 中可能会遇到几种其他存储帐户类型。 两种存储帐户类型（块 Blob 存储帐户和 Blob 存储存储帐户）不能包含 Azure 文件共享。 您可能会看到的其他两种存储帐户类型是通用版本 1 （GPv1） 和经典存储帐户，这两种帐户都可以包含 Azure 文件共享。 尽管 GPv1 和经典存储帐户可能包含 Azure 文件共享，但 Azure 文件的大多数新功能仅在 GPv2 和文件存储存储帐户中可用。 因此，我们建议仅将 GPv2 和 FileStorage 存储帐户用于新部署，并在环境中已存在 GPv1 和经典存储帐户时升级这些帐户。  