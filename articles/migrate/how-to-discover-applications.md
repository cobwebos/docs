---
title: 使用 Azure Migrate Server 评估发现本地服务器上安装的应用、角色和功能
description: 介绍如何使用 Azure Migrate Server 评估发现本地服务器上的应用、角色和功能。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 11/18/2019
ms.author: snehaa
ms.openlocfilehash: 14ecc6454c3d0f167c671532dd8e659e767cda66
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158633"
---
# <a name="discover-machine-apps-roles-and-features"></a>发现计算机应用、角色和功能

本文介绍如何使用 Azure Migrate：服务器评估来发现本地服务器上的应用程序、角色和功能。

发现应用的清单，以及在本地计算机上运行的角色/功能，可帮助您识别和计划针对您的工作负荷定制的 Azure 迁移路径。

使用 Azure Migrate 的应用程序发现：服务器评估是无代理的。 不需要在计算机和 Vm 上安装任何内容。 服务器评估使用 Azure Migrate 设备，同时使用计算机来宾凭据执行发现。 设备使用 WMI 和 SSH 调用远程访问计算机。

> [!NOTE]
> 目前仅 VMware Vm 支持应用程序发现，仅限发现。 我们尚未提供基于应用的评估。  现在，服务器评估当前评估计算机级别的本地 VMware Vm、Hyper-v Vm 和物理服务器，以便进行直接迁移。


## <a name="before-you-start"></a>开始之前

1. 查看应用程序级别发现的[支持限制](migrate-support-matrix-vmware.md#application-discovery)。
2. 请确保已[创建](how-to-add-tool-first-time.md)Azure Migrate 项目。
3. 如果已创建项目，请确保已[添加](how-to-assess.md)Azure Migrate： Server 评估工具。
4. 查看[vmware 要求](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements)，了解如何通过 Azure Migrate 设备发现和评估 vmware vm。
4. 检查部署 Azure Migrate 设备的[要求](migrate-support-matrix-vmware.md#assessment-appliance-requirements)。

## <a name="prepare-for-app-discovery"></a>准备应用发现

1. [准备进行设备部署](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware)。 准备工作包括验证设备设置，以及设置设备将用于访问 vCenter Server 的帐户。
2. 请确保具有管理员权限的用户帐户（每个用于 Windows 和 Linux 服务器）以及要在其上发现应用、角色和功能的计算机的管理员权限。
3. [部署 VMware 设备](how-to-set-up-appliance-vmware.md)以启动发现。 若要部署设备，请下载 .OVA 模板并将其导入 VMware，以将设备创建为 VMware VM。 配置设备，然后将其注册到 Azure Migrate。
2. 部署设备时，若要启动连续发现，请指定以下各项：
    - 要连接到的 vCenter Server 的名称。
    - 为设备创建的用于连接 vCenter Server 的凭据。
    - 为设备创建的用于连接到 Windows/Linux Vm 的帐户凭据。

部署设备并提供凭据后，设备会开始连续发现 VM 元数据和性能数据，以及应用、功能和角色的发现。  应用发现的持续时间取决于你拥有的 Vm 数量。 应用发现 500 Vm 通常要花费一小时。

## <a name="review-and-export-the-inventory"></a>查看并导出清单

发现完成后，如果为应用发现提供凭据，则可以查看和导出 Azure 门户中的应用清单。

1. 在**Azure Migrate-服务器** > **Azure Migrate：服务器评估**中，单击显示的计数打开 "**发现的服务器**" 页。

    > [!NOTE]
    > 在此阶段，你还可以选择为发现的计算机设置依赖关系映射，这样你就可以在想要评估的计算机上可视化依赖项。 [了解详细信息](how-to-create-group-machine-dependencies.md)。

2. 在**发现的应用程序**中，单击显示的计数。
3. 在**应用程序清单**中，可以查看已发现的应用、角色和功能。
4. 若要导出清单，请在 "**发现的服务器**" 中，单击 "**导出应用程序清单**"。

应用清单以 Excel 格式导出和下载。 **应用程序清单**表显示所有计算机上发现的所有应用。

## <a name="next-steps"></a>后续步骤

- 为发现的服务器的直接迁移和迁移迁移[创建一个评估](how-to-create-assessment.md)。
- 使用[Azure Migrate：数据库评估](https://docs.microsoft.com/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)评估 SQL Server 数据库。
