---
title: 准备好要通过 Azure Migrate 进行评估/迁移的物理服务器
description: 了解如何准备好要通过 Azure Migrate 进行评估/迁移的物理服务器。
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: b7bde5df943a35bfcf08ace3b454a26dae8c1d89
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901415"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>准备评估物理服务器并将其迁移到 Azure

本文介绍如何使用 [Azure Migrate](migrate-services-overview.md) 准备评估本地物理服务器。

[Azure Migrate](migrate-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 

本教程是介绍如何使用 Azure Migrate 评估物理服务器的教程系列中的第一篇文章。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备 Azure。 设置要与 Azure Migrate 配合使用的 Azure 帐户和资源的权限。
> * 准备本地物理服务器以进行服务器评估。


> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看物理服务器评估的操作指南。


如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure-for-server-assessment"></a>准备 Azure 进行服务器评估

设置 Azure 以便使用 Azure Migrate。 

**任务** | **详细信息** 
--- | --- 
**创建 Azure Migrate 项目** | Azure 帐户需要“参与者”或“所有者”权限才能创建项目。 
**注册资源提供程序（仅限评估）** | Azure Migrate 使用轻型 Azure Migrate 设备通过“Azure Migrate:服务器评估”来发现并评估计算机。<br/><br/> 在设备注册过程中，资源提供程序会注册到在设备中选择的订阅。 [了解详细信息](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> 需要订阅的“参与者”或“所有者”角色才能注册资源提供程序。
**创建 Azure AD 应用（仅限评估）** | 注册设备时，Azure Migrate 会创建一个 Azure Active Directory (Azure AD) 应用，方便在设备上运行的代理与其在 Azure 上运行的相应服务通信。 [了解详细信息](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> 你需要具有创建 Azure AD 应用的权限（在“应用程序开发人员”角色中提供）。


### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限 

请检查你是否有权创建 Azure Migrate 项目。

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 
3. 你应该拥有“参与者”或“所有者”权限。  
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。


### <a name="assign-permissions-to-register-the-appliance"></a>分配注册设备的权限 

可以使用以下方法之一为 Azure Migrate 分配权限，以便在注册设备期间创建 Azure AD 应用：

- 租户/全局管理员可为租户中的用户授予创建和注册 Azure AD 应用的权限。
- 租户/全局管理员可将“应用程序开发人员”角色（拥有权限）分配到帐户。

> [!NOTE]
> - 除上述权限外，应用对订阅没有任何其他访问权限。
> - 只有在注册新的设备时，你才需要这些权限。 设置设备后可以删除这些权限。


#### <a name="grant-account-permissions"></a>授予帐户权限

租户/全局管理员可按如下所述授予权限：

1. 在 Azure AD 中，租户/全局管理员应导航到“Azure Active Directory” > “用户” > “用户设置”    。
2. 管理员应将“应用注册”设置为“是”   。

    ![Azure AD 权限](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> 这是不受影响的默认设置。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

#### <a name="assign-application-developer-role"></a>分配“应用程序开发人员”角色

租户/全局管理员可将“应用程序开发人员”角色分配到帐户。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。


## <a name="prepare-azure-for-physical-server-migration"></a>准备 Azure 进行物理服务器迁移

使用“服务器迁移”功能准备 Azure 进行物理服务器迁移。

**任务** | **详细信息**
--- | ---
**创建 Azure Migrate 项目** | Azure 帐户需要“参与者”或“所有者”权限才能创建项目。
**验证 Azure 帐户的权限** | Azure 帐户需要有权创建 VM 并将数据写入 Azure 托管磁盘。
**创建 Azure 网络** | 在 Azure 中设置网络。


### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 
3. 你应该拥有“参与者”或“所有者”权限。  
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。


### <a name="assign-azure-account-permissions"></a>分配 Azure 帐户权限

将“虚拟机参与者”角色分配给 Azure 帐户。 这提供了执行以下操作的权限：
  - 在所选资源组中创建 VM。
  - 在所选虚拟网络中创建 VM。
  - 写入 Azure 托管磁盘。 

### <a name="create-an-azure-network"></a>创建 Azure 网络

[设置](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Azure 虚拟网络 (VNet)。 复制到 Azure 时，会创建 Azure VM 并将其加入到在设置迁移时指定的 Azure VNet。


## <a name="prepare-for-physical-server-assessment"></a>准备物理服务器评估

若要准备物理服务器评估，需验证物理服务器设置，并验证设备部署设置：

### <a name="verify-physical-server-settings"></a>验证物理服务器设置

1. 验证服务器评估的[物理服务器要求](migrate-support-matrix-physical.md#physical-server-requirements)。
2. 确保物理服务器上已打开[所需的端口](migrate-support-matrix-physical.md#port-access)。


### <a name="verify-appliance-settings"></a>验证设备设置

在设置 Azure Migrate 设备并在下一篇教程中开始评估之前，需要准备好设备部署。

1. [验证](migrate-appliance.md#appliance---physical)物理服务器的设备要求。
2. 查看设备在[公有](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)云中需要访问的各个 Azure URL。
3. [查看](migrate-appliance.md#collected-data---vmware)设备在发现和评估期间要收集的数据。
4. [请注意](migrate-support-matrix-physical.md#port-access)端口访问要求进行物理服务器评估。


### <a name="set-up-an-account-for-physical-server-discovery"></a>设置物理服务器发现的帐户

Azure Migrate 需要拥有发现本地服务器的权限。

- **Windows：** 在要包括在发现中的所有 Windows 服务器上设置一个本地用户帐户。 需要将该用户帐户添加到以下组：- 远程管理用户 - 性能监视器用户 - 性能日志用户
- **Linux：** 需要在要发现的 Linux 服务器上拥有根帐户。

## <a name="prepare-for-physical-server-migration"></a>为物理服务器迁移做准备

查看物理服务器迁移的要求。

> [!NOTE]
> 迁移物理计算机时，“Azure Migrate:服务器迁移”使用与 Azure Site Recovery 服务中基于代理的灾难恢复功能相同的复制体系结构，并且某些组件共享相同的代码库。 某些内容可能链接到 Site Recovery 文档。

- [查看](migrate-support-matrix-physical-migration.md#physical-server-requirements)物理服务器迁移要求。
- “Azure Migrate:服务器迁移”使用复制服务器进行物理服务器迁移：
    - [查看](migrate-replication-appliance.md#appliance-requirements)复制设备的部署要求，以及用于在设备上安装 MySQL 的[选项](migrate-replication-appliance.md#mysql-installation)。
    - 查看复制设备访问公有云和政府云时所需的 [Azure URL](migrate-appliance.md#url-access)。
    - 查看复制设备的[端口] (migrate-replication-appliance.md#port-access)访问要求。




## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 设置 Azure 帐户权限。
> * 准备物理服务器以进行评估。

继续学习下一篇教程以创建 Azure Migrate 项目，并评估要迁移到 Azure 的物理服务器

> [!div class="nextstepaction"]
> [评估物理服务器](./tutorial-assess-physical.md)
