---
title: 准备目标环境以便将 VMware 复制到 Azure | Microsoft Docs
description: 本文介绍如何准备目标 Azure 环境，以便将 VMware VM 复制到 Azure。
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: e75d4b1701944e206fcf6ded2dcb6d1e1fbc77cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723768"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>准备目标环境，以便将 VMware VM 或物理服务器灾难恢复到 Azure

本文介绍如何准备目标 Azure 环境，以便开始将 VMware 虚拟机或物理服务器复制到 Azure。

## <a name="prerequisites"></a>必备组件

本文假设：
- 已在 [Azure 门户](https://portal.azure.com "Azure 门户")上创建恢复服务保管库，用于保护源计算机
- 已设置本地环境，用于将源 [VMware 虚拟机](vmware-azure-set-up-source.md)或[物理服务器](physical-azure-set-up-source.md)复制到 Azure。

## <a name="prepare-target"></a>准备目标

完成“步骤 1：  选择保护目标”和“步骤 2：  准备源”后，将转到“步骤 3：目标” 

![准备目标](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **订阅：** 从下拉菜单中，选择要将虚拟机或物理服务器复制到的“订阅”。
2. **部署模型：** 选择部署模型（“经典”或“资源管理器”）

根据选择的部署模型，运行验证，以确保虚拟机或物理服务器到，对复制和故障转移的目标订阅中有至少一个虚拟网络。

成功完成验证后，单击“确定”转到下一步。

如果没有虚拟网络，则可以创建一个通过单击 **+ 网络**在页面顶部的按钮。

## <a name="next-steps"></a>后续步骤
[配置复制设置](vmware-azure-set-up-replication.md)。
