---
title: 虚拟机概述
titleSuffix: Azure VMware Solution by CloudSimple
description: 了解 CloudSimple 虚拟机及其优点。 你可以从 Azure 门户管理 VMware 虚拟机。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88141970"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple 虚拟机概述

通过 CloudSimple，你可以从 Azure 门户管理)  (Vm 的 VMware 虚拟机。  你的 vSphere 群集中的群集或资源池由 Azure 通过映射到你的订阅进行管理。

若要从 Azure 创建 CloudSimple VM，你的私有云 vCenter 上必须存在一个 VM 模板。  模板用于自定义操作系统和应用程序。  可以强制模板 VM 满足企业安全策略要求。  可以使用模板创建 Vm，然后使用自助服务模型从 Azure 门户中使用。

## <a name="benefits"></a>好处

Azure 门户中的虚拟机 CloudSimple 为用户提供自助服务机制，以创建和管理 VMware 虚拟机。

* 在私有云 vCenter 上创建 CloudSimple VM
* 管理 VM 属性
  * 添加/删除磁盘
  * 添加/删除 Nic
* CloudSimple VM 的电源操作
  * 开机和关机
  * 重置 VM
* 删除 VM

## <a name="next-steps"></a>后续步骤

* 了解如何 [在 Azure 上使用 VMware vm](quickstart-create-vmware-virtual-machine.md)
* 了解如何 [映射 Azure 订阅](azure-subscription-mapping.md)
