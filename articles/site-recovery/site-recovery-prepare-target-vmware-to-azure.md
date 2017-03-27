---
title: "准备目标（VMware 到 Azure）| Microsoft Docs"
description: "本文介绍如何准备 Azure 环境，以便开始将 VMware 虚拟机复制到 Azure。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/11/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: b7f26df96ddedb579cf5d9d20fee6b2599e762e0
ms.openlocfilehash: 555ea8eed17e42019513029f7b3ac2167f002578

---

# <a name="prepare-target-vmware-to-azure"></a>准备目标（VMware 到 Azure）
> [!div class="op_single_selector"]
> * [VMware 虚拟机](./site-recovery-prepare-target-vmware-to-azure.md)
> * [物理服务器](./site-recovery-prepare-target-physical-to-azure.md)

本文介绍如何准备 Azure 环境，以便开始将 VMware 虚拟机复制到 Azure。

## <a name="prerequisites"></a>先决条件

本文的假定条件如下：
- 已创建恢复服务保管库，用于保护 VMware 虚拟机。 已从 [Azure 门户](http://portal.azure.com "Azure 门户")创建恢复服务保管库。
- 已[设置本地环境](./site-recovery-set-up-vmware-to-azure.md)，用于将 VMware 虚拟机复制到 Azure。

## <a name="prepare-target"></a>准备目标

完成“**步骤 1: 选择保护目标**”和“**步骤 2: 准备源**”后，将转到“**步骤 3: 目标**”

![准备目标](./media/site-recovery-prepare-target-vmware-to-azure/prepare-target-vmware-to-azure.png)

1. **订阅：**从下拉菜单中，选择要将虚拟机复制到的“订阅”。
2. **部署模型：**选择部署模型（经典或 Resource Manager）

根据选择的部署模型，运行验证以确保目标订阅中至少具有一个兼容的存储帐户和虚拟网络，以将虚拟机复制并故障转移到此目标订阅中。

成功完成验证后，单击“确定”转到下一步。

如果不具备（或想添加更多）兼容的 Resource Manager 存储帐户或虚拟网络，可单击边栏选项卡顶部的“+存储帐户”或“+网络”按钮实现。

## <a name="next-steps"></a>后续步骤
[配置复制设置](./site-recovery-setup-replication-settings-vmware.md)。



<!--HONumber=Feb17_HO2-->


