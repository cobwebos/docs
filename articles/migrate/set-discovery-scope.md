---
title: 将 VMware VM 发现的作用域设置 Azure Migrate
description: 描述如何在 Azure Migrate 中设置 VMware VM 评估和迁移的发现作用域。
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337631"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>为 VMware Vm 设置发现作用域

本文介绍如何限制[Azure Migrate 设备](migrate-appliance-architecture.md)发现的 VMware vm 的发现范围。

当你执行以下操作时，Azure Migrate 设备将发现本地 VMware Vm： 

- 使用[Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具评估要迁移到 Azure 的 VMware vm。
- 使用[Azure Migrate：服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具将 VMware vm[无代理迁移](server-migrate-overview.md)到 Azure。

## <a name="set-discovery-scope"></a>设置发现作用域


为 VMware Vm 评估或迁移设置 Azure Migrate 设备后，该设备将开始发现 Vm，并将 VM 元数据发送到 Azure。 在将设备连接到 vCenter Server 用于发现之前，你可以将发现作用域设置为将发现限制为 vCenter Server 数据中心、群集、群集文件夹、主机、主机的文件夹或单个 Vm。

若要设置作用域，你可以在 Azure Migrate 用于访问 vCenter Server 的帐户上分配权限。

## <a name="create-a-vcenter-user-account"></a>创建 vCenter 用户帐户

如果尚未设置 Azure Migrate 设备用于发现、评估和迁移 VMware Vm 的 vCenter 用户帐户，请先执行此操作。

1.    以 vCenter Server 管理员身份登录到 vSphere Web 客户端。
2.    选择 "**管理** > **SSO 用户和组**"，然后单击 "**用户**" 选项卡。
3.    选择“新建用户”图标。****
4.    填写新的用户信息 > **"确定"**。

帐户权限取决于要部署的内容。

**功能** | **帐户权限**
--- | ---
[评估](tutorial-assess-vmware.md)| 帐户需要只读访问权限。
[发现应用/角色/功能](how-to-discover-applications.md) | 帐户需要只读访问权限，并且对虚拟机 > 来宾操作启用了特权。
[分析依赖关系（无代理）](how-to-create-group-machine-dependencies-agentless.md) | 帐户需要只读访问权限，并且对虚拟机 > 来宾操作启用了特权。
[迁移（无代理）](tutorial-migrate-vmware.md) | 需要为角色分配正确的权限。<br/><br/> 若要创建角色，请以 vCenter Server 管理员身份登录到 vSphere Web 客户端。 选择 >**创建角色**的 vCenter Server 实例。 指定角色名称，例如<em>Azure_Migrate</em>，并将[所需的权限](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)分配给该角色。


## <a name="assign-permissions-on-vcenter-objects"></a>在 vCenter 对象上分配权限

您可以使用以下两种方法之一分配对库存对象的权限：

- 将具有所需权限的角色分配给托管要发现/迁移的 Vm 的所有父对象。
- 或者，你可以在数据中心级别分配角色和用户帐户，并将其传播到子对象。 然后，为不想发现/迁移的每个对象授予该帐户 "**无访问**" 角色。 我们不建议使用这种方法，因为这种方法很繁琐，可能会公开访问控制，因为每个新的子对象会自动授予从父对象继承的访问权限。

若要将角色分配给所有相关对象所使用的帐户，请执行以下操作：

- **对于评估**：对于 vm 评估，请将**只读**角色应用于要发现的 vm 的所有父对象的 vCenter 用户帐户。 包括的父对象包括层次结构中的主机、主机、群集和群集文件夹，直到数据中心。 将这些权限传播到层次结构中的子对象。

    ![分配权限](./media/tutorial-assess-vmware/assign-perms.png)

- **对于无代理迁移**：对于无代理迁移，对于托管要发现的 vm 的所有父对象，请将具有[所需权限](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)的用户定义角色应用于用户帐户。 可以<em>Azure_Migrate</em>命名角色。

### <a name="scope-support"></a>范围支持

目前，如果 vCenter 帐户有权在 vCenter VM 文件夹级别授予访问权限，则服务器评估工具无法发现 Vm。 支持主机和群集的文件夹。

如果你想要按 VM 文件夹确定发现的范围，请完成下一个过程，以确保 vCenter 帐户在 VM 级别分配了只读访问权限。

1. 为要确定其作用域的 VM 文件夹中的所有 Vm 分配只读权限。
2. 向托管 Vm 的所有父对象授予只读访问权限。
    - 层次结构中包含的所有父对象（主机、主机、群集、群集文件夹）都包含在数据中心。
    - 不需将权限传播到所有子对象。
3. 选择数据中心作为“收集范围”即可使用凭据进行发现。**** 基于角色的访问控制设置可确保相应的 vCenter 用户帐户仅有权访问特定于租户的 Vm。


## <a name="next-steps"></a>后续步骤

[设置设备](how-to-set-up-appliance-vmware.md)并[开始连续发现](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential)。
