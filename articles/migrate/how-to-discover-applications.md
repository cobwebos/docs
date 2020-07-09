---
title: 在本地服务器上发现应用、角色和功能，并提供 Azure Migrate
description: 了解如何通过 Azure Migrate Server 评估发现本地服务器上的应用、角色和功能。
ms.topic: article
ms.date: 06/10/2020
ms.openlocfilehash: 535c8ae8c2d6e5d9d175e663a58d47dc76aa0529
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86118641"
---
# <a name="discover-machine-apps-roles-and-features"></a>发现计算机应用、角色和功能

本文介绍如何使用 Azure Migrate：服务器评估来发现本地服务器上的应用程序、角色和功能。

发现本地计算机上运行的应用、角色和功能的清单有助于识别和定制适用于你的工作负荷的 Azure 迁移路径。 应用发现使用 Azure Migrate 设备，使用 VM 来宾凭据执行发现。 应用程序发现无代理。 未在 Vm 上安装任何内容。

> [!NOTE]
> 应用发现目前仅适用于 VMware Vm，并仅限于发现。 我们尚未提供基于应用的评估。 


## <a name="before-you-start"></a>开始之前

- 请确保已完成以下操作：
    - [创建](how-to-add-tool-first-time.md)Azure Migrate 项目。
    - 向项目[添加](how-to-assess.md)了 Azure Migrate：服务器评估工具。
- 查看[应用发现支持和要求](migrate-support-matrix-vmware.md#vmware-requirements)。
- 请确保运行应用发现的 Vm 安装了 PowerShell 2.0 版或更高版本，并且安装了 VMware 工具（高于10.2.0）。
- 检查部署 Azure Migrate 设备的[要求](migrate-appliance.md)。


## <a name="deploy-the-azure-migrate-appliance"></a>部署 Azure Migrate 设备

1. [查看](migrate-appliance.md#appliance---vmware)部署 Azure Migrate 设备的要求。
2. 查看设备需要在[公共](migrate-appliance.md#public-cloud-urls)和[政府云中](migrate-appliance.md#government-cloud-urls)访问的 Azure url。
3. [查看](migrate-appliance.md#collected-data---vmware)设备在发现和评估期间收集的数据。
4. [注意](migrate-support-matrix-vmware.md#port-access-requirements)设备的端口访问要求。
5. [部署 Azure Migrate 设备](how-to-set-up-appliance-vmware.md)以启动发现。 若要部署设备，请下载 .OVA 模板并将其导入 VMware，以将设备创建为 VMware VM。 配置设备，然后将其注册到 Azure Migrate。
6. 部署设备时，若要启动连续发现，请指定以下各项：
    - 要连接到的 vCenter Server 的名称。
    - 为设备创建的用于连接 vCenter Server 的凭据。
    - 为设备创建的用于连接到 Windows/Linux Vm 的帐户凭据。

部署设备并提供凭据后，设备会开始连续发现 VM 元数据和性能数据，以及应用、功能和角色的发现。  应用发现的持续时间取决于你拥有的 Vm 数量。 应用发现 500 Vm 通常要花费一小时。

## <a name="verify-permissions"></a>验证权限

你[创建了一个 vCenter Server 只读帐户](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)用于发现和评估。 只读帐户需要为**虚拟机**  >  **来宾操作**启用特权，才能与 VM 交互以进行应用发现。

### <a name="add-the-user-account-to-the-appliance"></a>将用户帐户添加到设备

添加用户帐户，如下所示：

1. 打开 "设备管理" 应用。 
2. 导航到 "**提供 vCenter 详细信息**" 面板。
3. 在 "**发现 vm 上的应用程序和依赖项**" 中，单击 "**添加凭据**"
3. 选择**操作系统**，提供帐户的友好名称和**用户名** / **密码**
6. 单击“保存” 。
7. 单击 "**保存并启动发现**"。

    ![添加 VM 用户帐户](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)


## <a name="review-and-export-the-inventory"></a>查看并导出清单

发现结束后，如果为应用发现提供凭据，则可以在 Azure 门户中查看和导出应用清单。

1. 在**Azure Migrate-服务器**  >  **Azure Migrate：服务器评估**中，单击显示的计数打开 "**发现的服务器**" 页。

    > [!NOTE]
    > 在此阶段，你还可以选择为发现的计算机设置依赖关系分析，这样你就可以在想要评估的计算机上可视化依赖项。 [了解](concepts-dependency-visualization.md)有关依赖项分析的详细信息。

2. 在**发现的应用程序**中，单击显示的计数。
3. 在**应用程序清单**中，可以查看已发现的应用、角色和功能。
4. 若要导出清单，请在 "**发现的服务器**" 中，单击 "**导出应用程序清单**"。

应用清单以 Excel 格式导出和下载。 **应用程序清单**表显示所有计算机上发现的所有应用。

## <a name="next-steps"></a>后续步骤

- 为发现的服务器[创建评估](how-to-create-assessment.md)。
- 使用 Azure Migrate 评估 SQL Server 数据库[：数据库评估](/sql/dma/dma-assess-sql-data-estate-to-sqldb?view=sql-server-2017)。
