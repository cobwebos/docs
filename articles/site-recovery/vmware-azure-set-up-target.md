---
title: 准备目标环境以便将 VMware 复制到 Azure | Microsoft Docs
description: 本文介绍如何准备目标 Azure 环境，以便将 VMware VM 复制到 Azure。
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: 5f14bbed7ae59737f62fb736591775cb7ba495c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2018
ms.locfileid: "29811064"
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>准备目标环境以便将 VMware 复制到 Azure

本文介绍如何准备目标 Azure 环境，以便开始将 VMware 虚拟机复制到 Azure。

## <a name="prerequisites"></a>先决条件

本文假设：
- 已创建恢复服务保管库，用于保护 VMware 虚拟机。 已从 [Azure 门户](http://portal.azure.com "Azure 门户")创建恢复服务保管库。
- 已[设置本地环境](vmware-azure-set-up-source.md)，用于将 VMware 虚拟机复制到 Azure。

## <a name="prepare-target"></a>准备目标

完成“**步骤 1: 选择保护目标**”和“**步骤 2: 准备源**”后，会转到“**步骤 3: 目标**”

![准备目标](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. 订阅：从下拉菜单中，选择要将虚拟机复制到的“订阅”。
2. **部署模型：** 选择部署模型（经典或 Resource Manager）

根据选择的部署模型，运行验证以确保目标订阅中至少具有一个兼容的存储帐户和虚拟网络，以将虚拟机复制并故障转移到此目标订阅中。

成功完成验证后，单击“确定”转到下一步。

如果你没有兼容的资源管理器存储帐户或虚拟网络，可单击页面顶部的“+ 存储帐户”或“+ 网络”按钮来创建一个。

## <a name="next-steps"></a>后续步骤
[配置复制设置](vmware-azure-set-up-replication.md)。
