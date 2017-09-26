---
title: "在 OpenSUSE VM 上安装 MySQL | Microsoft Docs"
description: "了解如何在 Azure 中的 OpenSUSE Linux 虚拟机上安装 MySQL。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/19/2016
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 01b798a25575b66f89057315ce80d6cc0cde53b5
ms.contentlocale: zh-cn
ms.lasthandoff: 03/27/2017

---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>在 Azure 中运行 OpenSUSE Linux 的虚拟机上安装 MySQL
[MySQL][MySQL] 是一种常用的开源 SQL 数据库。 本教程介绍如何创建运行 OpenSUSE Linux 的虚拟机，然后安装 MySQL。

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

<br>

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>创建运行 OpenSUSE Linux 的虚拟机
[!INCLUDE [create-and-configure-opensuse-vm-in-portal](../../../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>在虚拟机上安装和运行 MySQL
[!INCLUDE [install-and-run-mysql-on-opensuse-vm](../../../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>后续步骤
有关 MySQL 的详细信息，请参阅 [MySQL 文档][MySQLDocs]。

[MySQLDocs]:http://dev.mysql.com/doc/index-topic.html
[MySQL]:http://www.mysql.com


