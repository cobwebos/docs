---
title: 虚拟机概述
titleSuffix: Azure VMware Solution by CloudSimple
description: 了解云简单虚拟机及其优势。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 436a5ab6fb286ce206f981487b6a29d774ecd1a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024902"
---
# <a name="cloudsimple-virtual-machines-overview"></a>云简单虚拟机概述

CloudSimple 允许您从 Azure 门户管理 VMware 虚拟机 （VM）。  vSphere 群集的群集或资源池通过 Azure 通过将其映射到订阅进行管理。

要从 Azure 创建云简单 VM，私有云 vCenter 上必须存在 VM 模板。  该模板用于自定义操作系统和应用程序。  可以强化模板 VM 以满足企业安全策略。  可以使用模板创建 VM，然后使用自助服务模型从 Azure 门户使用它们。

## <a name="benefits"></a>优点

Azure 门户的 CloudSimple 虚拟机为用户提供了创建和管理 VMware 虚拟机的自助服务机制。

* 在私有云 vCenter 上创建云简单 VM
* 管理 VM 属性
  * 添加/删除磁盘
  * 添加/删除 NIC
* 云简单 VM 的电源操作
  * 开机并关机
  * 重置 VM
* 删除 VM

## <a name="next-steps"></a>后续步骤

* 了解如何在[Azure 上使用 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 了解如何映射[Azure 订阅](azure-subscription-mapping.md)
