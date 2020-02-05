---
title: Azure VMware 解决方案（AVS）-虚拟机概述
description: 了解 AVS 虚拟机及其优点。
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0e6cc6d51e37cea98cc553a236295311d21f595a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024902"
---
# <a name="avs-virtual-machines-overview"></a>AVS 虚拟机概述

AVS 允许从 Azure 门户管理 VMware 虚拟机（Vm）。 你的 vSphere 群集中的群集或资源池由 Azure 通过映射到你的订阅进行管理。

若要从 Azure 创建 AVS VM，你的 AVS 私有云 vCenter 上必须存在一个 VM 模板。 模板用于自定义操作系统和应用程序。 可以强制模板 VM 满足企业安全策略要求。 可以使用模板创建 Vm，然后使用自助服务模型从 Azure 门户中使用。

## <a name="benefits"></a>优势

Azure 门户的 AVS 虚拟机提供了自助服务机制，供用户创建和管理 VMware 虚拟机。

* 在 AVS 私有云 vCenter 上创建 AVS VM
* 管理 VM 属性
  * 添加/删除磁盘
  * 添加/删除 Nic
* AVS VM 的电源操作
  * 开机和关机
  * 重置 VM
* 删除 VM

## <a name="next-steps"></a>后续步骤

* 了解如何[在 Azure 上使用 VMware vm](quickstart-create-vmware-virtual-machine.md)
* 了解如何[映射 Azure 订阅](azure-subscription-mapping.md)
