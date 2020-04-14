---
title: 使用 Azure Migrate 准备 VMware VM 以进行评估/迁移
description: 了解如何使用 Azure Migrate 准备评估/迁移 VMware VM。
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 2e8aa72300c840832168138015e0a01ab054f954
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619424"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>准备 VMware VM 以进行评估和迁移到 Azure

本文帮助你使用 [Azure Migrate](migrate-services-overview.md) 准备要评估和/或迁移到 Azure 的本地 VMware VM。



本教程是介绍如何评估和迁移 VMware VM 的教程系列中的第一篇文章。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备 Azure 以便使用 Azure Migrate。
> * 使用 Azure Migrate 服务器评估工具为 VM 评估准备 VMware。
> * 准备 VMware 以使用 Azure Migrate 服务器迁移工具迁移 VM。 

> [!NOTE]
> 教程介绍了某个方案的最简单部署路径。 当学习如何设置部署以及作为快速概念验证时，它们非常有用。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure"></a>准备 Azure

你需要在 Azure 中对这些任务拥有这些权限，然后才能评估或迁移 VMware VM。

**任务** | **详细信息** 
--- | --- 
**创建 Azure Migrate 项目** | Azure 帐户需要“参与者”或“所有者”权限才能创建项目。 
**注册资源提供程序** | Azure Migrate 使用轻型 Azure Migrate 设备，通过“Azure Migrate: 服务器评估”来发现和评估 VMware VM，并将这些 VM 迁移到 Azure。<br/><br/> 在设备注册过程中，资源提供程序会注册到在设备中选择的订阅。 [了解详细信息](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> 需要订阅的“参与者”或“所有者”角色才能注册资源提供程序。
**创建 Azure AD 应用** | 注册设备时，Azure Migrate 将创建 Azure Active Directory (Azure AD) 应用。 <br/><br/> - 第一个应用用于设备上运行的代理与其各自在 Azure 上运行的服务之间的通信。<br/><br/> - 第二个应用专门用于访问在用户的订阅中创建的 KeyVault，以便进行无代理 VMware VM 迁移。 [了解详细信息](migrate-appliance-architecture.md#appliance-registration)。<br/><br/> 你需要具有创建 Azure AD 应用的权限（在“应用程序开发人员”角色中提供）。
**创建 Key Vault** | 若要使用无代理迁移来迁移 VMware VM，Azure Migrate 需要创建一个 Key Vault，用于管理订阅中复制存储帐户的访问密钥。<br/><br/> 若要创建保管库，需要对 Azure Migrate 项目所在的资源组拥有角色分配权限。




### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 
3. 你应该拥有“参与者”或“所有者”权限。  
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

### <a name="assign-permissions-to-register-the-appliance"></a>分配注册设备的权限

要注册设备，请为 Azure Migrate 分配权限，以便在注册设备期间创建 Azure AD 应用。 可使用以下方法之一分配权限：

- **授予权限**：租户/全局管理员可为租户中的用户授予创建和注册 Azure AD 应用的权限。
- **分配“应用程序开发人员”角色**：租户/全局管理员可将“应用程序开发人员”角色（拥有权限）分配到帐户。

> [!NOTE]
> - 除上述权限外，应用对订阅不拥有任何其他访问权限。
> - 只有在注册新的设备时，你才需要这些权限。 设置设备后可以删除这些权限。


#### <a name="grant-account-permissions"></a>授予帐户权限

如果希望租户/全局管理员授予权限，请执行如下操作：

1. 在 Azure AD 中，租户/全局管理员应导航到“Azure Active Directory” > “用户” > “用户设置”    。
2. 管理员应将“应用注册”设置为“是”   。 这是不受影响的默认设置。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

    ![Azure AD 权限](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>分配“应用程序开发人员”角色

另外，租户/全局管理员还可将“应用程序开发人员”角色分配给帐户。 [详细了解](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)如何分配角色。

### <a name="assign-permissions-to-create-a-key-vault"></a>分配创建 Key Vault 的权限

若要允许 Azure Migrate 创建 Key Vault，请按如下所示分配权限：

1. 在 Azure 门户上的资源组中，选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 

    - 若要运行服务器评估，“参与者”权限足以。 
    - 若要运行无代理服务器迁移，应该拥有“所有者”（或“参与者”和“用户访问管理员”）权限。   

3. 如果没有所需的权限，可请求资源组所有者分配这些权限。



## <a name="prepare-for-vmware-vm-assessment"></a>准备 VMware VM 评估

要准备 VMware VM 评估，你需要：

- 验证 VMware 设置  。 确保要迁移的 vCenter Server 和 VM 满足要求。
- **设置用于评估的帐户**。 Azure Migrate 使用此帐户访问 vCenter Server 来发现用于评估的 VM。
- 验证设备要求  。 在部署 Azure Migrate 设备之前，验证其部署要求。

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

1. [验证](migrate-appliance.md#appliance---vmware) Azure Migrate 设备要求。
2. [查看](migrate-appliance.md#url-access)设备需要访问的 Azure URL。 如果使用的是基于 URL 的防火墙或代理，请确保它允许访问所需的 URL。
3. [查看](migrate-appliance.md#collected-data---vmware)设备在发现和评估期间收集的数据。
4. [注意](migrate-support-matrix-vmware.md#port-access)设备的端口访问要求。




## <a name="prepare-for-agentless-vmware-migration"></a>准备无代理 VMware 迁移

查看 VMware VM 的[无代理迁移](server-migrate-overview.md)要求。

1. [查看](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) VMware 服务器要求。
2. [查看](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Azure Migrate 访问 vCenter Server 所需的权限。
3. [查看](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) VMware VM 要求。
4. [查看](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) Azure Migrate 设备要求。
5. 请注意 [URL 访问](migrate-appliance.md#url-access)和[端口访问](migrate-support-matrix-vmware-migration.md#agentless-ports)要求。

## <a name="prepare-for-agent-based-vmware-migration"></a>准备基于代理的 VMware 迁移

查看对 VMware VM 进行[基于代理的迁移](server-migrate-overview.md)的要求。

1. [查看](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) VMware 服务器要求。
2. [查看](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Azure Migrate 访问 vCenter Server 所需的权限。
2. [查看](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) VMware VM 要求，包括在要迁移的每个 VM 上安装移动服务。
3. 基于代理的迁移使用复制设备：
    - [查看](migrate-replication-appliance.md#appliance-requirements)复制设备的部署要求。
    - [查看](migrate-replication-appliance.md#mysql-installation)在设备上安装 MySQL 的选项。
    - 查看复制设备的 [URL](migrate-replication-appliance.md#url-access) 和[端口](migrate-replication-appliance.md#port-access)访问要求。
    
## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 设置 Azure 权限。
> * 准备 VMware 以进行评估和迁移。


继续学习第二篇教程以设置 Azure Migrate 项目，并评估要迁移到 Azure 的 VMware VM。

> [!div class="nextstepaction"]
> [访问 VMware VM](./tutorial-assess-vmware.md)
