---
title: 在本地服务器上发现应用、角色和功能，并提供 Azure Migrate
description: 了解如何通过 Azure Migrate Server 评估发现本地服务器上的应用、角色和功能。
ms.topic: article
ms.date: 03/12/2020
ms.openlocfilehash: ff9f5489b513cd1405e6b093d7537e4cbcead041
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744617"
---
# <a name="discover-machine-apps-roles-and-features"></a>发现计算机应用、角色和功能

本文介绍如何使用 Azure Migrate：服务器评估来发现本地服务器上的应用程序、角色和功能。

发现应用的清单，以及在本地计算机上运行的角色/功能，可帮助您识别和计划针对您的工作负荷定制的 Azure 迁移路径。

> [!NOTE]
> 应用发现目前仅适用于 VMware Vm，并仅限于发现。 我们尚未提供基于应用的评估。 针对本地 VMware Vm、Hyper-v Vm 和物理服务器的基于计算机的评估。

使用 Azure Migrate 的应用程序发现：服务器评估是无代理的。 不会在计算机和 Vm 上安装任何内容。 服务器评估使用 Azure Migrate 设备，同时使用计算机来宾凭据执行发现。 设备使用 VMware Api 远程访问 VMware 计算机。


## <a name="before-you-start"></a>开始之前

1. 请确保已[创建](how-to-add-tool-first-time.md)Azure Migrate 项目。
2. 请确保已将 Azure Migrate： Server 评估工具[添加](how-to-assess.md)到项目。
4. 查看[vmware 要求](migrate-support-matrix-vmware.md#vmware-requirements)，了解如何通过 Azure Migrate 设备发现和评估 vmware vm。
5. 检查部署 Azure Migrate 设备的[要求](migrate-appliance.md)。
6. 验证应用程序发现的[支持和要求](migrate-support-matrix-vmware.md#application-discovery)。



## <a name="deploy-the-azure-migrate-appliance"></a>部署 Azure Migrate 设备

1. [查看](migrate-appliance.md#appliance---vmware)部署 Azure Migrate 设备的要求。
2. 查看设备需要在[公共](migrate-appliance.md#public-cloud-urls)和[政府云中](migrate-appliance.md#government-cloud-urls)访问的 Azure url。
3. [查看](migrate-appliance.md#collected-data---vmware)设备在发现和评估期间收集的数据。
4. [注意](migrate-support-matrix-vmware.md#port-access)设备的端口访问要求。
5. [部署 Azure Migrate 设备](how-to-set-up-appliance-vmware.md)以启动发现。 若要部署设备，请下载 .OVA 模板并将其导入 VMware，以将设备创建为 VMware VM。 配置设备，然后将其注册到 Azure Migrate。
6. 部署设备时，若要启动连续发现，请指定以下各项：
    - 要连接到的 vCenter Server 的名称。
    - 为设备创建的用于连接 vCenter Server 的凭据。
    - 为设备创建的用于连接到 Windows/Linux Vm 的帐户凭据。

部署设备并提供凭据后，设备会开始连续发现 VM 元数据和性能数据，以及应用、功能和角色的发现。  应用发现的持续时间取决于你拥有的 Vm 数量。 应用发现 500 Vm 通常要花费一小时。

## <a name="prepare-a-user-account"></a>准备用户帐户

创建用于发现的帐户，并将其添加到设备。

### <a name="create-a-user-account-for-discovery"></a>创建用于发现的用户帐户

设置用户帐户，以便服务器评估可以访问 VM 以便发现。 [了解](migrate-support-matrix-vmware.md#application-discovery)帐户要求。


### <a name="add-the-user-account-to-the-appliance"></a>将用户帐户添加到设备

将用户帐户添加到设备。

1. 打开 "设备管理" 应用。 
2. 导航到 "**提供 vCenter 详细信息**" 面板。
3. 在 "**发现 vm 上的应用程序和依赖项**" 中，单击 "**添加凭据**"
3. 选择**操作系统**，提供帐户的友好名称和**用户名**/**密码**
6. 单击“保存”  。
7. 单击 "**保存并启动发现**"。

    ![添加 VM 用户帐户](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)





## <a name="review-and-export-the-inventory"></a>查看并导出清单

发现结束后，如果为应用发现提供凭据，则可以在 Azure 门户中查看和导出应用清单。

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
