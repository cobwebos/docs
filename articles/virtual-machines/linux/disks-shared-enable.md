---
title: 为 Azure 托管磁盘启用共享磁盘
description: 使用共享磁盘配置 Azure 托管磁盘，以便可以跨多个 Vm 共享它
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/30/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: cc365f6eef4da61ab7de5113b8f4b2b185360740
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460178"
---
# <a name="enable-shared-disk"></a>启用共享磁盘

本文介绍如何为 Azure 托管磁盘启用共享磁盘功能。 Azure 共享磁盘是 Azure 托管磁盘的一项新功能，可让你同时将托管磁盘附加到多个虚拟机（Vm）。 通过将托管磁盘附加到多个 VM，可以向 Azure 部署新的群集应用程序或迁移现有的群集应用程序。 

如果正在查找有关已启用共享磁盘的托管磁盘的概念信息，请参阅[Azure 共享磁盘](disks-shared.md)。
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]