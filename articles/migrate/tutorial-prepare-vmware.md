---
title: 使用 Azure Migrate 准备好要评估并迁移到 Azure 的 VMware VM | Microsoft Docs
description: 介绍如何使用 Azure Migrate 准备好要评估并迁移到 Azure 的本地 VMware VM。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 16c0354466d5c2a1207873a1e83e209da9339705
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509995"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>准备 VMware VM 以进行评估和迁移到 Azure

本文介绍如何使用 [Azure Migrate](migrate-services-overview.md) 准备好要评估并迁移到 Azure 的本地 VMware VM。

[Azure Migrate](migrate-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 


本教程是介绍如何评估和迁移 VMware VM 的教程系列中的第一篇文章。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 准备 Azure。 设置要与 Azure Migrate 配合使用的 Azure 帐户和资源的权限。
> * 准备本地 VMware 服务器和 VM 以进行 VM 评估。
> * 准备本地 VMware 服务器和 VM 以进行 VM 迁移。

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看 VMware 评估和迁移的操作指南。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure"></a>准备 Azure

需要以下 Azure 权限：

- 你的 Azure 帐户需要有权创建用于评估和迁移的 Azure Migrate 项目。 
- 为了对 VMware VM 进行评估和无代理迁移，Azure Migrate 将运行一个可发现 VM 并将 VM 元数据和性能数据发送到 Azure Migrate 的轻型设备。 在 Azure 中，你需要有权注册 Azure Migrate 设备。
- 若要使用 Azure Migrate 服务器迁移工具迁移 VMware VM，Azure Migrate 需在资源组中创建一个 Key Vault，用于管理订阅中复制存储帐户的访问密钥。 若要创建保管库，需要对 Azure Migrate 项目所在的资源组拥有角色分配权限。 


### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 
3. 你应该拥有“参与者”或“所有者”权限。  
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。

### <a name="assign-permissions-to-register-the-appliance"></a>分配注册设备的权限

若要部署 Azure Migrate 设备，以便对 VM 进行评估或无代理迁移，需要注册该设备。

- 在设备注册过程中，Azure Migrate 将创建两个 Azure Active Directory (Azure AD) 应用，用于唯一标识设备
    - 第一个应用将与 Azure Migrate 服务终结点通信。
    - 第二个应用访问注册期间创建的 Azure Key Vault，以存储 Azure AD 应用信息和设备配置设置。
- 可使用以下方法之一分配权限，使 Azure Migrate 能够创建这些 Azure AD 应用：
    - 租户/全局管理员可为租户中的用户授予创建和注册 Azure AD 应用的权限。
    - 租户/全局管理员可将“应用程序开发人员”角色（拥有权限）分配到帐户。

值得注意的是：

- 除上述权限外，应用对订阅不拥有任何其他访问权限。
- 只有在注册新的设备时，你才需要这些权限。 设置设备后可以删除这些权限。 


#### <a name="grant-account-permissions"></a>授予帐户权限

租户/全局管理员可按如下所述授予权限

1. 在 Azure AD 中，租户/全局管理员应导航到“Azure Active Directory” > “用户” > “用户设置”    。
2. 管理员应将“应用注册”设置为“是”   。

    ![Azure AD 权限](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> 这是不受影响的默认设置。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>分配“应用程序开发人员”角色 

租户/全局管理员可将“应用程序开发人员”角色分配到帐户。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

## <a name="assign-role-assignment-permissions"></a>分配角色分配权限

按如下所述分配对 Azure Migrate 项目所在的资源组的角色分配权限：

1. 在 Azure 门户上的资源组中，选择“访问控制(IAM)”。 
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。 

    - 若要运行服务器评估，“参与者”权限足以。 
    - 若要运行无代理服务器迁移，应该拥有“所有者”（或“参与者”和“用户访问管理员”）权限。   

3. 如果没有所需的权限，可请求资源组所有者分配这些权限。 



## <a name="prepare-for-vmware-vm-assessment"></a>准备 VMware VM 评估

若要准备 VMware VM 评估，需验证 VMware 主机和 VM 设置，并验证设备部署设置。

### <a name="verify-vmware-settings"></a>验证 VMware 设置

1. [验证](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) VMware 服务器对 VM 评估的要求。
2. [确保](migrate-support-matrix-vmware.md#assessment-port-requirements)在 vCenter 服务器上打开所需端口。


### <a name="set-up-an-account-for-assessment"></a>设置评估帐户

Azure Migrate 需访问 vCenter Server 来发现用于评估和无代理迁移的 VM。 若只进行评估，需要一个适用于 vCenter Server 的只读帐户。

如果使用基于 URL 的防火墙代理，请允许访问所需的 [Azure URL](migrate-support-matrix-vmware.md#assessment-url-access-requirements)。

确保代理解析在查找 URL 时收到的任何 CNAME 记录。


### <a name="verify-appliance-settings-for-assessment"></a>验证用于评估的设备设置

在设置 Azure Migrate 设备并在下一篇教程中开始评估之前，需要准备好设备部署。

1. [验证](migrate-support-matrix-vmware.md#assessment-appliance-requirements)在 VMware 中设置 Azure Migrate 设备的要求。
2. [查看](migrate-support-matrix-vmware.md#assessment-url-access-requirements)设备需要访问的 Azure URL。
3. 查看设备在发现和评估期间要收集的数据。
4. [注意](migrate-support-matrix-vmware.md#assessment-port-requirements)设备的端口访问要求。
5. 我们使用 OVA 文件将 Azure Migrate 设备部署为 VMware VM。 在 vCenter Server 上，请确保帐户有权使用 OVA 文件创建 VM。


## <a name="prepare-for-agentless-vmware-migration"></a>准备无代理 VMware 迁移

查看无代理迁移 VMware VM 的要求。

1. [查看](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements)无代理迁移的 VMware 服务器要求。
2. 设置一个帐户，以便使用进行无代理迁移[所需的权限](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions)访问 vCenter Server。
3. [注意](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)需要使用无代理迁移来迁移到 Azure 的 VMware VM 的要求。
4. [查看](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements)无代理迁移的设备要求。
5. 注意进行无代理迁移的设备 [URL 访问](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements)和[端口访问](migrate-support-matrix-vmware.md#agentless-migration-port-requirements)要求。


## <a name="prepare-for-agent-based-vmware-migration"></a>准备基于代理的 VMware 迁移

查看对 VMware VM 进行基于代理的迁移的要求。

1. [查看](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements)无代理迁移的 VMware 服务器要求。 
2. 设置一个帐户，以便使用进行无代理迁移[所需的权限](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions)访问 vCenter Server。
3. [注意](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements)需要使用基于代理的迁移来迁移到 Azure 的 VMware VM 的要求，包括在要迁移的每个 VM 上安装移动服务。
4. 注意 [URL 访问](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements)。
5. 查看在每个 VM 上运行的移动服务的[端口访问](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements)要求，以及 Azure Migrate 配置服务器的该要求。

## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：
 
> [!div class="checklist"] 
> * 设置 Azure 权限。
> * 准备 VMware 以进行评估和迁移。


继续学习第二篇教程以设置 Azure Migrate 项目，并评估要迁移到 Azure 的 VMware VM。

> [!div class="nextstepaction"] 
> [访问 VMware VM](./tutorial-migrate-vmware.md) 

