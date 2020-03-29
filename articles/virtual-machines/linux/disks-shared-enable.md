---
title: 为 Azure 托管磁盘启用共享磁盘
description: 使用共享磁盘（预览）配置 Azure 托管磁盘，以便跨多个 VM 共享磁盘
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0e925abf151abc1a229b57bb035775430fa5332f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970380"
---
# <a name="enable-shared-disk"></a>启用共享磁盘

本文介绍如何为 Azure 托管磁盘启用共享磁盘（预览）功能。 Azure 共享磁盘（预览）是 Azure 托管磁盘的新功能，使您能够同时将托管磁盘附加到多个虚拟机 （VM）。 通过将托管磁盘附加到多个 VM，可以部署新的群集应用程序或将现有群集应用程序迁移到 Azure。 

如果要查找已启用共享磁盘的托管磁盘上的概念信息，请参阅[Azure 共享磁盘](disks-shared.md)。
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]