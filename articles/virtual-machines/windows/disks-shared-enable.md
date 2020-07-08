---
title: 为 Azure 托管磁盘启用共享磁盘
description: 使用共享磁盘（预览版）配置 Azure 托管磁盘，以便可以跨多个 Vm 共享它
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3b949905f311b1a8878aa691e32abc3f568e1e6b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84659289"
---
# <a name="enable-shared-disk"></a>启用共享磁盘

本文介绍如何为 Azure 托管磁盘启用共享磁盘（预览版）功能。 Azure 共享磁盘（预览版）是 Azure 托管磁盘的一项新功能，可让你同时将托管磁盘附加到多个虚拟机（Vm）。 通过将托管磁盘附加到多个 VM，可以向 Azure 部署新的群集应用程序或迁移现有的群集应用程序。 

如果正在查找有关已启用共享磁盘的托管磁盘的概念信息，请参阅[Azure 共享磁盘](disks-shared.md)。
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]