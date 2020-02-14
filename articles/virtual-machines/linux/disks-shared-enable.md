---
title: 为 Azure 托管磁盘启用共享磁盘
description: 使用共享磁盘（预览版）配置 Azure 托管磁盘，以便可以跨多个 Vm 共享它
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60070dc4c49f83866e2d789a6bc1f9fd6b253bae
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202276"
---
# <a name="enable-shared-disk"></a>启用共享磁盘

本文介绍如何为 Azure 托管磁盘启用共享磁盘（预览版）功能。 Azure 共享磁盘（预览版）是 Azure 托管磁盘的一项新功能，可让你同时将托管磁盘附加到多个虚拟机（Vm）。 通过将托管磁盘附加到多个 Vm，可将现有的群集应用程序部署到 Azure 或将其迁移到 Azure。 

如果正在查找有关已启用共享磁盘的托管磁盘的概念信息，请参阅[Azure 共享磁盘](disks-shared.md)。
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]