---
title: 使用 Azure Migrate 准备 VMware VM 以进行评估/迁移
description: 了解如何使用 Azure Migrate 准备评估/迁移 VMware VM。
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8b812924c0922d460c631baec8b0e13a9f45cd76
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "86109569"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>准备 VMware VM 以进行评估和迁移到 Azure

本文帮助你使用 [Azure Migrate](migrate-services-overview.md) 准备要评估和迁移到 Azure 的本地 VMware VM。

本教程是介绍如何评估和迁移 VMware VM 的教程系列中的第一篇文章。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备 Azure 以便使用 Azure Migrate。
> * 准备使用 Azure Migrate 服务器评估工具评估 VMware VM。
> * 准备使用 Azure Migrate 服务器迁移工具迁移 VMware VM。 

> [!NOTE]
> 教程介绍了某个方案的最简单部署路径。 作为快速的概念证明，它们非常有用。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure"></a>准备 Azure

下表总结了需要在 Azure 中完成的任务。 有关每个任务的说明，请参阅该表。

**任务** | **详细信息** | **权限**
--- | --- | ---
**创建 Azure Migrate 项目** | Azure Migrate 项目提供了一个中心位置，用于使用 Azure Migrate 工具、Microsoft 工具和第三方产品来协调和管理评估与迁移。 | Azure 帐户需要项目所在的资源组中的“参与者”或“所有者”权限。
**注册设备** | Azure Migrate 使用轻型 Azure Migrate 设备来发现 VM，使用服务器评估工具对其进行评估，并使用服务器迁移工具通过无代理迁移对其进行迁移。 [详细了解](migrate-appliance-architecture.md#appliance-registration)有关注册的信息。 | 若要注册设备，Azure 帐户需要 Azure 订阅的“参与者”或“所有者”权限。
**创建 Azure AD 应用** | 注册设备时，Azure Migrate 将创建 Azure Active Directory (Azure AD) 应用。 <br/><br/> - 第一个应用用于设备上运行的代理与 Azure Migrate 之间的通信。 <br/><br/> - 第二个应用专门用于访问在用户的订阅中创建的 KeyVault，以便进行无代理 VMware VM 迁移。   | Azure 帐户需要创建 Azure AD 应用的权限。
**创建 Key Vault** | 若要使用无代理迁移来迁移 VMware VM，Azure Migrate 需要创建一个 Key Vault，用于管理订阅中复制帐户的访问密钥。 | 若要允许 Azure Migrate 创建 Key Vault，需要在 Azure Migrate 项目所在的资源组中设置权限（所有者或参与者和用户访问管理员）。


### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。
3. 你应该拥有“参与者”或“所有者”权限。 
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

### <a name="assign-permissions-to-create-azure-ad-apps"></a>分配用于创建 Azure AD 应用的权限

若要注册设备，你的 Azure 帐户需要创建 Azure AD 应用的权限。 使用下面的一种方法分配权限：

- **方法 1：向帐户授予权限**：租户/全局管理员可为租户中的用户帐户授予创建和注册 Azure AD 应用的权限。
- **方法 2：向用户帐户分配具有权限的角色**：租户/全局管理员可将“应用程序开发人员”角色（拥有所需权限）分配到用户帐户。

> [!NOTE]
> 只有在注册新的设备时，你才需要这些权限。 设置设备后可以删除这些权限。


#### <a name="method-1-grant-permissions-to-the-account"></a>方法 1：向帐户授予权限

按如下方式向帐户授予权限：

1. 确保你是租户管理员或全局管理员。然后，在 Azure AD 中，导航到“Azure Active Directory” > “用户” > “用户设置”  。
2. 将“应用注册”设置为“是” 。 这是不受影响的默认设置。 [了解详细信息](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

    ![Azure AD 权限](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>方法 2：分配“应用程序开发人员”角色

另外，租户/全局管理员还可将“应用程序开发人员”角色分配给帐户。 [详细了解](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)如何分配角色。

### <a name="assign-permissions-to-create-a-key-vault"></a>分配创建 Key Vault 的权限

若要允许 Azure Migrate 创建 Key Vault，请按如下所示分配权限：

1. 在 Azure 门户上的资源组中，选择“访问控制(IAM)”。
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。

    - 若要运行服务器评估，“参与者”权限足以。
    - 若要运行无代理服务器迁移，应该拥有“所有者”（或“参与者”和“用户访问管理员”）权限。  

3. 如果没有所需的权限，可请求资源组所有者分配这些权限。

## <a name="prepare-for-assessment"></a>准备进行评估

要准备 VMware VM 评估，你需要：

1. 验证 VMware 设置。 确保要迁移的 vCenter Server 和 VM 满足要求。
2. 设置评估权限。 Azure Migrate 使用 vCenter 帐户访问 vCenter Server，以发现并评估 VM。
3. 验证设备要求。 在下一个教程中部署 Azure Migrate 设备之前，请验证其部署要求。

### <a name="verify-vmware-settings"></a>验证 VMware 设置

1. [查看 VMware 评估要求](migrate-support-matrix-vmware.md#vmware-requirements)。
2. [确保](migrate-support-matrix-vmware.md#port-access-requirements)在 vCenter 服务器上打开所需端口。
3. 在 vCenter Server 上，检查帐户是否有权使用 OVA 文件创建 VM。 使用 OVA 文件将 Azure Migrate 设备部署为 VMware VM 时，则需要这一权限。
4. 在将 VM 迁移到 Azure 之前，需要在 VM 上进行一些更改。

    - 对于某些操作系统，Azure Migrate 可自动做出这些更改。 
    - 在开始迁移之前，必须做出这些更改。 如果在做出更改之前迁移 VM，VM 可能无法在 Azure 中启动。
    - 查看在 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 上需要进行的更改。


### <a name="set-up-permissions-for-assessment"></a>设置评估权限

Azure Migrate 需访问 vCenter Server，以便 Azure Migrate 设备发现用于评估和无代理迁移的 VM。 按如下方式设置帐户：

1. 在 vSphere Web 客户端中，打开“管理” > 访问” > “SSO 用户和组”  。
2. 在“用户”中，单击“新建用户”图标 。
3. 键入新用户详细信息。
4. 根据表值选择权限。

    **功能** | **帐户权限**
    --- | ---
    [评估 VM](tutorial-assess-vmware.md) | 对于评估，帐户需要只读访问。
    [发现 VM 应用、角色和功能](how-to-discover-applications.md) | 若要使用应用发现，用于评估的只读帐户需要为“虚拟机” > “来宾操作”启用特权 。
    [分析 VM 上的依赖项（无代理）](how-to-create-group-machine-dependencies-agentless.md) | 若要分析依赖项，用于评估的只读帐户需要为“虚拟机” > “来宾操作”启用特权 。
    
> [!NOTE]
> 若要将用于评估的 VM 发现限制在特定范围内，请查看[本文](set-discovery-scope.md#assign-a-role-for-assessment)。

### <a name="verify-appliance-settings-for-assessment"></a>验证用于评估的设备设置

在[下一个教程](tutorial-assess-vmware.md)中，设置 Azure Migrate 设备并开始评估。 在执行此操作之前，请查看设备要求，如下所示： 

1. [查看部署 Azure Migrate 设备的要求](migrate-appliance.md#appliance---vmware)。
2. 查看设备在[公有](migrate-appliance.md#public-cloud-urls)和[政府](migrate-appliance.md#government-cloud-urls)云中需要访问的各个 Azure URL。
3. [注意](migrate-support-matrix-vmware.md#port-access-requirements)设备使用的端口。
4. [查看设备在发现和评估期间收集的数据](migrate-appliance.md#collected-data---vmware)。

## <a name="prepare-for-agentless-vmware-migration"></a>准备无代理 VMware 迁移

可以使用[无代理或基于代理的迁移](server-migrate-overview.md)来迁移 VMware VM。 本部分概述无代理迁移的要求。

1. [确定](server-migrate-overview.md#compare-migration-methods)是否要使用无代理迁移。
2. [查看](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)要迁移的计算机的虚拟机监控程序要求。
3. [查看](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)要使用无代理迁移进行迁移的 VMware VM 的要求。
4. [查看](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) Azure Migrate 设备进行无代理迁移的要求。
5. 记下在[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)中需要访问的 URL。
6. 查看[端口访问](migrate-support-matrix-vmware-migration.md#port-requirements-agentless)要求。
7. 如下一过程所述，设置无代理迁移的权限。


### <a name="assign-permissions-to-an-account"></a>向帐户分配权限

Azure Migrate 设备连接到 vCenter Server，通过无代理迁移来发现和迁移 VM。 可以将设备所需的权限分配给用户帐户，或创建具有权限的角色，并将该角色分配给用户帐户。

1. 在 vSphere Web 客户端中，打开“管理” > 访问” > “SSO 用户和组”  。
2. 在“用户”中，单击“新建用户”图标 。
3. 键入新用户详细信息。
4. 分配[这些权限](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)

#### <a name="create-a-role-and-assign-to-an-account"></a>创建角色并将其分配给帐户

此外，也可以创建帐户。 然后按如下方式，创建一个角色并将其分配给帐户：

1. 以 vCenter Server 管理员身份登录到 vSphere Web 客户端
2. 选择相应 vCenter Server 实例 >“创建角色”。
3. 指定角色名称（例如 Azure_Migrate）并向角色分配[所需权限](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)。

    ![vCenter Server 帐户特权](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. 现在创建一个帐户，并将该角色分配给该帐户。

> [!NOTE]
> 若要将用于无代理迁移的 VM 发现限制在特定范围内，请查看[本文](set-discovery-scope.md#assign-a-role-for-agentless-migration)。



## <a name="prepare-for-agent-based-vmware-migration"></a>准备基于代理的 VMware 迁移

可以使用[无代理或基于代理的迁移](server-migrate-overview.md)来迁移 VMware VM。 本部分概述基于代理的迁移的要求。

1. [确定](server-migrate-overview.md#compare-migration-methods)是否要使用基于代理的迁移。
1. [查看](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based)要迁移的计算机的虚拟机监控程序要求。
2. [查看](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) VMware VM 要求，包括在要迁移的每个 VM 上安装移动服务。
3. 基于代理的迁移使用复制设备：
    - [查看](migrate-replication-appliance.md#appliance-requirements)复制设备的部署要求。
    - [查看](migrate-replication-appliance.md#mysql-installation)在设备上安装 MySQL 的选项。
    - 记下在[公有云](migrate-replication-appliance.md#url-access)和[政府云](migrate-replication-appliance.md#azure-government-url-access)中需要访问的 URL。
    - 查看复制设备的[端口访问](migrate-replication-appliance.md#port-access)要求。
4. 在将 VM 迁移到 Azure 之前，需要在 VM 上进行一些更改。 查看在 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 上需要进行的更改。



## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 设置 Azure 权限。
> * 准备 VMware 以进行评估和迁移。


继续学习第二篇教程以设置 Azure Migrate 项目，并评估要迁移到 Azure 的 VMware VM。

> [!div class="nextstepaction"]
> [访问 VMware VM](./tutorial-assess-vmware.md)
