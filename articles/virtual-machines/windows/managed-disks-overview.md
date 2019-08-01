---
title: 适用于 Windows VM 的 Azure 磁盘存储托管磁盘概述 | Microsoft Docs
description: 概要介绍 Azure 托管磁盘，该磁盘在使用 Azure Windows VM 时处理存储帐户
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f86937feb7061950084a8b5bdb532075c7f93869
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68693708"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure 托管磁盘简介

Azure 托管磁盘是虚拟硬盘 (VHD)。 可以将其视为本地服务器中的物理磁盘，但它是虚拟化的。 Azure 托管磁盘作为页 blob 存储，后者是 Azure 中的随机 IO 存储对象。 我们之所以将托管磁盘称为“托管”是因为，它是对页 blob、blob 容器和 Azure 存储帐户的抽象。 对于托管磁盘，你所要做的就是预配磁盘，而 Azure 负责其余的工作。

如果选择将 Azure 托管磁盘与工作负荷配合使用，Azure 将为你创建和管理该磁盘。 可用的磁盘类型包括超级固态硬盘 (SSD)（预览版）、高级 SSD、标准 SSD 和标准硬盘驱动器 (HDD)。 有关每种磁盘类型的详细信息，请参阅[选择适用于 IaaS VM 的磁盘类型](disks-types.md)。

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [选择适用于 IaaS VM 的磁盘类型](disks-types.md)