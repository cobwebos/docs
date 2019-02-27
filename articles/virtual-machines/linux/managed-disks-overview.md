---
title: 适用于 Linux VM 的 Azure 磁盘存储托管磁盘概述 | Microsoft Docs
description: 概述了 Azure 托管磁盘，该磁盘在使用 Linux VM 时处理存储帐户
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/11/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: d8394121019338b53c87f7229d0ccf52fbdf8f21
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327021"
---
# <a name="introduction-to-azure-managed-disks"></a>Azure 托管磁盘简介

Azure 托管磁盘是虚拟硬盘 (VHD)。 可以将其视为本地服务器中的物理磁盘，但它是虚拟化的。 Azure 托管磁盘作为页 blob 存储，后者是 Azure 中的随机 IO 存储对象。 我们之所以将托管磁盘称为“托管”是因为，它是对页 blob、blob 容器和 Azure 存储帐户的抽象。 对于托管磁盘，你所要做的就是预配磁盘，而 Azure 负责其余的工作。

如果选择将 Azure 托管磁盘与工作负荷配合使用，Azure 将为你创建和管理该磁盘。 可用的磁盘类型包括超级磁盘（预览版）、高级固态硬盘 (SSD)、标准 SSD 和标准硬盘驱动器 (HDD)。 有关每种磁盘类型的详细信息，请参阅[选择适用于 IaaS VM 的磁盘类型](disks-types.md)。

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [选择适用于 IaaS VM 的磁盘类型](disks-types.md)
