---
title: "在 Azure 的 Windows VM 上安装 MongoDB | Microsoft Docs"
description: "了解如何在使用经典部署模型创建的运行 Windows Server 的 Azure VM 上安装 MongoDB。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
ms.openlocfilehash: d70194f30b7866cab38cfbbe1ea06664b0ddc1f5
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="install-mongodb-on-a-windows-vm-in-azure"></a>在 Azure 的 Windows VM 上安装 MongoDB
> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。  本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 若要使用 Resource Manager 部署模型安装和配置 MongoDB，请参阅[此文](../install-mongodb.md)。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[MongoDB][MongoDB] 是一个流行的开源、高性能 NoSQL 数据库。 本文将引导使用 [Azure 门户][AzurePortal]创建 Windows Server 虚拟机 (VM)。 然后创建数据磁盘并将其附加到 VM，再安装和配置 MongoDB。 如果想使用 Azure 中现有的 VM，可直接跳到[安装并配置 MongoDB](#install-and-run-mongodb-on-the-virtual-machine)。

## <a name="create-a-virtual-machine-running-windows-server"></a>创建运行 Windows Server 的虚拟机
按照以下说明创建虚拟机。

[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> 在创建虚拟机时可以为 MongoDB 添加一个终结点并将其配置为如下设置：将其命名为 **Mongo**，使用 **TCP** 作为协议，并将公共端口和专用端口都设为 **27017**。
>
>

## <a name="attach-a-data-disk"></a>附加数据磁盘
要为虚拟机提供存储，请附加数据磁盘，并对其进行初始化，以便 Windows 可以使用它。 如果已有数据磁盘，可附加该现有磁盘；此外还可附加空磁盘。

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-windows-linux.md)]

有关初始化磁盘的说明，请参阅[如何将数据磁盘附加到 Windows 虚拟机](attach-disk.md)中的“如何在 Windows Server 中初始化新数据磁盘”。

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>在该虚拟机上安装和运行 MongoDB
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>摘要
在本教程中，已了解如何创建运行 Windows Server 的虚拟机、如何远程连接到该虚拟机以及如何附加数据磁盘。  你还了解了如何在基于 Windows 的虚拟机上安装和配置 MongoDB。 现可按照 [MongoDB 文档][MongoDocs]中的高级主题操作，访问基于 Windows 的虚拟机上的 MongoDB。

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: https://portal.azure.com/

