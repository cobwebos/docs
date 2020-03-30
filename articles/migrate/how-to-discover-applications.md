---
title: 使用 Azure 迁移在本地服务器上发现应用、角色和功能
description: 了解如何通过 Azure 迁移服务器评估在本地服务器上发现应用、角色和功能。
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: e8ce279afc845ebf37ad4ab8b2ce7236cb18137a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79453576"
---
# <a name="discover-machine-apps-roles-and-features"></a>发现计算机应用、角色和功能

本文介绍如何使用 Azure 迁移：服务器评估发现本地服务器上的应用程序、角色和功能。

发现应用清单以及本地计算机上运行的角色/功能可帮助您识别和规划针对工作负荷定制的 Azure 迁移路径。

> [!NOTE]
> 应用发现当前仅针对 VMware VM 处于预览状态，仅限于发现。 我们尚未提供基于应用程序的评估。 基于机器的评估，适用于本地 VMware VM、超虚拟机和物理服务器。

使用 Azure 迁移的应用发现：服务器评估是无代理的。 计算机和 VM 上未安装任何内容。 服务器评估使用 Azure 迁移设备与计算机来宾凭据一起执行发现。 设备使用 VMware API 远程访问 VMware 计算机。


## <a name="before-you-start"></a>开始之前

1. 请确保[已创建](how-to-add-tool-first-time.md)Azure 迁移项目。
2. 确保将 Azure 迁移：服务器评估工具[添加到](how-to-assess.md)项目中。
4. 使用 Azure 迁移设备检查[VMware 要求](migrate-support-matrix-vmware.md#vmware-requirements)，以发现和评估 VMware VM。
5. 检查部署 Azure 迁移设备[的要求](migrate-appliance.md)。
6. [验证应用程序发现的支持和要求](migrate-support-matrix-vmware.md#application-discovery)。

## <a name="prepare-for-app-discovery"></a>准备应用发现

1. [准备设备部署](tutorial-prepare-vmware.md)。 准备包括验证设备设置，以及设置设备将用于访问 vCenter Server 的帐户。
2. 请确保您有一个用户帐户（每个为 Windows 和 Linux 服务器一个），并且对要发现应用、角色和功能的计算机具有管理员权限。
3. [部署 Azure 迁移设备](how-to-set-up-appliance-vmware.md)以开始发现。 要部署设备，请将 OVA 模板下载并导入 VMware，以将设备创建为 VMware VM。 配置设备，然后将其注册到 Azure 迁移。
2. 部署设备时，要开始连续发现，请指定以下内容：
    - 要连接到的 vCenter 服务器的名称。
    - 为设备为连接到 vCenter 服务器而创建的凭据。
    - 为设备为连接到 Windows/Linux VM 而创建的帐户凭据。

部署设备并提供凭据后，设备将开始持续发现 VM 元数据和性能数据，以及应用、功能和角色的发现。  应用发现的时间长取决于您拥有多少个 VM。 应用发现 500 个 VM 通常需要一个小时。

## <a name="review-and-export-the-inventory"></a>审核并导出库存

发现结束后，如果提供了应用发现凭据，则可以在 Azure 门户中查看和导出应用清单。

1. 在**Azure 迁移 - 服务器** > **Azure 迁移：服务器评估中**，单击显示的计数以打开 **"发现"服务器**页面。

    > [!NOTE]
    > 在此阶段，您还可以选择为已发现的计算机设置依赖项映射，以便可以可视化要评估的计算机之间的依赖项。 [了解详情](how-to-create-group-machine-dependencies.md)。

2. 在**发现的应用程序**中，单击显示的计数。
3. 在 **"应用程序"清单**中，您可以查看发现的应用、角色和功能。
4. 要导出库存，请在 **"发现服务器"** 中单击"**导出应用清单**"。

应用清单以 Excel 格式导出和下载。 "**应用程序清单**"表显示在所有计算机上发现的所有应用。

## <a name="next-steps"></a>后续步骤

- [创建有关](how-to-create-assessment.md)已发现服务器的提升和移动迁移的评估。
- 使用[Azure 迁移：数据库评估](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)SQL Server 数据库。
