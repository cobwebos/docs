---
title: 使用 Azure Migrate 准备好要评估并迁移到 Azure 的 Hyper-V VM | Microsoft Docs
description: 介绍如何使用 Azure Migrate 准备好要评估并迁移到 Azure 的 Hyper-V VM。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9e0d29770aa36f8e79bf08b7c5435ea2dbc4ae38
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840367"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>准备评估 Hyper-V VM 并将其迁移到 Azure

本文介绍如何使用 [Azure Migrate](migrate-services-overview.md) 准备好要评估并迁移到 Azure 的本地 Hyper-V VM。

[Azure Migrate](migrate-overview.md) 在一个中心位置提供多种工具，帮助你发现、评估应用、基础结构和工作负荷并将其迁移到 Microsoft Azure。 该中心包含 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 的产品/服务。 

本教程是介绍如何评估 Hyper-V VM 以及将其迁移到 Azure 的教程系列中的第一篇文章。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 准备 Azure。 设置要与 Azure Migrate 配合使用的 Azure 帐户和资源的权限。
> * 准备本地 Hyper-V 主机和 VM 以进行服务器评估。
> * 准备本地 Hyper-V 主机和 VM 以进行服务器迁移。


> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。 有关详细说明，请查看 Hyper-V 评估和迁移的操作指南。


如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure"></a>准备 Azure

### <a name="azure-permissions"></a>Azure 权限

需要拥有几项权限才能部署 Azure Migrate：

- 你的 Azure 帐户需要有权创建用于评估和迁移的 Azure Migrate 项目。 
- 你的 Azure 帐户需要有权注册 Azure Migrate 设备。
    - 对于评估，Azure Migrate 将运行一个可发现 Hyper-V VM 并将 VM 元数据和性能数据发送到 Azure Migrate 的轻型设备。
    - 在设备注册过程中，Azure Migrate 将创建两个 Azure Active Directory (Azure AD) 应用用于唯一标识设备：
        - 第一个应用将与 Azure Migrate 服务终结点通信。
        - 第二个应用访问注册期间创建的 Azure Key Vault，以存储 Azure AD 应用信息和设备配置设置。
    - 可使用以下方法之一分配权限，使 Azure Migrate 能够创建这些 Azure AD 应用：
        - 租户/全局管理员可为租户中的用户授予创建和注册 Azure AD 应用的权限。
        - 租户/全局管理员可将“应用程序开发人员”角色（拥有权限）分配到帐户。
    - 值得注意的是：
        - 除上述权限外，应用对订阅不拥有任何其他访问权限。
        - 只有在注册新的设备时，你才需要这些权限。 设置设备后可以删除这些权限。 


### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

请检查你是否有权创建 Azure Migrate 项目。

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。
3. 你应该拥有“参与者”或“所有者”权限。
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。


### <a name="assign-permissions-to-register-the-appliance"></a>分配注册设备的权限

如果你要部署 Azure Migrate 设备用于评估 VM，需要注册该设备。

- 在设备注册过程中，Azure Migrate 将创建两个 Azure Active Directory (Azure AD) 应用用于唯一标识设备
    - 第一个应用将与 Azure Migrate 服务终结点通信。
    - 第二个应用访问注册期间创建的 Azure Key Vault，以存储 Azure AD 应用信息和设备配置设置。
- 可使用以下方法之一分配权限，使 Azure Migrate 能够创建这些 Azure AD 应用：
    - 租户/全局管理员可为租户中的用户授予创建和注册 Azure AD 应用的权限。
    - 租户/全局管理员可将“应用程序开发人员”角色（拥有权限）分配到帐户。

值得注意的是：

- 除上述权限外，应用对订阅不拥有任何其他访问权限。
- 只有在注册新的设备时，你才需要这些权限。 设置设备后可以删除这些权限。 


#### <a name="grant-account-permissions"></a>授予帐户权限

租户/全局管理员可按如下所述授予权限：

1. 在 Azure AD 中，租户/全局管理员应导航到“Azure Active Directory” > “用户” > “用户设置”。
2. 管理员应将“应用注册”设置为“是”。

    ![Azure AD 权限](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> 这是不受影响的默认设置。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>分配“应用程序开发人员”角色 

租户/全局管理员可将“应用程序开发人员”角色分配到帐户。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。


## <a name="prepare-for-hyper-v-assessment"></a>准备 Hyper-V 评估

若要准备 Hyper-V 评估，请验证 Hyper-V 主机和 VM 设置，并验证设备部署设置。

### <a name="verify-hyper-v-host-settings"></a>验证 Hyper-V 主机设置

1. 验证服务器评估的 [Hyper-V 主机要求](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements)。
2. 确保 Hyper-V 主机上已打开[所需的端口](migrate-support-matrix-hyper-v.md#assessment-port-requirements)。

### <a name="enable-powershell-remoting-on-hosts"></a>在主机上启用 PowerShell 远程处理

按如下所述在每台主机上设置 PowerShell 远程处理：

1. 在每台主机上，以管理员身份打开 PowerShell 控制台。
2. 运行以下命令：

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>在主机上启用 CredSSP

如果 VM 磁盘位于 SMB 共享中，请在每台相关 Hyper-V 主机上完成此步骤。 此步骤用于发现包含 SMB 共享中的磁盘的 Hyper-V VM 的配置信息。 如果 SMB 共享中没有你的 VM 磁盘，则可跳过该步骤。

1. 识别运行包含 SMB 共享中的磁盘的 Hyper-V VM 的 Hyper-V 主机。
2. 在识别到的每台 Hyper-V 主机上运行以下命令：

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

- CredSSP 身份验证允许 Hyper-v 主机代表 Azure Migrate 客户端委托凭据。
- 可在所有 Hyper-v 主机上远程运行此命令。
- 如果在群集中添加了新的主机节点，则会自动添加这些节点以供发现，但需要根据情况在新节点上手动启用 CredSSP。

### <a name="verify-appliance-settings"></a>验证设备设置

在设置 Azure Migrate 设备并在下一篇教程中开始评估之前，需要准备好设备部署。

1. [验证](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements)设备要求。
2. [查看](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access)设备需要访问的 Azure URL。
3. 查看设备在发现和评估期间要收集的数据。
4. [注意](migrate-support-matrix-hyper-v.md#assessment-port-requirements)设备的端口访问要求。


### <a name="set-up-an-account-for-vm-discovery"></a>设置用于 VM 发现的帐户

Azure Migrate 需要拥有发现本地 VM 的权限。

- 在 Hyper-V 主机/群集上设置拥有管理员权限的域或本地用户帐户。

    - 对于要包含在发现中的所有主机和群集，都需要设置一个帐户。
    - 该帐户可以是本地帐户或域帐户。 我们建议为该帐户分配 Hyper-V 主机或群集的管理员权限。
    - 或者，如果你不想要分配管理员权限，则需要分配以下权限：
        - 远程管理用户
        - Hyper-V 管理员
        - 性能监视器用户

### <a name="enable-integration-services-on-vms"></a>在 VM 上启用 Integration Services

应在每个 VM 上启用 Integration Services，使 Azure Migrate 能够捕获 VM 上的操作系统信息。

- 在要发现和评估的每个 VM 上，启用 [Hyper-V Integration Services](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)。 

## <a name="prepare-for-hyper-v-migration"></a>准备 Hyper-V 迁移

1. [查看](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) Hyper-V 主机迁移要求。
2. [查看](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements)要迁移到 Azure 的 Hyper-V VM 要求。
3. [注意](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access)在迁移 VM 期间 Hyper-V 主机和群集需要访问的 Azure URL。

## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：
 
> [!div class="checklist"] 
> * 设置 Azure 帐户权限。
> * 准备要评估和迁移的 Hyper-V 主机与 VM。

继续学习下一篇教程以创建 Azure Migrate 项目，并评估要迁移到 Azure 的 Hyper-V Vm

> [!div class="nextstepaction"] 
> [评估 Hyper-V VM](./tutorial-assess-hyper-v.md) 
