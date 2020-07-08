---
title: 使用 Azure Migrate 准备 Hyper-V VM 以进行评估/迁移
description: 了解如何使用 Azure Migrate 准备评估/迁移 Hyper-V VM。
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 5f669de6bd8d767ca7b947fca883187dad9fe29d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109614"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>准备评估 Hyper-V VM 并将其迁移到 Azure

本文介绍如何使用 [Azure Migrate 的服务器评估工具](migrate-services-overview.md#azure-migrate-server-assessment-tool)和 [Azure Migrate 的服务器迁移工具](migrate-services-overview.md#azure-migrate-server-migration-tool)准备本地 Hyper-V VM 到 Azure 的评估和迁移。


本教程是介绍如何评估 Hyper-V VM 以及将其迁移到 Azure 的教程系列中的第一篇文章。 在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 准备 Azure 以便使用 Azure Migrate。
> * 准备评估 Hyper-V VM。
> * 准备迁移 Hyper-V VM 

> [!NOTE]
> 教程中演示了方案的最简单部署路径，使你能够快速设置概念证明。 教程尽可能使用默认选项，不会演示所有可能的设置和路径。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="prepare-azure"></a>准备 Azure

下表总结了需要在 Azure 中完成的任务。 表后面有说明。

**任务** | **详细信息** | **权限**
--- | --- | ---
**创建 Azure Migrate 项目** | Azure Migrate 项目提供了一个中心位置，用于使用 Azure Migrate 工具、Microsoft 工具和第三方产品来协调和管理评估与迁移。 | Azure 帐户需要项目所在的资源组中的“参与者”或“所有者”权限。
**注册设备** | Azure Migrate 使用轻型 Azure Migrate 设备来发现并评估 Hyper-V VM。 [了解详细信息](migrate-appliance-architecture.md#appliance-registration)。 | 若要注册设备，Azure 帐户需要 Azure 订阅的“参与者”或“所有者”权限。
**创建 Azure AD 应用** | 注册设备时，Azure Migrate 会创建一个 Azure Active Directory (Azure AD) 应用，用于在设备和 Azure Migrate 上运行的代理之间进行通信。 | Azure 帐户需要创建 Azure AD 应用的权限。
**创建 VM** | 需要权限才能在资源组和虚拟网络中创建 VM，也才能写入 Azure 托管磁盘。 | Azure 帐户需要“虚拟机参与者”角色。


### <a name="assign-permissions-to-create-project"></a>分配创建项目的权限

请检查你是否有权创建 Azure Migrate 项目。

1. 在 Azure 门户中打开订阅，然后选择“访问控制(IAM)”。
2. 在“检查访问权限”中找到相关的帐户，然后单击它以查看权限。
3. 你应该拥有“参与者”或“所有者”权限。 
    - 如果你刚刚创建了免费的 Azure 帐户，那么你就是订阅的所有者。
    - 如果你不是订阅所有者，请让所有者分配该角色。


### <a name="assign-permissions-to-create-azure-ad-apps"></a>分配用于创建 Azure AD 应用的权限

可以使用以下方法之一为 Azure Migrate 分配权限，以便在注册设备期间创建 Azure AD 应用：

- 租户/全局管理员可为租户中的用户授予创建和注册 Azure AD 应用的权限。
- 租户/全局管理员可将“应用程序开发人员”角色（拥有权限）分配到帐户。

> [!NOTE]
> - 除上述权限外，应用对订阅没有任何其他访问权限。
> - 只有在注册新的设备时，你才需要这些权限。 设置设备后可以删除这些权限。


#### <a name="grant-account-permissions"></a>授予帐户权限

租户/全局管理员可按如下所述授予权限：

1. 在 Azure AD 中，租户/全局管理员应导航到“Azure Active Directory” > “用户” > “用户设置”  。
2. 管理员应将“应用注册”设置为“是” 。

    ![Azure AD 权限](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> 这是不受影响的默认设置。 [了解详细信息](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)。



#### <a name="assign-application-developer-role"></a>分配“应用程序开发人员”角色

租户/全局管理员可将“应用程序开发人员”角色分配到帐户。 [了解详细信息](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

### <a name="assign-azure-account-permissions"></a>分配 Azure 帐户权限

为“虚拟机参与者”角色分配帐户，以便你有权执行以下操作：

- 在所选资源组中创建 VM。
- 在所选虚拟网络中创建 VM。
- 写入 Azure 托管磁盘。 


### <a name="set-up-an-azure-network"></a>设置 Azure 网络

[设置 Azure 网络](../virtual-network/manage-virtual-network.md#create-a-virtual-network)。 本地计算机将复制到 Azure 托管磁盘。 在故障转移到 Azure 进行迁移时，将基于这些托管磁盘创建 Azure VM，并将其加入到你设置的 Azure 网络。


## <a name="prepare-for-assessment"></a>准备进行评估

可以手动或使用配置脚本为 VM 评估准备 Hyper-V。 准备步骤包括： 如果准备使用脚本，则会自动配置这些脚本。

**步骤** | **脚本** | **手动**
--- | --- | ---
**验证 Hyper-V 主机请求** | 脚本检查主机是否正在运行受支持的 Hyper-V 版本，并检查 Hyper-V 角色。<br/><br/> 启用 WinRM 服务，并在主机上打开端口 5985 (HTTP) 和 5986 (HTTPS)（收集元数据时需要使用这些端口）。 | 验证服务器评估的 [Hyper-V 主机要求](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)。<br/><br/> 确保 Hyper-V 主机上已打开[所需的端口](migrate-support-matrix-hyper-v.md#port-access)。
**验证 PowerShell 版本** | 检查你是否在受支持的 PowerShell 版本中运行该脚本。 | 检查是否正在 Hyper-V 主机上运行 PowerShell 版本 4.0 或更高版本。
**创建帐户** | 验证你（运行脚本的用户）是否对 Hyper-V 主机拥有管理特权。<br/><br/>  允许你创建一个本地用户帐户（非管理员），供 Azure Migrate 服务用来与 Hyper-V 主机通信。 此用户帐户将添加到主机上的以下组：<br/><br/> - 远程管理用户<br/><br/> - Hyper-V 管理员<br/><br/>- 性能监视器用户 | 在 Hyper-V 主机/群集上设置拥有管理员权限的域或本地用户帐户。<br/><br/> - 对于要包含在发现中的所有主机和群集，都需要设置一个帐户。<br/><br/> - 该帐户可以是本地帐户或域帐户。 我们建议为该帐户分配 Hyper-V 主机或群集的管理员权限。<br/><br/> 或者，如果你不想要分配管理员权限，则需要分配以下权限：远程管理用户；Hyper-V 管理员；性能监视器用户。
**启用 PowerShell 远程处理** | 在每台主机上启用 PowerShell 远程控制，使 Azure Migrate 设备能够通过 WinRM 连接在主机上运行 PowerShell 命令。| 要设置，请在每台主机上，以管理员身份打开 PowerShell 控制台，然后运行以下命令：<br/><br/>``` Enable-PSRemoting -force ```
**设置 Hyper-V 集成服务** | 检查主机管理的所有 VM 上是否已启用 Hyper-V Integration Services。 |  在每个 VM 上[启用 Hyper-V 集成服务](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)。<br/><br/> 如果运行的是 Windows Server 2003，请[遵循这些说明](prepare-windows-server-2003-migration.md)。
**如果 VM 磁盘位于远程 SMB 共享上，则委派凭据** | 脚本委托凭据。 | [启用 CredSSP](#enable-credssp-to-delegate-credentials) 以委托凭据。

### <a name="run-the-script"></a>运行脚本

按如下所示运行脚本：

1. 确保在 Hyper-V 主机上安装了 PowerShell 4.0 或更高版本。
2. 从 [Microsoft 下载中心](https://aka.ms/migrate/script/hyperv)下载脚本。 该脚本已由 Microsoft 加密签名。
3. 使用 MD5 或 SHA256 哈希文件验证脚本完整性。 井号标签值如下。 运行以下命令生成脚本的哈希：
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    用法示例：
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. 验证脚本完整性后，在每台 Hyper-V 主机上结合以下 PowerShell 命令运行该脚本：
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>井号标签值

哈希值为:

| **哈希** | **值** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>启用 CredSSP 以委托凭据

如果主机上的 VM 包含位于 SMB 共享上的磁盘，请在主机上完成此步骤。

- 可在所有 Hyper-v 主机上远程运行此命令。
- 如果在群集中添加了新的主机节点，则会自动添加这些节点以供发现，但需要根据情况在新节点上手动启用 CredSSP。

按如下所示启用 CredSSP：

1. 识别运行包含 SMB 共享中的磁盘的 Hyper-V VM 的 Hyper-V 主机。
2. 在识别到的每台 Hyper-V 主机上运行以下命令：

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

设置设备时，[在设备上启用 CredSSP](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds) 即可完成 CredSSP 的设置。 本教程系列的下一篇文章对此做了介绍。


## <a name="prepare-for-appliance-deployment"></a>准备进行设备部署

在设置 Azure Migrate 设备并在下一篇教程中开始评估之前，需要准备好设备部署。

1. [验证](migrate-appliance.md#appliance---hyper-v)设备要求。
2. 查看设备在[公有云](migrate-appliance.md#public-cloud-urls)和[政府云](migrate-appliance.md#government-cloud-urls)中需要访问的 Azure URL。 如果使用的是基于 URL 的防火墙或代理，请确保它允许访问所需的 URL。
3. 查看设备在发现和评估期间要收集的数据。
4. [查看](migrate-appliance.md#collected-data---hyper-v)设备的端口访问要求。


## <a name="prepare-for-hyper-v-migration"></a>准备 Hyper-V 迁移

1. [查看](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) Hyper-V 主机迁移要求，以及迁移 VM 时 Hyper-V 主机和群集需要访问的 Azure URL。
2. [查看](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)要迁移到 Azure 的 Hyper-V VM 要求。
3. 在将 VM 迁移到 Azure 之前，需要在 VM 上进行一些更改。
    - 在开始迁移之前，必须做出这些更改。 如果在做出更改之前迁移 VM，VM 可能无法在 Azure 中启动。
    - 查看在 [Windows](prepare-for-migration.md#windows-machines) 和 [Linux](prepare-for-migration.md#linux-machines) 上需要进行的更改。



## <a name="next-steps"></a>后续步骤

本教程介绍以下操作：

> [!div class="checklist"]
> * 设置 Azure 帐户权限。
> * 准备要评估和迁移的 Hyper-V 主机与 VM。
> * 准备了 Azure Migrate 设备部署。

继续阅读下一篇教程，以创建 Azure Migrate 项目、部署设备，并发现和评估要迁移到 Azure 的 Hyper-V VM。

> [!div class="nextstepaction"]
> [评估 Hyper-V VM](./tutorial-assess-hyper-v.md)
