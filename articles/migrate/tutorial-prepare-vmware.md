---
title: 使用 Azure Migrate 准备 VMware VM 以进行评估/迁移
description: 了解如何使用 Azure Migrate 准备评估/迁移 VMware VM。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4dec76140f61c433561ccfea07b833d9821acfc5
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028909"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>准备 VMware VM 以进行评估和迁移到 Azure

本文帮助你使用 [Azure Migrate](migrate-services-overview.md) 准备要评估和/或迁移到 Azure 的本地 VMware VM。

[Azure Migrate](migrate-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。


本教程是介绍如何评估和迁移 VMware VM 的教程系列中的第一篇文章。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备 Azure 以便使用 Azure Migrate。
> * 准备 VMware 进行 VM 评估。
> * 准备 VMware 进行 VM 迁移。

> [!NOTE]
> 教程介绍了某个方案的最简单部署路径。 当学习如何设置部署以及作为快速概念验证时，它们非常有用。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看 VMware 评估和迁移指南。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure"></a>准备 Azure

需要以下权限。

**任务** | **权限**
--- | ---
**创建 Azure Migrate 项目** | Azure 帐户需要创建项目的权限。
**注册 Azure Migrate 设备** | Azure Migrate 使用轻型 Azure Migrate 设备通过 Azure Migrate 服务器评估来评估 VMware VM，并通过 Azure Migrate 服务器迁移来执行 VMware VM 的[无代理迁移](server-migrate-overview.md)。 此设备发现 VM，并将 VM 元数据和性能数据发送到 Azure Migrate。<br/><br/>在注册过程中，Azure Migrate 将创建两个 Azure Active Directory 域服务 (Azure AD) 应用用于唯一标识设备，并需要权限来创建这些应用。<br/> - 第一个应用将与 Azure Migrate 服务终结点通信。<br/> - 第二个应用访问注册期间创建的 Azure Key Vault，以存储 Azure AD 应用信息和设备配置设置。
**创建 Key Vault** | 若要使用 Azure Migrate 服务器迁移工具迁移 VMware VM，Azure Migrate 需要创建一个 Key Vault，用于管理订阅中复制存储帐户的访问密钥。 若要创建保管库，需要对 Azure Migrate 项目所在的资源组拥有角色分配权限。


### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 
3. 你应该拥有“参与者”或“所有者”权限。  
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

### <a name="assign-permissions-to-register-the-appliance"></a>分配注册设备的权限

要注册设备，请为 Azure Migrate 分配权限，以便在注册设备期间创建 Azure AD 应用。 可使用以下方法之一分配权限：

- 租户/全局管理员可为租户中的用户授予创建和注册 Azure AD 应用的权限。
- 租户/全局管理员可将“应用程序开发人员”角色（拥有权限）分配到帐户。

> [!NOTE]
> - 除上述权限外，应用对订阅不拥有任何其他访问权限。
> - 只有在注册新的设备时，你才需要这些权限。 设置设备后可以删除这些权限。


#### <a name="grant-account-permissions"></a>授予帐户权限

租户/全局管理员可按如下所述授予权限

1. 在 Azure AD 中，租户/全局管理员应导航到“Azure Active Directory” > “用户” > “用户设置”    。
2. 管理员应将“应用注册”设置为“是”   。 这是不受影响的默认设置。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

    ![Azure AD 权限](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>分配“应用程序开发人员”角色

租户/全局管理员可将“应用程序开发人员”角色分配到帐户。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

### <a name="assign-role-assignment-permissions"></a>分配角色分配权限

若要使 Azure Migrate 创建 Key Vault，请按以下方式分配角色分配权限：

1. 在 Azure 门户上的资源组中，选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 

    - 若要运行服务器评估，“参与者”权限足以。 
    - 若要运行无代理服务器迁移，应该拥有“所有者”（或“参与者”和“用户访问管理员”）权限。   

3. 如果没有所需的权限，可请求资源组所有者分配这些权限。



## <a name="prepare-for-vmware-vm-assessment"></a>准备 VMware VM 评估

要准备 VMware VM 评估，你需要：

- 验证 VMware 设置  。 确保要迁移的 vCenter Server 和 VM 满足要求。
- 设置评估帐户  。 Azure Migrate 需要访问 vCenter Server 来发现用于评估的 VM。
- 验证设备要求  。 验证用于评估的 Azure Migrate 设备的部署要求。

### <a name="verify-vmware-settings"></a>验证 VMware 设置

1. [查看](migrate-support-matrix-vmware.md#vmware-requirements) VMware 服务器评估的要求。
2. [确保](migrate-support-matrix-vmware.md#port-access)在 vCenter 服务器上打开所需端口。
3. 在 vCenter Server 上，请确保帐户有权使用 OVA 文件创建 VM。 使用 OVA 文件将 Azure Migrate 设备部署为 VMware VM。


### <a name="set-up-an-account-for-assessment"></a>设置评估帐户

Azure Migrate 需访问 vCenter Server 来发现用于评估和无代理迁移的 VM。

- 如果打算发现应用程序或以无代理方式可视化依赖项，请创建具有只读访问权限的 vCenter Server 帐户，并针对“虚拟机” > “来宾操作”启用特权   。

  ![vCenter Server 帐户特权](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- 如果不打算执行应用程序发现和无代理依赖项可视化，请为 vCenter Server 设置一个只读帐户。

### <a name="verify-appliance-settings-for-assessment"></a>验证用于评估的设备设置

在设置 Azure Migrate 设备并在下一篇教程中开始评估之前，需要准备好设备部署。

1. [验证](migrate-appliance.md#appliance---vmware) VMware VM 的设备要求。
2. [查看](migrate-appliance.md#url-access)设备需要访问的 Azure URL。 如果使用的是基于 URL 的防火墙或代理，请确保它允许访问所需的 URL。
3. [查看](migrate-appliance.md#collected-data---vmware)设备在发现和评估期间要收集的数据。
4. [注意](migrate-support-matrix-vmware.md#port-access)设备的端口访问要求。




## <a name="prepare-for-agentless-vmware-migration"></a>准备无代理 VMware 迁移

查看无代理迁移 VMware VM 的要求。

1. [查看](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) VMware 服务器要求，以及 Azure Migrate 访问 vCenter Server 以使用 Azure Migrate 服务器迁移进行无代理迁移所需的[权限](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)。
2. [查看](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)需要使用无代理迁移来迁移到 Azure 的 VMware VM 的要求。
4. [查看](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance)使用 Azure Migrate 设备进行无代理迁移的要求。
5. 注意，无代理迁移需要[访问 URL](migrate-appliance.md#url-access) 和[访问端口](migrate-support-matrix-vmware-migration.md#agentless-ports)。


## <a name="prepare-for-agent-based-vmware-migration"></a>准备基于代理的 VMware 迁移

查看对 VMware VM 进行[基于代理的迁移](server-migrate-overview.md)的要求。

1. [查看](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) VMware 服务器要求，以及 Azure Migrate 访问 vCenter Server 以使用 Azure Migrate 服务器迁移进行基于代理的迁移所需的权限。
2. [查看](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms)需要使用基于代理的迁移来迁移到 Azure 的 VMware VM 的要求，包括在要迁移的每个 VM 上安装移动服务。
3. 基于代理的迁移使用复制设备：
    - [查看](migrate-replication-appliance.md#appliance-requirements)复制设备的部署要求，以及用于在设备上安装 MySQL 的[选项](migrate-replication-appliance.md#mysql-installation)。
    - 查看复制设备的 [URL](migrate-replication-appliance.md#url-access) 和[端口](migrate-replication-appliance.md#port-access)访问要求。
    
## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 设置 Azure 权限。
> * 准备 VMware 以进行评估和迁移。


继续学习第二篇教程以设置 Azure Migrate 项目，并评估要迁移到 Azure 的 VMware VM。

> [!div class="nextstepaction"]
> [访问 VMware VM](./tutorial-assess-vmware.md)
